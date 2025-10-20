# Flutter & Neovim (LazyVim) Installation Guide for Linux Mint

This guide provides detailed instructions for setting up a Flutter development environment on Linux Mint, including the Neovim editor with LazyVim configuration.

**Author:** Gemini
**Last Updated:** October 20, 2025

---

## Prerequisites Installation

First, ensure your system has the necessary build tools. We recommend using `apt` for a stable installation on Linux Mint.

Open a terminal and execute the following commands:

```bash
# Update system package list
sudo apt update && sudo apt upgrade -y

# Install required tools
sudo apt install curl git unzip clang cmake ninja-build pkg-config libgtk-3-dev liblzma-dev
```

## Step 2: Install Flutter SDK

We will install Flutter by cloning directly from Google's official repository.

### Download Flutter SDK

Create a directory for development tools and clone the Flutter repository:

```bash
# Create directory and navigate to it
mkdir -p ~/development
cd ~/development

# Clone the latest stable Flutter version
git clone https://github.com/flutter/flutter.git -b stable
```

### Configure PATH Environment Variable

This crucial step allows flutter commands to be executed from anywhere. The commands differ based on your shell.

**Important:** Choose the correct commands based on your shell (Zsh or Bash).

**For Zsh users (recommended):**

If using Zsh (~/.zshrc):

```bash
# Add Flutter path to zshrc configuration
echo 'export PATH="$PATH:$HOME/development/flutter/bin"' >> ~/.zshrc

# Apply changes immediately
source ~/.zshrc
```

**For Bash users (default):**

If using Bash (~/.bashrc):

```bash
# Add Flutter path to bashrc configuration
echo 'export PATH="$PATH:$HOME/development/flutter/bin"' >> ~/.bashrc

# Apply changes immediately
source ~/.bashrc
```

### Download Required Binaries

Execute flutter for the first time to automatically download Linux-specific binaries:

```bash
flutter precache
```

## Step 3: Run flutter doctor to Verify Installation

`flutter doctor` is Flutter's diagnostic tool that checks your environment setup.

```bash
flutter doctor -v
```

The initial results may show some issues, particularly with the Android toolchain. These will be addressed in the next step.

Expected initial output (may vary):
```
[✓] Flutter (Channel stable, 3.x.x, on Linux, locale en_US.UTF-8)
[!] Android toolchain - develop for Android devices (Android SDK not found)
[✓] Chrome - develop for the web
[✓] Linux toolchain - develop for Linux desktop
[!] Android Studio (not installed)
[✓] Connected device (1 available)
[✓] Network resources
```

## Step 4: Install Android Toolchain

To develop Android applications, Android SDK is required. The easiest way is through Android Studio.

### Download and Install Android Studio

1. Visit the Android Studio official website and download the .tar.gz file.

2. Extract to the ~/development/ directory:
```bash
tar -xvzf ~/Downloads/android-studio-*.tar.gz -C ~/development/
```

3. Launch Android Studio to complete initial setup:
```bash
cd ~/development/android-studio/bin
./studio.sh
```

In the Setup Wizard, select "Standard Installation" and follow the prompts to automatically download Android SDK and necessary components.

### Accept Android SDK Licenses

After Android Studio installation, run:
```bash
flutter doctor --android-licenses
```
Press 'y' to accept all license agreements.

### Re-run flutter doctor Verification

Execute flutter doctor again. The Android toolchain and Android Studio sections should now show green checkmarks.

```bash
flutter doctor
```

## Step 5: Integrate Flutter in Neovim (LazyVim)

LazyVim provides an "extras" system for easy language support addition.

### Enable Flutter Extra

Create a new file at `~/.config/nvim/lua/plugins/flutter.lua` with the following content:

```lua
-- ~/.config/nvim/lua/plugins/flutter.lua
return {
  "lazyvim.plugins.extras.lang.flutter",
}
```

### Restart Neovim

Launch Neovim. LazyVim will automatically detect and install required plugins, including LSP for Dart/Flutter and flutter-tools.nvim for features like hot reload, hot restart, and debugging.

## Step 6: Create and Run Your First Application

With everything configured, let's verify the setup.

### Create a New Project

```bash
cd ~/development
flutter create my_awesome_app
```

### Open Project in Neovim

```bash
cd my_awesome_app
nvim .
```

### Run the Application

1. Connect a physical Android device or start an emulator from Android Studio.

2. In Neovim, use flutter-tools.nvim commands (e.g., press `<leader>fk` to select device, `<leader>fr` to run).

3. Alternatively, open a separate terminal and run:
```bash
flutter run
```