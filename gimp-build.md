## How to Build GIMP 3.3 from Source Locally on Ubuntu (Clean & Reliable Method

### 1. Install Dependencies (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install -y \
    meson ninja-build git \
    libgtk-3-dev libbabl-dev libgegl-dev \
    libgexiv2-dev libexiv2-dev librsvg2-dev \
    libtiff-dev libpoppler-glib-dev libarchive-dev \
    libmypaint-dev mypaint-brushes libxmu-dev \
    xsltproc libatk1.0-dev cmake \
    liblcms2-dev libjson-glib-dev libwebp-dev \
    libheif-dev libjpeg-dev libpng-dev
```

### 2. Clone GIMP

```bash
cd ~/dev
git clone https://github.com/GNOME/gimp.git --depth=1
git submodule update --init --recursive
cd gimp
```

### 3. Build babl and gegl (Very Important)

```bash
cd ~/dev

git clone https://gitlab.gnome.org/GNOME/babl.git
git clone https://gitlab.gnome.org/GNOME/gegl.git

# Build babl
cd ~/dev/babl
meson setup _build --prefix=$HOME/dev/gimp/_install --reconfigure
ninja -C _build install

# Build gegl
cd ~/dev/gegl
meson setup _build --prefix=$HOME/dev/gimp/_install --reconfigure
ninja -C _build install
```

### 4. Build and Install GIMP locally

```bash
cd ~/dev/gimp

# Configure with local install prefix
meson setup build --prefix=$(pwd)/_install --reconfigure

# Build
ninja -C build

# Install
ninja -C build install
```

### 5. Create Run Script (`run-gimp.sh`)

```bash
cat > run-gimp.sh << 'EOF'
#!/bin/bash
ROOT=$(pwd)

export LD_LIBRARY_PATH="${ROOT}/_install/lib/x86_64-linux-gnu:${LD_LIBRARY_PATH}"
export PATH="${ROOT}/_install/bin:${PATH}"

export GIMP3_DATADIR="${ROOT}/_install/share/gimp/3.0"
export GIMP3_SYSCONFDIR="${ROOT}/_install/etc/gimp/3.0"
export GIMP3_LOCALEDIR="${ROOT}/_install/share/locale"

exec ./_install/bin/gimp-3.3 "$@"
EOF

chmod +x run-gimp.sh
```

### 6. Run GIMP

```bash
./run-gimp.sh
```

---

### Future Updates (Quick Commands)

```bash
cd ~/dev/gimp
git pull
cd ~/dev/babl && ninja -C _build install
cd ~/dev/gegl && ninja -C _build install
cd ~/dev/gimp
meson setup build --reconfigure
ninja -C build
ninja -C build install
./run-gimp.sh
```

---

**This method gives you:**
- A clean local install in `_install` folder
- No system pollution
- Easy to update
- Works reliably for development
