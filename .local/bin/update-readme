#!/bin/bash
# update-readme.sh - Automatische README.md Generierung für verschiedene Repository-Typen
# Unterstützt dotfiles, scripts und arch-install Repositories

set -e

# Tokyo Night Storm Farben
readonly RED='\033[38;2;247;118;142m'
readonly GREEN='\033[38;2;158;206;106m'
readonly YELLOW='\033[38;2;224;175;104m'
readonly BLUE='\033[38;2;122;162;247m'
readonly PURPLE='\033[38;2;187;154;247m'
readonly CYAN='\033[38;2;125;207;255m'
readonly WHITE='\033[38;2;192;202;245m'
readonly GRAY='\033[38;2;86;95;137m'
readonly RESET='\033[0m'

# Hilfsfunktionen
log_info() {
    echo -e "${BLUE}[INFO]${RESET} $1"
}

log_success() {
    echo -e "${GREEN}[SUCCESS]${RESET} $1"
}

log_warning() {
    echo -e "${YELLOW}[WARNING]${RESET} $1"
}

log_error() {
    echo -e "${RED}[ERROR]${RESET} $1"
}

log_section() {
    echo -e "\n${PURPLE}=== $1 ===${RESET}"
}

# Screenshot-Funktion
take_screenshot() {
    local repo_dir="$1"
    local screenshot_dir="$repo_dir/screenshots"
    
    mkdir -p "$screenshot_dir"
    
    local timestamp=$(date +"%Y%m%d_%H%M%S")
    local screenshot_file="$screenshot_dir/desktop_$timestamp.png"
    
    log_info "Erstelle Screenshot: $screenshot_file"
    
    if command -v scrot >/dev/null 2>&1; then
        scrot "$screenshot_file"
    elif command -v gnome-screenshot >/dev/null 2>&1; then
        gnome-screenshot -f "$screenshot_file"
    elif command -v import >/dev/null 2>&1; then
        import -window root "$screenshot_file"
    else
        log_error "Kein Screenshot-Tool gefunden (scrot, gnome-screenshot, oder ImageMagick)"
        return 1
    fi
    
    log_success "Screenshot erstellt: $screenshot_file"
    echo "desktop_$timestamp.png"
}

# Dotfiles Repository verarbeiten
process_dotfiles() {
    local repo_dir="$1"
    local readme_file="$repo_dir/README.md"
    
    log_section "Verarbeite Dotfiles Repository: $repo_dir"
    
    # README.md Header
    cat > "$readme_file" << 'EOF'
# Dotfiles

Meine persönlichen Konfigurationsdateien, verwaltet mit GNU Stow.

## Installation

```bash
# Repository klonen
git clone <repository-url> ~/.dotfiles
cd ~/.dotfiles

# Alle Konfigurationen installieren
stow */

# Oder einzelne Pakete installieren
stow <package-name>
```

## Pakete

EOF
    
    # Stow-Ordner durchgehen
    for stow_dir in "$repo_dir"/*/; do
        if [[ -d "$stow_dir" && ! "$stow_dir" =~ screenshots$ ]]; then
            local package_name=$(basename "$stow_dir")
            echo "### $package_name" >> "$readme_file"
            echo "" >> "$readme_file"
            
            # Dateien auflisten
            echo "**Enthaltene Dateien:**" >> "$readme_file"
            find "$stow_dir" -type f -printf "- \`%P\`\n" | sort >> "$readme_file"
            echo "" >> "$readme_file"
            
            # Benötigte Pakete extrahieren
            local packages=""
            while IFS= read -r -d '' file; do
                case "$file" in
                    *.conf|*.config|*.rc|*.yml|*.yaml|*.toml)
                        # Suche nach Paket-Referenzen in Kommentaren
                        local found_packages=$(grep -h "# requires:\|# needs:\|# package:\|# depends:" "$file" 2>/dev/null | sed 's/.*: *//' | tr ',' '\n' | xargs)
                        if [[ -n "$found_packages" ]]; then
                            packages="$packages $found_packages"
                        fi
                        ;;
                esac
            done < <(find "$stow_dir" -type f -print0)
            
            if [[ -n "$packages" ]]; then
                echo "**Benötigte Pakete:**" >> "$readme_file"
                echo "\`\`\`bash" >> "$readme_file"
                echo "sudo pacman -S$(echo "$packages" | tr ' ' '\n' | sort -u | tr '\n' ' ')" >> "$readme_file"
                echo "\`\`\`" >> "$readme_file"
                echo "" >> "$readme_file"
            fi
        fi
    done
    
    # Screenshots einbinden
    local screenshot_dir="$repo_dir/screenshots"
    if [[ -d "$screenshot_dir" ]]; then
        echo "## Screenshots" >> "$readme_file"
        echo "" >> "$readme_file"
        
        for screenshot in "$screenshot_dir"/*.png "$screenshot_dir"/*.jpg; do
            if [[ -f "$screenshot" ]]; then
                local screenshot_name=$(basename "$screenshot")
                echo "![Screenshot](screenshots/$screenshot_name)" >> "$readme_file"
                echo "" >> "$readme_file"
            fi
        done
    fi
    
    log_success "README.md für Dotfiles erstellt: $readme_file"
}

# Scripts Repository verarbeiten
process_scripts() {
    local repo_dir="$1"
    local readme_file="$repo_dir/README.md"
    local scripts_dir="$repo_dir/.local/bin"
    
    log_section "Verarbeite Scripts Repository: $repo_dir"
    
    if [[ ! -d "$scripts_dir" ]]; then
        log_warning "Scripts-Verzeichnis nicht gefunden: $scripts_dir"
        return 1
    fi
    
    # README.md Header
    cat > "$readme_file" << 'EOF'
# Scripts

Sammlung nützlicher Shell-Scripts für den täglichen Gebrauch.

## Installation

```bash
# Repository klonen
git clone <repository-url> ~/scripts
cd ~/scripts

# Scripts verfügbar machen
stow .
```

## Scripts

EOF
    
    # Scripts durchgehen
    for script in "$scripts_dir"/*; do
        if [[ -f "$script" && -x "$script" ]]; then
            local script_name=$(basename "$script")
            echo "### $script_name" >> "$readme_file"
            echo "" >> "$readme_file"
            
            # Beschreibung aus Kommentar-Header extrahieren
            local description=$(head -10 "$script" | grep "^#" | grep -v "^#!/" | head -1 | sed 's/^# *//')
            if [[ -n "$description" ]]; then
                echo "$description" >> "$readme_file"
                echo "" >> "$readme_file"
            fi
            
            # Usage-Beispiel generieren
            echo "**Usage:**" >> "$readme_file"
            echo "\`\`\`bash" >> "$readme_file"
            
            # Versuche Usage aus dem Script zu extrahieren
            local usage=$(grep -m1 "Usage:\|usage:" "$script" 2>/dev/null | sed 's/.*[Uu]sage: *//' || echo "$script_name [options]")
            echo "$usage" >> "$readme_file"
            echo "\`\`\`" >> "$readme_file"
            echo "" >> "$readme_file"
        fi
    done
    
    log_success "README.md für Scripts erstellt: $readme_file"
}

# Arch-Install Repository verarbeiten
process_arch_install() {
    local repo_dir="$1"
    local readme_file="$repo_dir/README.md"
    
    log_section "Verarbeite Arch-Install Repository: $repo_dir"
    
    # README.md Header
    cat > "$readme_file" << 'EOF'
# Arch Linux Installation Script

Automatisiertes Installationsskript für Arch Linux mit vorkonfigurierten Einstellungen.

## Features

EOF
    
    # Features aus Scripts extrahieren
    for script in "$repo_dir"/*.sh; do
        if [[ -f "$script" ]]; then
            # Suche nach Feature-Kommentaren
            grep "^# Feature:\|^# - " "$script" 2>/dev/null | sed 's/^# */- /' >> "$readme_file" || true
        fi
    done
    
    echo "" >> "$readme_file"
    echo "## Installation" >> "$readme_file"
    echo "" >> "$readme_file"
    echo "\`\`\`bash" >> "$readme_file"
    echo "# Script herunterladen und ausführen" >> "$readme_file"
    echo "curl -O <script-url>" >> "$readme_file"
    echo "chmod +x install.sh" >> "$readme_file"
    echo "./install.sh" >> "$readme_file"
    echo "\`\`\`" >> "$readme_file"
    echo "" >> "$readme_file"
    
    # Installierte Pakete auflisten
    echo "## Installierte Pakete" >> "$readme_file"
    echo "" >> "$readme_file"
    
    # Suche nach pacman-Befehlen in Scripts
    local packages=""
    for script in "$repo_dir"/*.sh; do
        if [[ -f "$script" ]]; then
            packages="$packages $(grep -o "pacman -S[^ ]* [^\"']*" "$script" 2>/dev/null | sed 's/pacman -S[^ ]* //' | tr '\n' ' ')"
        fi
    done
    
    if [[ -n "$packages" ]]; then
        echo "### Base Pakete" >> "$readme_file"
        echo "\`\`\`" >> "$readme_file"
        echo "$packages" | tr ' ' '\n' | sort -u | grep -v "^$" >> "$readme_file"
        echo "\`\`\`" >> "$readme_file"
    fi
    
    log_success "README.md für Arch-Install erstellt: $readme_file"
}

# Repository-Typ erkennen
detect_repo_type() {
    local repo_dir="$1"
    
    if [[ -d "$repo_dir/.local/bin" ]]; then
        echo "scripts"
    elif find "$repo_dir" -maxdepth 1 -type d -name "*" | grep -v "^\.$\|^\.\.$\|screenshots$" | head -1 | grep -q .; then
        # Hat Unterverzeichnisse (potentielle Stow-Pakete)
        echo "dotfiles"
    elif ls "$repo_dir"/*.sh >/dev/null 2>&1; then
        echo "arch-install"
    else
        echo "unknown"
    fi
}

# Hauptfunktion
main() {
    local take_screenshot_flag=false
    local target_dirs=()
    
    # Argument-Parsing
    while [[ $# -gt 0 ]]; do
        case $1 in
            --screenshot)
                take_screenshot_flag=true
                shift
                ;;
            *)
                target_dirs+=("$1")
                shift
                ;;
        esac
    done
    
    # Standard-Verzeichnisse wenn keine angegeben
    if [[ ${#target_dirs[@]} -eq 0 ]]; then
        target_dirs=(
            "$HOME/repos/dotfiles"
            "$HOME/repos/scripts"
            "$HOME/repos/arch-install"
        )
    fi
    
    log_info "Update README.md für Repository-Typen"
    
    for repo_dir in "${target_dirs[@]}"; do
        if [[ ! -d "$repo_dir" ]]; then
            log_warning "Verzeichnis nicht gefunden: $repo_dir"
            continue
        fi
        
        local repo_type=$(detect_repo_type "$repo_dir")
        log_info "Repository-Typ erkannt: $repo_type für $repo_dir"
        
        # Screenshot erstellen wenn gewünscht
        if [[ "$take_screenshot_flag" == true ]]; then
            take_screenshot "$repo_dir"
        fi
        
        # Repository verarbeiten
        case "$repo_type" in
            dotfiles)
                process_dotfiles "$repo_dir"
                ;;
            scripts)
                process_scripts "$repo_dir"
                ;;
            arch-install)
                process_arch_install "$repo_dir"
                ;;
            *)
                log_warning "Unbekannter Repository-Typ für: $repo_dir"
                ;;
        esac
    done
    
    log_success "README.md Update abgeschlossen!"
}

# Script ausführen
main "$@"
