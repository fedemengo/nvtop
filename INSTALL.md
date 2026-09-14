# Installing this fork

This fork is not packaged by any distribution. There are exactly two ways to
get it: download the prebuilt binary from the releases page, or build it from
source. Distribution packages (`dnf install nvtop`, `apt install nvtop`, …)
install [upstream nvtop](https://github.com/Syllo/nvtop) and will **not** have
the changes in this fork.

## Prebuilt binary (x86_64 Linux)

```bash
curl -fL -o ~/.local/bin/nvtop https://github.com/fedemengo/nvtop/releases/latest/download/nvtop-linux-x86_64
chmod +x ~/.local/bin/nvtop
```

`~/.local/bin` has to be on your `PATH`. If a distribution package is also
installed, check which one you get with `command -v nvtop`.

Releases are cut by hand, so the binary can lag `master`. The release notes
say which commit it was built from; build from source to get anything newer.

## From source

Build dependencies: CMake, a C compiler, ncurses (wide-character build),
libdrm and libsystemd or libudev.

```bash
# Fedora / RHEL
sudo dnf install cmake gcc ncurses-devel libdrm-devel systemd-devel
# Debian / Ubuntu
sudo apt install cmake gcc libncurses5-dev libncursesw5-dev libdrm-dev libsystemd-dev
```

```bash
git clone https://github.com/fedemengo/nvtop.git && cd nvtop
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="$HOME/.local"
cmake --build build -j"$(nproc)"
cmake --install build
```

That installs to `~/.local/bin/nvtop` without privileges. Drop
`-DCMAKE_INSTALL_PREFIX` and use `sudo cmake --install build` to install system
wide instead.

Support for each GPU vendor is enabled only when its dependencies are found.
The vendor flags (`-DNVIDIA_SUPPORT=ON`, `-DAMDGPU_SUPPORT=ON`,
`-DINTEL_SUPPORT=ON`, …) are listed in `CMakeLists.txt`; turn off the ones you
do not need to skip their dependencies. AMD, Intel and V3D support require
libsystemd or libudev and the configure step fails without them, so an
NVIDIA-only machine can build with:

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="$HOME/.local" \
  -DNVIDIA_SUPPORT=ON -DAMDGPU_SUPPORT=OFF -DRADEON_SUPPORT=OFF \
  -DINTEL_SUPPORT=OFF -DV3D_SUPPORT=OFF
```

To uninstall: `cmake --build build --target uninstall`.

See the [upstream README](README.markdown) for per-vendor requirements, the
Docker image and WSL2 notes.
