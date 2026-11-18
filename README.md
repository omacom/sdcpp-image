# sdcpp-image

The container image [omarchy-local-ai](https://github.com/omacom/omarchy-local-ai) runs image models in: [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp)'s `sd-server`, built with Vulkan for AMD GPUs, published as `ghcr.io/omacom/sdcpp`.

It builds `sd-server` from a pinned upstream commit with the patches in [`patches/`](patches), both open as upstream pull requests:

- [`0001`](patches/0001-server-add-api-key-and-api-key-file.patch): `--api-key`, `--api-key-file` and `SD_API_KEY`, so every request needs `Authorization: Bearer <key>`.
- [`0002`](patches/0002-server-stop-on-sigint-and-sigterm.patch): a clean stop on SIGINT and SIGTERM, which a container's first process otherwise ignores.

The runtime layer is Arch Linux, pinned by digest, with the Vulkan loader, Mesa's RADV driver and curl for the health probe. The licenses of stable-diffusion.cpp, ggml and the third-party code built into `sd-server` are in `/usr/share/licenses/stable-diffusion.cpp`.

## Building and publishing

```sh
podman build -t ghcr.io/omacom/sdcpp:<commit>-<n> .
podman push ghcr.io/omacom/sdcpp:<commit>-<n>
```

omarchy-local-ai pins the image by digest in `runtimes/sdcpp.toml`, with a review of the build in `reviews/`. Drop a patch once upstream has it and the pinned commit includes it.

## License

The build files here are MIT. What the image contains keeps its own licenses, listed above.
