#!/usr/bin/env bash

# setup-wsl-dev-env.sh
# Prepares a fresh WSL Debian installation for development with Node.js, npm, npx, Python, pip, venv, and PATH hygiene.

set -e  # Exit immediately on error
set -u  # Treat unset variables as errors
set -o pipefail

echo "🔧 Starting WSL Debian development environment setup..."

# Optional: Remove Windows Node.js from PATH in current shell session
echo "🧹 Removing Windows Node.js from PATH (current session only)..."
export PATH=$(echo $PATH | tr ':' '\n' | grep -v '/mnt/c/Program Files/nodejs' | paste -sd ':' -)

# Update apt repositories
echo "📦 Updating apt repositories..."
sudo apt update

# Install prerequisites
echo "📦 Installing prerequisites: curl, software-properties-common..."
sudo apt install -y curl software-properties-common

# Install Node.js from NodeSource
echo "📦 Installing Node.js LTS from NodeSource..."
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Verify Node.js installation
echo "🟢 Node.js version: $(node -v)"
echo "📦 npm version: $(npm -v)"
echo "📦 npx version: $(npx -v)"

# Install Python 3, ensure 'python' points to 'python3'
echo "📦 Installing Python 3 and python-is-python3..."
sudo apt install -y python3 python-is-python3

# Install pip for Python 3
echo "📦 Installing python3-pip..."
sudo apt install -y python3-pip

# Verify Python and pip installation
echo "🐍 Python version: $(python --version)"
echo "📦 pip version: $(pip --version)"

# Install python3-venv for virtual environments
echo "📦 Installing python3-venv..."
sudo apt install -y python3-venv

# Create a virtual environment named 'venv' if it doesn't exist
if [ ! -d "venv" ]; then
    echo "🛠️ Creating Python virtual environment in ./venv..."
    python -m venv venv
else
    echo "✅ Virtual environment 'venv' already exists, skipping creation."
fi

# Activate virtual environment
echo "💻 Activating virtual environment..."
source venv/bin/activate

# Upgrade pip within venv
echo "⬆️ Upgrading pip in virtual environment..."
pip install --upgrade pip

# Display final tool versions within venv
echo "✅ Final tool versions:"
echo "Python: $(python --version)"
echo "pip: $(pip --version)"
echo "Node.js: $(node -v)"
echo "npm: $(npm -v)"
echo "npx: $(npx -v)"

echo "🎉 WSL Debian development environment setup complete."

