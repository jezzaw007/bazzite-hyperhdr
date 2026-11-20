### 1. bazzite-hyperhdr

```markdown

Run [HyperHDR](https://hyperhdr.com) on [Bazzite](https://bazzite.gg) via a single Podman Compose file — no `rpm-ostree` layering required.

## Why?

Bazzite is an immutable (atomic) system, so installing HyperHDR natively isn't straightforward. Pre-built HyperHDR container images target USB/LED devices and **lack the PipeWire libraries** needed for Wayland screen capture. This repo solves that with a self-contained Compose file that builds an Ubuntu container with all the right dependencies baked in.

## Prerequisites

- **Bazzite** (any desktop variant, running in Desktop Mode)
- **Podman** — pre-installed
- **Homebrew** — pre-installed, used to install `podman-compose`

## Deployment

### 1. Install `podman-compose`

```bash
brew install podman-compose
```

### 2. Clone this repository

```bash
git clone https://github.com/YOUR_USERNAME/bazzite-hyperhdr.git
cd bazzite-hyperhdr
```

### 3. Edit the Compose file

Replace every occurrence of `user` in the volume paths with your Bazzite username:

```bash
whoami          # confirm your username
nano compose.yaml
```

You'll see lines like:

```yaml
volumes:
  - /home/user/hyperhdr:/config
```

Change `user` → your actual username.

### 4. Build and start

```bash
podman-compose up -d
```

The first run pulls the Ubuntu base image and installs HyperHDR + PipeWire dependencies. Subsequent starts are fast.

### 5. Grant screen-capture permission

On first launch, KDE will prompt you to allow screen capture for the container.

1. Open **System Settings → Security & Privacy → Application Permissions → Screen Capture**
2. Find the **HyperHDR** (or container) entry and allow it
3. If the prompt never appeared, restart the container:
   ```bash
   podman-compose restart
   ```

### 6. Open the Web UI

```
http://localhost:8090
```

Check the logs for credentials or startup errors:

```bash
podman-compose logs -f
```

## Day-to-day

| Action | Command |
|---|---|
| Start | `podman-compose up -d` |
| Stop | `podman-compose down` |
| Restart | `podman-compose restart` |
| View logs | `podman-compose logs -f` |
| Update HyperHDR | `podman-compose down && podman-compose up -d --build` |
| **Destroy config** | `podman-compose down -v` ⚠️ |

## Troubleshooting

| Symptom | Fix |
|---|---|
| No screen capture / black preview | Verify KDE screen-capture permission (step 5 above); confirm you're in a **Wayland** session, not X11 |
| `podman-compose: command not found` | `brew install podman-compose` |
| `permission denied` on config volume | Ensure the volume path in `compose.yaml` matches your actual `$HOME` directory |
| Port 8090 already in use | Change the port mapping in the compose file, e.g. `"8091:8090"` |
| LED strip not detected | Add the USB device to the service, e.g. `devices: - /dev/bus/usb/001/002` (find it with `lsusb`) |

## Notes

- The container runs as your user (not root) so config files on the host volume keep correct ownership.
- PipeWire screen capture requires a **Wayland** session — Bazzite's default. It will not work under X11.
- If you add USB LED devices later, you may also need `--security-opt label=disable` (or the compose equivalent `security_opt`) for udev access.
```

**Key changes from your original:**

- **Added a "Why?" section** so visitors immediately understand the problem it solves (immutable OS + missing PipeWire in prebuilt images).
- **Structured deployment as numbered steps** — install tool → clone → edit → build → permission → verify.
- **Added a day-to-day command table** so you don't have to re-derive `podman-compose` flags.
- **Added a troubleshooting table** covering the most common failure modes (screen capture permission, missing brew package, volume paths, USB passthrough).
- **Noted the Wayland requirement** explicitly, since X11 users will hit a dead end.
- **Flagged the destructive `-v` flag** so nobody accidentally nukes their config.

Adjust the `YOUR_USERNAME` placeholder and the volume path example to match your actual compose file. If your compose file uses a `build:` section with a `Containerfile` in the repo, you can add a short "How it works" section describing the build stages.

