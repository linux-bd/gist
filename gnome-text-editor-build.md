
### **How to Build GNOME Text Editor from Source on Ubuntu (Local Install)**

#### 1. Install Required Dependencies

```bash
sudo apt update
sudo apt install -y \
    meson ninja-build git \
    libgtk-4-dev libadwaita-1-dev \
    libgtksourceview-5-dev libenchant-2-dev \
    libgirepository1.0-dev valac \
    gi-docgen itstool gettext \
    libeditorconfig-dev libxml2-utils \
    pkg-config
```

#### 2. Clone the Repository

```bash
cd ~/dev
git clone https://github.com/GNOME/gnome-text-editor.git --depth=1
cd gnome-text-editor
```

#### 3. Build & Install Locally

```bash
# Configure
meson setup build --prefix=$(pwd)/_install

# Build
ninja -C build

# Install
ninja -C build install
```

#### 4. Create Run Script (`run-editor.sh`)

```bash
cat > run-editor.sh << 'EOF'
#!/bin/bash
ROOT=$(pwd)

export LD_LIBRARY_PATH="${ROOT}/_install/lib/x86_64-linux-gnu:${LD_LIBRARY_PATH}"
export PATH="${ROOT}/_install/bin:${PATH}"

exec ./_install/bin/gnome-text-editor "$@"
EOF

chmod +x run-editor.sh
```

#### 5. Run GNOME Text Editor

```bash
./run-editor.sh
```

---

**Alternative quick run (without install):**
```bash
ninja -C build run
```

**For future updates:**
```bash
git pull
meson setup build --reconfigure
ninja -C build
ninja -C build install
./run-editor.sh
```
