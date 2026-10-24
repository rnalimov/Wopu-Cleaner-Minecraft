# Wopu Cleaner (Windows Cleaner)
# ZIP Password: WopuWopu
**Wopu Cleaner** is a single-file C++ utility for Windows designed for deep system cleaning, anti-forensics, and process concealment. It is primarily used to automate the removal of digital traces and bypass manual anti-cheat inspections (e.g., via Discord or AnyDesk) in games like CS2 and Minecraft.

<img width="805" height="691" alt="image" src="https://github.com/user-attachments/assets/7ee74389-7064-44cb-b344-fac8d2961cea" /> <img width="400" height="325" alt="image" src="https://github.com/user-attachments/assets/cfe18fba-a0b5-44cd-b3f6-17352c3ea271" />


> **⚠️ WARNING / DISCLAIMER**
> This tool modifies critical system areas (MFT, PEB, Event Logs, USN Journal, Registry) and uses evasion techniques. It will almost certainly be flagged by antivirus software (AV) and anti-cheat systems.
> * Using this tool violates the Terms of Service of most gaming platforms and will likely result in permanent bans (including HWID bans).
> * The author is not responsible for system damage, data loss, or account bans. Use at your own risk.

## Features

The application is divided into several tabs:

### 1. Clean (Basic & Advanced)
Automates system trace removal using a bundled PowerShell script executed in the background.
* **Basic Clean:** Clears Windows Event Logs (`wevtutil`), deletes the USN Journal (`fsutil`), and wipes Prefetch, Amcache, BAM/DAM, UserAssist, RecentDocs, and standard temporary files.
* **Advanced Clean:** Additionally wipes browser caches (Chrome, Edge, Firefox, Opera, Yandex, Brave), Discord, Steam, Minecraft logs, and network history.

### 2. Custom (MFT Scanner & Backup)
* **Direct MFT Parsing:** The tool reads the NTFS Master File Table (MFT) directly using `FSCTL_ENUM_USN_DATA`. This allows it to find files (like cheat clients) that might be hidden from standard file system APIs.
* **Smart Filtering:** Automatically ignores critical system directories (`Windows`, `Program Files`, `System32`, etc.) to prevent accidental OS damage.
* **Backup Modes:**
  * **Stealth:** Matched files are moved to a hidden system folder: `C:\ProgramData\Microsoft\Windows\~svc_<timestamp>` (with Hidden + System attributes).
  * **Legacy:** Matched files are packed into a password-protected ZIP archive at `C:\Windows\System32\~bk_<timestamp>.zip` (requires 7-Zip or WinRAR installed). If archiving fails, the originals are still deleted.

### 3. Misc (Evasion & Utilities)
* **Hide Client:** Copies the executable to `C:\Windows\System32\backgroundTasksHost.exe`, masks the Process Environment Block (PEB) to spoof the image path, and deletes the original file. The main window is excluded from screen capture using `WDA_EXCLUDEFROMCAPTURE` (invisible to OBS, Discord, AnyDesk, Win+Shift+S, etc.) and removed from the taskbar/Alt+Tab. Toggle window visibility via `Ctrl+Alt+Shift+F12`.
* **Password (Stealth Mode):** Rebuilds the `.exe` with a SHA-256 password hash. To launch it, you must pass the password as the first command-line argument (`Cleaner.exe <your_password>`). If the password is missing or incorrect, it displays a fake error message and exits.
* **Fake Virus:** Displays a full-screen, topmost fake ransomware window. It uses a low-level keyboard hook (`WH_KEYBOARD_LL`) to block the `Win` key, `Alt+Tab`, `Alt+F4`, and `Ctrl+Esc`.
* **Disabler:** Creates a full-screen overlay with `WDA_MONITOR` affinity. Screen capture tools (OBS, DXGI-based recorders) will see a black screen. Toggle off with `F9`.
* **Auto Cleaner:** A background thread that continuously deletes the USN Journal and Shellbags (Explorer folder history) every second.
* **Close Cleaner:** Self-destructs the executable, clears the USN Journal, and wipes execution traces (Prefetch, Amcache, UserAssist, Recent).

## Technical Details

* **Language:** C++ (Win32 API only, no external dependencies).
* **Architecture:** Single `.cpp` file. Requires Administrator privileges (UAC elevation on launch).
* **MFT Parsing:** Uses `DeviceIoControl` with `FSCTL_ENUM_USN_DATA` to bypass standard file system limitations.
* **Screen Capture Bypass:** Uses `SetWindowDisplayAffinity` with `WDA_EXCLUDEFROMCAPTURE` (Windows 10 2004+) and `WDA_MONITOR`.
* **PEB Modification:** Modifies `ImagePathName` and `CommandLine` inside `RTL_USER_PROCESS_PARAMETERS` to masquerade as a system process.

## Building

Since this is a single-file project, you can compile it using MSVC or MinGW.

**Using MSVC (Developer Command Prompt):**
```cmd
cl /EHsc /O2 /MT main.cpp /link /SUBSYSTEM:WINDOWS /OUT:Cleaner.exe
```
Using MinGW:

```bash
x86_64-w64-mingw32-g++ -O2 -static -municode -mwindows main.cpp -o Cleaner.exe -lcomctl32 -lshell32 -lrstrtmgr -ladvapi32 -lws2_32 -lwininet
```
License
This project is licensed under the MIT License. See the LICENSE file for details.
