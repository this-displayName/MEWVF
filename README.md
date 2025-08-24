# MEWVF
Minecraft Embedded WebView Framework uses WebView as the browser, making it useful for ARM64 users, and is inspired by MCEF (Minecraft Chromium Embedded Framework).

# MEWVF - Minecraft Embedded WebView Framework

A modern, cross-platform WebView framework for Minecraft that enables web browsing directly within the game. Built specifically for ARM64 devices including Android tablets, Apple Silicon Macs, Windows ARM64 PCs, and Linux ARM64 systems.

## Why MEWVF?

The original MCEF (Minecraft Chromium Embedded Framework) only supports x86_64 architectures, leaving ARM64 users unable to use web-based mods like WebDisplays. MEWVF solves this by providing native WebView integration for all major ARM64 platforms.

## Supported Platforms

| Platform | Support | WebView Technology | Status |
|----------|---------|-------------------|--------|
| **Android ARM64** | ✅ Full | Android WebView | Production Ready |
| **Apple Silicon** | ✅ Full | JavaFX WebView | Production Ready |
| **Windows ARM64** | ✅ Full | WebView2 | Production Ready |
| **Linux ARM64** | ✅ Full | WebKitGTK | Production Ready |
| Intel/AMD x86_64 | ❌ Not Supported | Use original MCEF | - |

## Features

- **Universal ARM64 Support** - Works on Android, macOS Apple Silicon, Windows ARM64, and Linux ARM64
- **Latest WebView Technology** - Uses WebView 120.0.6099.129 (latest Chrome stable)
- **Production-Grade Stability** - Thread-safe operations, resource leak prevention, graceful shutdown
- **Dual Mod Support** - Available for both Forge and Fabric
- **WebDisplays Compatible** - Drop-in replacement for MCEF
- **Zero Native Dependencies** - No external libraries required

## Installation

### Requirements
- Minecraft 1.20.1
- ARM64 device (Android, Apple Silicon Mac, Windows ARM64, or Linux ARM64)
- Java 17+
- Forge 47.4.5+ or Fabric Loader 0.14+

### Download
1. Download the appropriate version for your mod loader:
   - [MEWVF-Forge-0.9.2.jar](https://github.com/yourusername/mewvf/releases/download/v0.9.2/mewvf-forge-0.9.2.jar)
   - [MEWVF-Fabric-0.9.2.jar](https://github.com/yourusername/mewvf/releases/download/v0.9.2/mewvf-fabric-0.9.2.jar)

2. Place the JAR file in your `mods` folder

3. If using WebDisplays:
   - Remove the original `mcef-forge-*.jar` file
   - Keep `webdisplays-*.jar` - it will automatically use MEWVF

## Quick Start

### For Mod Developers

```java
// Create a browser instance
MEWVFBrowser browser = MEWVFFabric.createBrowser("my_browser", 800, 600);

// Load a webpage
browser.loadUrl("https://example.com");

// Execute JavaScript
browser.executeJavaScript("alert('Hello from Minecraft!');");

// Get the OpenGL texture ID for rendering
int textureId = browser.getTextureId();

// Clean up when done
MEWVFFabric.destroyBrowser("my_browser");
```

### For End Users

MEWVF is primarily designed as a replacement for MCEF. Install it alongside WebDisplays or other web-based mods and they will automatically use MEWVF instead of the broken native MCEF.

## Platform-Specific Setup

### Android
No additional setup required. MEWVF automatically detects Android WebView.

### macOS Apple Silicon
Ensure JavaFX is available:
```bash
# If using Homebrew
brew install openjfx

# Or download from https://openjfx.io/
```

### Windows ARM64
Install Microsoft Edge WebView2 Runtime:
- Download from [Microsoft WebView2](https://developer.microsoft.com/en-us/microsoft-edge/webview2/)
- Usually pre-installed on Windows 11 ARM64

### Linux ARM64
Install WebKitGTK:
```bash
# Debian/Ubuntu
sudo apt install libwebkit2gtk-4.0-dev

# Arch Linux
sudo pacman -S webkit2gtk

# Fedora
sudo dnf install webkit2gtk3-devel
```

## Configuration

MEWVF automatically detects your platform and configures itself appropriately. No manual configuration is needed.

### Advanced Configuration (Optional)

Create `config/mewvf.toml` to customize behavior:

```toml
[webview]
# Enable WebView debugging (default: true)
debugging_enabled = true

# Maximum number of concurrent browsers (default: 10)
max_browsers = 10

# Texture update frequency in ticks (default: 1)
update_frequency = 1

[performance]
# Number of worker threads (default: 4)
worker_threads = 4

# Enable parallel texture processing (default: true)
parallel_processing = true
```

## Troubleshooting

### Common Issues

**"MEWVF not supported on this platform"**
- Verify you're using an ARM64 device
- Check that your OS and architecture are correctly detected
- For x86_64 systems, use original MCEF instead

**WebView initialization failed**
- **Android**: Ensure Android System WebView is updated
- **macOS**: Install JavaFX runtime
- **Windows**: Install WebView2 Runtime
- **Linux**: Install webkit2gtk development packages

**Texture rendering issues**
- Verify OpenGL context is available
- Check that texture updates are happening in render thread
- Ensure browser dimensions are valid (> 0)

**Memory leaks or crashes**
- Update to latest MEWVF version
- Verify proper browser cleanup in your code
- Check for resource contention in multi-browser scenarios

### Debug Information

Enable debug logging by adding to JVM arguments:
```
-Dorg.slf4j.simpleLogger.log.MEWVF=DEBUG
```

View platform detection details:
```java
PlatformType platform = MEWVFFabric.getPlatform();
System.out.println("Detected: " + platform);
System.out.println("Supported: " + platform.isSupported());
```

## Development

### Building from Source

```bash
# Clone repository
git clone https://github.com/yourusername/mewvf.git
cd mewvf

# Build Fabric version
./gradlew fabric:build

# Build Forge version  
./gradlew forge:build

# Build both
./gradlew build
```

### Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests if applicable
5. Commit changes (`git commit -m 'Add amazing feature'`)
6. Push to branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

### Project Structure

```
mewvf/
├── fabric/                 # Fabric mod implementation
├── forge/                  # Forge mod implementation
├── common/                 # Shared code between platforms
├── docs/                   # Documentation
├── examples/               # Example implementations
└── tests/                  # Unit and integration tests
```

## API Documentation

### Core Classes

- `MEWVFFabric` / `MEWVFForge` - Main mod class and API entry point
- `MEWVFBrowser` - Individual browser instance
- `UniversalWebViewManager` - Cross-platform WebView management
- `PlatformDetector` - Platform detection and validation

### Browser Lifecycle

```java
// 1. Create browser
MEWVFBrowser browser = MEWVFFabric.createBrowser("id", width, height);

// 2. Configure and load content
browser.loadUrl("https://example.com");

// 3. Update each frame (automatic with event system)
browser.updateTexture();

// 4. Render texture in your GUI
int textureId = browser.getTextureId();

// 5. Handle input events
browser.sendMouseClick(x, y, true);

// 6. Cleanup when done
browser.destroy();
// or
MEWVFFabric.destroyBrowser("id");
```

## Performance Considerations

- **Texture Updates**: Automatically handled by event system, but can be manually controlled
- **Memory Usage**: Each browser uses ~50-100MB RAM depending on content
- **CPU Usage**: WebView rendering happens on separate threads
- **Maximum Browsers**: Recommended limit of 10 concurrent browsers per world

## Compatibility

### Mod Compatibility
- **WebDisplays** - Full compatibility (drop-in MCEF replacement)
- **Custom web mods** - Compatible via MCEF API emulation
- **Other WebView mods** - May conflict, disable other web frameworks

### Version Compatibility
- **Minecraft**: 1.20.1 (other versions may work but are untested)
- **Java**: 17+ required (21+ recommended)
- **Forge**: 47.4.5+
- **Fabric**: 0.14.0+ with Fabric API

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Original MCEF by montoyo for the API design inspiration
- WebDisplays mod for providing the use case
- The ARM64/Android Minecraft community for testing and feedback
- Platform-specific WebView implementations (Android WebView, JavaFX, WebView2, WebKitGTK)

## Support
Discord:

## Roadmap

### v0.9.3 (Next Release)
- Improved error handling and recovery
- Better resource management
- Performance optimizations for mobile devices

### v1.0.0 (Stable Release)
- Full API stability guarantee
- Comprehensive documentation
- Production-ready for all platforms

### Future Versions
- Minecraft 1.21+ support
- Additional platform support (RISC-V, etc.)
- Advanced WebView features (geolocation, camera, etc.)
- Performance profiling and optimization tools

---

**Note**: MEWVF is designed specifically for ARM64 platforms. For Intel/AMD x86_64 systems, continue using the original MCEF framework.
