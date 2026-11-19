> **Archived.** This image is now built from [`images/sdcpp`](https://github.com/omacom/omarchy-local-ai/tree/master/images/sdcpp) and [`images/sdcpp-cuda`](https://github.com/omacom/omarchy-local-ai/tree/master/images/sdcpp-cuda) in [omacom/omarchy-local-ai](https://github.com/omacom/omarchy-local-ai) and published as `ghcr.io/omacom/omarchy-local-ai/…`. Images already published from here stay available.

# sdcpp-image

The container image [omarchy-local-ai](https://github.com/omacom/omarchy-local-ai) runs image models in: [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp)'s `sd-server`, built with Vulkan for AMD GPUs, published as `ghcr.io/omacom/sdcpp`.

It builds `sd-server` from a pinned upstream commit with the patches in [`patches/`](patches), both open as upstream pull requests:

- [`0001`](patches/0001-server-add-api-key-and-api-key-file.patch): `--api-key`, `--api-key-file` and `SD_API_KEY`, so every request needs `Authorization: Bearer <key>`.
- [`0002`](patches/0002-server-stop-on-sigint-and-sigterm.patch): a clean stop on SIGINT and SIGTERM, which a container's first process otherwise ignores.

The runtime layer is Arch Linux, pinned by digest, with the Vulkan loader, Mesa's RADV driver and curl for the health probe. The licenses of stable-diffusion.cpp, ggml and the third-party code built into `sd-server` are in `/usr/share/licenses/stable-diffusion.cpp`.

## CUDA

[`Containerfile.cuda`](Containerfile.cuda) builds the same commit and patches with CUDA for NVIDIA's unified-memory ARM machines (GB10, N1x, compute capability 12.1), published as `ghcr.io/omacom/sdcpp-cuda`. It is built on one of those machines, on NVIDIA's CUDA development image pinned by digest, and its runtime layer is Ubuntu 24.04 with only the CUDA libraries `sd-server` links (cudart, cuBLAS and cuBLASLt, redistributable under NVIDIA's CUDA EULA, whose text is in `/usr/share/licenses/stable-diffusion.cpp/nvidia`). The driver's `libcuda` arrives through the machine's CDI spec.

## Building and publishing

```sh
podman build -t ghcr.io/omacom/sdcpp:<commit>-<n> .
podman push ghcr.io/omacom/sdcpp:<commit>-<n>

podman build -f Containerfile.cuda -t ghcr.io/omacom/sdcpp-cuda:<commit>-<n> .
podman push ghcr.io/omacom/sdcpp-cuda:<commit>-<n>
```

omarchy-local-ai pins the images by digest in `runtimes/sdcpp.toml` and `runtimes/sdcpp-cuda.toml`, with a review of each build in `reviews/`. Drop a patch once upstream has it and the pinned commit includes it.

## License

The build files here are MIT. What the image contains keeps its own licenses, listed above.
