# 🛠️ Scripts

<div align="center">

![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)
![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)

**[🇬🇧 English](#-english) | [🇩🇪 Deutsch](#-deutsch)**

</div>

---

## 🇬🇧 English

A collection of useful shell scripts for Arch Linux, managed with GNU Stow.

### 📦 Included Scripts

| Script | Description |
|--------|-------------|
| `dotfiles-sync` | Sync system configs with dotfiles repository |
| `system-check` | System diagnostics, updates, and repo status |
| `rofi-wifi` | WiFi network manager with Rofi interface |
| `update-readme` | Auto-generate README files for repositories |

### 🚀 Installation

```bash
# Clone repository
git clone https://github.com/Sampirer/scripts ~/repos/scripts
cd ~/repos/scripts

# Make scripts available in PATH
stow -t ~ .
```

### 📖 Usage

After installation, scripts are available in `~/.local/bin/`:

```bash
# Sync dotfiles
dotfiles-sync scan      # Show missing configs
dotfiles-sync status    # Repository overview

# System check
system-check            # Run diagnostics

# WiFi manager
rofi-wifi               # Open WiFi menu

# Update READMEs
update-readme           # Generate for all repos
```

### 🔗 Related

- [dotfiles](https://github.com/Sampirer/dotfiles) – Configuration files
- [arch-install](https://github.com/Sampirer/arch-install) – Installation script

---

## 🇩🇪 Deutsch

Eine Sammlung nützlicher Shell-Scripts für Arch Linux, verwaltet mit GNU Stow.

### 📦 Enthaltene Scripts

| Script | Beschreibung |
|--------|--------------|
| `dotfiles-sync` | System-Configs mit Dotfiles-Repository synchronisieren |
| `system-check` | System-Diagnose, Updates und Repo-Status |
| `rofi-wifi` | WLAN-Netzwerk-Manager mit Rofi-Interface |
| `update-readme` | README-Dateien automatisch generieren |

### 🚀 Installation

```bash
# Repository klonen
git clone https://github.com/Sampirer/scripts ~/repos/scripts
cd ~/repos/scripts

# Scripts im PATH verfügbar machen
stow -t ~ .
```

### 📖 Verwendung

Nach der Installation sind die Scripts in `~/.local/bin/` verfügbar:

```bash
# Dotfiles synchronisieren
dotfiles-sync scan      # Fehlende Configs anzeigen
dotfiles-sync status    # Repository-Übersicht

# System-Check
system-check            # Diagnose ausführen

# WLAN-Manager
rofi-wifi               # WLAN-Menü öffnen

# READMEs aktualisieren
update-readme           # Für alle Repos generieren
```

### 🔗 Verwandt

- [dotfiles](https://github.com/Sampirer/dotfiles) – Konfigurationsdateien
- [arch-install](https://github.com/Sampirer/arch-install) – Installations-Script

---

## 🤖 Credits

This setup was created with significant assistance from [Claude](https://claude.ai) (Anthropic).

Diese Konfiguration wurde mit erheblicher Unterstützung von [Claude](https://claude.ai) (Anthropic) erstellt.


---

<div align="center">

**Made with ❤️ on Arch Linux**

</div>
