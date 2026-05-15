## How to Build and Install GIMP 3.3 from Source System-Wide on Ubuntu

### 1. Install Dependencies

```bash
sudo apt update
sudo apt install -y meson ninja-build git cmake \
    libgtk-3-dev libbabl-dev libgegl-dev libgexiv2-dev \
    librsvg2-dev libtiff-dev libpoppler-glib-dev libarchive-dev \
    libmypaint-dev mypaint-brushes libxmu-dev xsltproc \
    liblcms2-dev libjson-glib-dev libwebp-dev libheif-dev
```

### 2. Clone babl, gegl & gimp

```bash
cd ~/dev

git clone https://gitlab.gnome.org/GNOME/babl.git
git clone https://gitlab.gnome.org/GNOME/gegl.git
git clone https://github.com/GNOME/gimp.git --depth=1
git submodule update --init --recursive

cd gimp
```

### 3. Build & Install babl + gegl System-Wide

```bash
# babl
cd ~/dev/babl
meson setup _build --prefix=/usr/local --reconfigure
ninja -C _build
sudo ninja -C _build install

# gegl
cd ~/dev/gegl
meson setup _build --prefix=/usr/local --reconfigure
ninja -C _build
sudo ninja -C _build install
```

### 4. Build & Install GIMP System-Wide

```bash
cd ~/dev/gimp

meson setup build --prefix=/usr/local --reconfigure
ninja -C build
sudo ninja -C build install
```

### 5. Final Step

```bash
sudo ldconfig
gimp-3.3
```

---

### Useful Commands

- **Run GIMP**: `gimp-3.3`
- **Check version**: `gimp-3.3 --version`
- **Update later** (when you pull new code):

```bash
cd ~/dev/babl && ninja -C _build && sudo ninja -C _build install
cd ~/dev/gegl && ninja -C _build && sudo ninja -C _build install
cd ~/dev/gimp
git pull
meson setup build --reconfigure
ninja -C build
sudo ninja -C build install
sudo ldconfig
gimp-3.3
```

---

This method installs GIMP into `/usr/local/bin` and `/usr/local/lib`, making it available system-wide like a normal application.
