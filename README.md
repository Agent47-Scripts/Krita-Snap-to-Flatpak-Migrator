# Fix: Krita Missing Menu Bar & Toolbar on Ubuntu Budgie (Snap to Flatpak Migration)

> **Background & Purpose:** I put together this step-by-step migration guide after troubleshooting persistent menu bar and toolbar issues with the Snap version of Krita on Linux (**Ubuntu Budgie 24.04 LTS** across iMac setups). My goal is to save you time, trial-and-error, and frustrating roadblocks while preserving custom brush bundles.

---

<p align="center">
  <a href="https://www.buymeacoffee.com/agent47.scripts" target="_blank" rel="noopener noreferrer">
    <img src="https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Donate-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me A Coffee">
  </a>
</p>

---

## 🚀 Key Features & Problem Solvers

- **Eliminates Budgie Panel / AppMenu Conflicts:** Replaces the sandboxed Snap build of Krita (`krita` via Snap) with the Flatpak build (`org.kde.krita`), avoiding menu bar disappearing or collapsing to minimal entries (*Krita*, *New File*, *Quit*) while working on Ubuntu Budgie.
- **Resolves Locked Menu & Toolbar States:** Moves away from Snap-specific `kritarc` state quirks (`MenuBar=Disabled` / `ToolBarsMovable=Disabled`) so menus and toolbars remain accessible and customizable.
- **Preserves Custom Brush Bundles & Resources:** Backs up existing Krita user data and brush bundles from the Snap directories before removing the Snap package.
- **Cleans Up Stale `.desktop` Overrides:** Removes custom user `.desktop` overrides under `~/.local/share/applications/` after removing the Snap package.
- **Repeatable Multi-Machine Workflow:** Designed as clear, copy-pasteable terminal commands so you can apply the exact same setup across multiple machines.

---

## 🔑 Prerequisites & System Context

- **Operating System:** Ubuntu Budgie (tested on **24.04 LTS** on iMac hardware).
- **Terminal Access:** A terminal emulator (such as Tilix or the default terminal) with `sudo` privileges for removing the Snap package and installing `flatpak` / Flathub.
- **Existing Snap Data (If Applicable):** If custom brush bundles or resources already exist under `~/snap/krita/`, back them up in Step 1 before running `snap remove krita`.

---

## ⚙️ Usage

Run the steps in order in your terminal.

### 1. Close Krita & Back Up Brush Bundles and Resources

Run this under the user account holding your Krita data:

```bash
killall krita 2>/dev/null || true
BACKUP_DIR=~/krita_migration_$(date +%Y%m%d_%H%M%S)
mkdir -p "$BACKUP_DIR"
cp -a ~/snap/krita/current/.local/share/krita "$BACKUP_DIR/krita_data" 2>/dev/null || true
cp -a ~/snap/krita/common/.local/share/krita "$BACKUP_DIR/krita_common" 2>/dev/null || true
ls -la "$BACKUP_DIR"
```

### 2. Remove the Snap Version of Krita & Clean Up `.desktop` Overrides

```bash
sudo snap remove krita
rm -f ~/.local/share/applications/*krita*.desktop
update-desktop-database ~/.local/share/applications 2>/dev/null || true
```

### 3. Install Krita via Flatpak (`org.kde.krita`)

```bash
sudo apt update
sudo apt install -y flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak install -y flathub org.kde.krita
```

### 4. Restore Backed-Up Brush Bundles to the Flatpak Directory

```bash
mkdir -p ~/.var/app/org.kde.krita/data/krita
cp -rn ~/krita_migration_*/krita_data/* ~/.var/app/org.kde.krita/data/krita/ 2>/dev/null || true
cp -rn ~/krita_migration_*/krita_common/* ~/.var/app/org.kde.krita/data/krita/ 2>/dev/null || true
ls -la ~/.var/app/org.kde.krita/data/krita
```

### 5. Launch Krita & Customize the Top Toolbar

```bash
flatpak run org.kde.krita &
```

1. In the Krita window, open **Settings → Configure Toolbars...** (*Einstellungen → Werkzeugleisten einrichten...* / *Ajustes → Configurar barras de herramientas...*).
2. Select the target toolbar at the top, move the desired actions from the left column to the right column, and click **OK**.
3. If any toolbar is hidden, re-enable it under **Settings → Toolbars Shown**.

---

## 🛠️ Multi-User & Multi-Machine Notes (Edge Cases)

- **Multiple User Accounts on One Machine:** Repeat Steps 1, 2, 4, and 5 for each user account that has Krita data or needs Krita launched with restored brush bundles.
- **No Snap Data Found:** If `~/snap/krita/` does not exist on a fresh machine, Step 1 simply creates an empty timestamped backup folder and Step 4 safely skips missing source folders.

---

## 📜 License
Licensed under the [MIT License](LICENSE). Free to use, adapt, and share.