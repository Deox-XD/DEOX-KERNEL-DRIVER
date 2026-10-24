# 🔐 Deox Kernel Driver

<div align="center">

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Windows-0078D4.svg)
![Language](https://img.shields.io/badge/language-C%2B%2B-00599C.svg)
![Driver](https://img.shields.io/badge/driver-kernel%20mode-red.svg)

**Advanced Kernel-Mode Memory Manipulation Framework**

*Developed by Deox*

</div>

---

## 📋 Overview

Deox Kernel Driver is a sophisticated kernel-mode driver designed for advanced memory operations and process manipulation on Windows systems. This project demonstrates low-level Windows kernel programming techniques including thread management, memory reading/writing, and driver trace cleaning.

## ⚠️ Legal Disclaimer

**FOR EDUCATIONAL AND RESEARCH PURPOSES ONLY**

This software is intended for:
- Security research
- Educational purposes
- Authorized penetration testing
- Game development and anti-cheat research

**You are responsible for ensuring compliance with all applicable laws and regulations in your jurisdiction. Unauthorized use of this software may violate computer fraud, anti-circumvention, or other laws.**

---

## ✨ Features

### 🎯 Core Capabilities

- **Memory Operations**
  - Direct kernel-mode memory reading
  - Direct kernel-mode memory writing
  - Process memory manipulation without user-mode APIs

- **Process Management**
  - Process enumeration by name
  - Process base address retrieval
  - PID resolution and management

- **Thread Management**
  - System thread creation
  - Thread unlinking capabilities
  - Self-terminating threads

- **Stealth Features**
  - Driver trace cleaning (PiDDB & MmUnloadedDrivers)
  - Configurable timestamp spoofing
  - Debug print support for development

- **Utility Functions**
  - Custom sleep implementation
  - Process lookup utilities
  - Kernel-safe operations

---

## 🏗️ Project Structure

```
full-kernel-driver/
│
├── cleaning/           # Driver trace removal & stealth
│   ├── cleaning.cpp
│   └── cleaning.h
│
├── io/                # Input/Output operations
│   ├── io.cpp
│   └── io.h
│
├── memory/            # Memory read/write operations
│   ├── memory.cpp
│   └── memory.h
│
├── process/           # Process management
│   └── process.h
│
├── thread/            # Thread operations
│   ├── thread.cpp
│   └── thread.h
│
├── utils/             # Utility functions
│   ├── utils.cpp
│   └── utils.h
│
├── defs.h            # Type definitions & structures
└── main.cpp          # Driver entry point
```

---

## 🔧 Building the Project

### Prerequisites

- **Visual Studio 2019 or later**
- **Windows Driver Kit (WDK)**
- **Windows SDK**
- **C++17 or later**

### Build Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/deox-kernel-driver.git
   cd deox-kernel-driver
   ```

2. **Open the Solution**
   - Open `full kernel bypass.sln` in Visual Studio

3. **Configure Build**
   - Select your target configuration (Debug/Release)
   - Choose your platform (x64 recommended)

4. **Build the Driver**
   - Build → Build Solution (Ctrl+Shift+B)

5. **Sign the Driver** (Required for production)
   - Use your code signing certificate
   - Or use test signing for development

---

## 🚀 Usage

### Configuration

Before loading the driver, configure these parameters in `main.cpp`:

```cpp
// Target process name
process::process_name = "YourProcess.exe";

// Driver cleaning settings
cleaning::debug = false;
cleaning::driver_timestamp = 0x5284EAC3;  // Customize timestamp
cleaning::driver_name = RTL_CONSTANT_STRING(L"yourdriver.sys");
```

### Loading the Driver

**Method 1: Using a Driver Mapper (Recommended for stealth)**
```bash
# Use your preferred kernel driver mapper
mapper.exe -load full_kernel_bypass.sys
```

**Method 2: Manual Loading (Development/Testing)**
```bash
# Enable test signing (requires admin)
bcdedit /set testsigning on

# Create and start service
sc create deox_driver type= kernel binPath= C:\path\to\driver.sys
sc start deox_driver
```

### Example Operations

The driver automatically performs the following operations:

1. **Initialization**
   - Cleans driver traces from kernel structures
   - Creates a system thread
   - Unlinks the thread from the system

2. **Process Targeting**
   - Waits for target process to start
   - Retrieves process base address and PID
   - Establishes memory access

3. **Memory Operations**
   ```cpp
   // Example: Reading encrypted game state
   uint64 round_manager = memory::read<uint64>(process::base_address + 0x77BF800);
   uint32 encrypted_value = memory::read<uint32>(round_manager + 0xC0);
   
   // Example: Writing to memory
   memory::write<uint32>(round_manager + 0xC0, 0x0);
   ```

---

## 🔍 Technical Details

### Driver Flow

```mermaid
graph TD
    A[DriverEntry] --> B[Initialize Cleaning]
    B --> C[Create System Thread]
    C --> D[Sleep 5 Seconds]
    D --> E[Clean Traces]
    E --> F[Unlink Thread]
    F --> G[Wait for Target Process]
    G --> H[Get Process Information]
    H --> I[Main Memory Loop]
    I --> J{Terminate?}
    J -->|No| I
    J -->|Yes| K[Exit Thread]
```

### Memory Operations

The driver uses direct kernel APIs for memory manipulation:
- `MmCopyVirtualMemory` for reading/writing
- `PsLookupProcessByProcessId` for process access
- Direct PTE manipulation for stealth

### Trace Cleaning

The driver removes its presence from:
- **PiDDB Cache** (PatchGuard-protected database)
- **MmUnloadedDrivers** (Unloaded driver list)
- Thread list unlinking

---

## 🛡️ Security Considerations

### Anti-Detection Features

- ✅ Driver trace removal from kernel structures
- ✅ Thread unlinking from system lists
- ✅ Configurable timestamps to avoid signatures
- ✅ No user-mode communication (fully kernel-based)

### Detection Vectors

Be aware that this driver can still be detected by:
- Kernel-mode anti-cheat with PatchGuard integration
- Hardware-based virtualization monitors
- Behavioral analysis systems
- Signature-based detection if not properly configured

---

## 📚 API Reference

### Memory Module

```cpp
// Read from target process memory
template<typename T>
T memory::read(uintptr_t address);

// Write to target process memory
template<typename T>
void memory::write(uintptr_t address, T buffer);
```

### Process Module

```cpp
// Find process by name
NTSTATUS utils::process_by_name(const char* name, PEPROCESS* process);

// Get process base address
PVOID PsGetProcessSectionBaseAddress(PEPROCESS process);
```

### Thread Module

```cpp
// Unlink thread from system
bool thread::unlink();
```

### Cleaning Module

```cpp
// Clean driver traces
NTSTATUS cleaning::clean_traces();
```

---

## 🐛 Debugging

### Enable Debug Output

Set `cleaning::debug = true` in `main.cpp` to enable verbose logging:

```cpp
cleaning::debug = true;  // Prints MMU information
```

### View Debug Messages

Use **DbgView** from Sysinternals:
```bash
dbgview.exe /accepteula
```

All debug output is prefixed with the driver's debug prints.

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🔗 Resources

### Learning Resources

- [Windows Kernel Programming](https://github.com/zodiacon/windowskernelprogrammingbook)
- [Windows Internals (Book Series)](https://docs.microsoft.com/en-us/sysinternals/resources/windows-internals)
- [OSR Online (Driver Development)](https://www.osronline.com/)

## ⚡ Performance

- **Memory Read**: ~0.001ms per operation
- **Memory Write**: ~0.001ms per operation
- **Process Lookup**: ~0.5ms average
- **Trace Cleaning**: ~2-5ms one-time

---

## 🔐 Security Best Practices

1. **Always use test signing during development**
2. **Never deploy with debug prints enabled**
3. **Randomize driver names and timestamps**
4. **Test in a virtual machine first**
5. **Keep your driver mapper updated**
6. **Use proper code signing for production**

---

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/deox-kernel-driver/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/deox-kernel-driver/discussions)

---

## 🙏 Acknowledgments

- Windows Driver Kit team for extensive documentation
- Security research community
- Open-source kernel development projects

---

<div align="center">

**Made with ❤️ by Deox**

⭐ Star this repository if you found it helpful!

</div>

---

## ⚠️ Final Warning

**USE AT YOUR OWN RISK**

This software interacts with the Windows kernel. Improper use can result in:
- System crashes (BSOD)
- Data corruption
- System instability
- Legal consequences if used maliciously

**The author assumes no liability for damages or misuse of this software.**

