# Claudia Installation Process for WSL

This guide provides step-by-step instructions for installing Claudia on Windows Subsystem for Linux (WSL).

## Prerequisites

- Windows 10/11 with WSL2 installed
- WSL Ubuntu distribution (recommended)
- Administrative privileges on Windows

## Installation Steps

### 1. Enter WSL Environment

**Open Windows Terminal or Command Prompt:**
```cmd
wsl
```

You should now see a Linux prompt (e.g., `username@hostname:~$`).

### 2. Install System Dependencies

**Update package list:**
```bash
sudo apt update
```

**Install required system packages:**
```bash
sudo apt install -y \
  libwebkit2gtk-4.1-dev \
  libgtk-3-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev \
  patchelf \
  build-essential \
  curl \
  wget \
  file \
  libssl-dev \
  libxdo-dev \
  libsoup-3.0-dev \
  libjavascriptcoregtk-4.1-dev \
  git
```

### 3. Install Rust

**Install Rust toolchain:**
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

**Reload environment:**
```bash
source ~/.cargo/env
```

**Verify installation:**
```bash
rustc --version
```

### 4. Install Bun

**Install Bun package manager:**
```bash
curl -fsSL https://bun.sh/install | bash
```

**Reload environment:**
```bash
source ~/.bashrc
```

**Verify installation:**
```bash
bun --version
```

### 5. Install Claude Code CLI

**Visit Claude Code website and follow installation instructions:**
- Go to: https://claude.ai/code
- Follow the installation process for your system

**Verify installation:**
```bash
claude --version
```

### 6. Setup X11 Server (for GUI)

**On Windows (outside WSL):**
1. Download VcXsrv from: https://sourceforge.net/projects/vcxsrv/
2. Install VcXsrv
3. Run XLaunch from Start Menu
4. Choose "Multiple windows" → Next
5. Choose "Start no client" → Next
6. **Important**: Check "Disable access control" → Next
7. Click "Finish"

**Back in WSL, configure X11 forwarding:**
```bash
echo 'export DISPLAY=$(ip route list default | awk '\''{print $3}'\''):0' >> ~/.bashrc
source ~/.bashrc
```

**Test X11 forwarding:**
```bash
sudo apt install x11-apps
xeyes
```

If a window with moving eyes appears, X11 is working correctly.

### 7. Clone and Build Claudia

**Clone the repository:**
```bash
git clone https://github.com/getAsterisk/claudia.git
cd claudia
```

**Install Node.js dependencies:**
```bash
bun install
```

**Build the application:**
```bash
bun run tauri build
```

### 8. Run Claudia

**Option 1: Development mode (with hot reload):**
```bash
bun run tauri dev
```

**Option 2: Production build:**
```bash
./src-tauri/target/release/claudia
```

## Troubleshooting

### Common Issues and Solutions

**Build fails with "out of memory" error:**
```bash
cargo build -j 2
```

**X11 display errors:**
```bash
export DISPLAY=:0
# Or try:
export DISPLAY=$(ip route list default | awk '{print $3}'):0
```

**Webkit2gtk not found:**
```bash
sudo apt install libwebkit2gtk-4.0-dev
```

**MSVC not found (should not happen in WSL):**
```bash
# This indicates you're running in Windows instead of WSL
# Make sure you're in WSL environment
```

**Claude command not found:**
```bash
# Make sure Claude Code CLI is properly installed
# Check if it's in your PATH
which claude
```

### Verification Commands

**Check all installations:**
```bash
# Rust
rustc --version

# Bun
bun --version

# Claude Code
claude --version

# Git
git --version

# X11 (should show moving eyes)
xeyes
```

## Optional: Create Desktop Launcher

**Create a launch script:**
```bash
cat > ~/claudia-launcher.sh << 'EOF'
#!/bin/bash
cd ~/claudia
export DISPLAY=$(ip route list default | awk '{print $3}'):0
./src-tauri/target/release/claudia
EOF

chmod +x ~/claudia-launcher.sh
```

**Run Claudia anytime:**
```bash
~/claudia-launcher.sh
```

## Development Commands

**Start development server:**
```bash
bun run tauri dev
```

**Build for production:**
```bash
bun run tauri build
```

**Run tests:**
```bash
# Frontend type checking
bunx tsc --noEmit

# Rust tests
cd src-tauri && cargo test
```

**Format code:**
```bash
# Rust
cd src-tauri && cargo fmt
```

## Notes

- Keep VcXsrv running whenever you want to use Claudia's GUI
- The first build may take 10-15 minutes due to Rust compilation
- Subsequent builds will be faster due to caching
- All data is stored locally in `~/.claude/` directory
- No telemetry or data collection is performed

## Support

If you encounter issues:
1. Check the troubleshooting section above
2. Verify all prerequisites are installed
3. Ensure VcXsrv is running with access control disabled
4. Report issues at: https://github.com/getAsterisk/claudia/issues