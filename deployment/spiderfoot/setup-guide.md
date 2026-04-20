# Documentation: Containerized OSINT Environment (SpiderFoot)
> 📝 **Read the full walkthrough on DEV.to:**  
> [How I got out of dependency hell with Docker (SpiderFoot OSINT lab)](https://dev.to/muzasio/how-i-got-out-of-dependency-hell-with-docker-spiderfoot-osint-lab-5e6d)

## 🎯 Objective

A persistent, isolated OSINT reconnaissance environment using SpiderFoot. The primary goal was to bypass local host dependency conflicts while streamlining the management workflow for long-term lab use.

## ⚠️ Technical Challenge: "Dependency Hell"

During the initial installation attempt via a Python virtual environment (`venv`), the `pip install` phase failed consistently.

- **Bottleneck:** Binary compilation errors for `lxml`.
- **Root Cause:** A common issue on rolling-release distributions (CachyOS/Arch Linux) where system library versions (headers) drift from the pinned requirements in a tool's `requirements.txt`.
- **Decision:** Instead of manually troubleshooting individual binary headers on the host, I pivoted to a **containerized deployment** to ensure environment parity and maintain system cleanliness.

## 🛠️ Solution: Containerized Pivot

By utilizing **Docker**, the SpiderFoot environment is completely abstracted from the host system. This ensures stability, eliminates "it works on my machine" issues, and allows for seamless integration with IDE-based management tools.

### 1. Permission Configuration

Configured the Docker daemon to allow socket access for the current user, adhering to best practices by avoiding unnecessary `sudo` usage for daily operations.

```bash
# Add user to docker group
sudo usermod -aG docker $USER


# Apply group changes without logout
newgrp docker

# Restart service to ensure clean state
sudo systemctl restart docker
```

### 2. Build & Execution

Built the image directly from the source repository...

```bash
# Clone the official repository
git clone https://github.com/smicallef/spiderfoot.git
cd spiderfoot

# Build local image from source
docker build -t spiderfoot .

# Deploy detached container with port mapping
docker run -d -p 5001:5001 --name spiderfoot-app spiderfoot
```

- UI Access: The reconnaissance interface is accessible via Firefox at http://localhost:5001.
- IDE Integration: Managed via the VS Code Docker Extension.
- Benefit: Provides a high-level GUI for monitoring logs, starting/stopping the service, and inspecting container health without repetitive CLI context-switching.
