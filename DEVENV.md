# Entwicklungsumgebung (DevEnvironment)

Anleitung zum Einrichten einer Entwicklungsumgebung (DEVENV) für die Programmierung auf Windows mit Hilfe von Windows Subsystem für Linux in einer beliebigen Programmiersprache, hier in diesem Beispiel wird als Editor Visual Studio Code verwendet. Alle für die Ausführung und Erstellung verwendeten Bibliotheken werden speziell in Docker-Containern verwendet, um eine Kapselung zu ermöglichen.

## Inhaltsangabe

<!-- TOC -->
- [Entwicklungsumgebung (DevEnvironment)](#entwicklungsumgebung-devenvironment)
  - [Inhaltsangabe](#inhaltsangabe)
  - [Hinweise](#hinweise)
    - [Keyboard Shortcuts](#keyboard-shortcuts)
    - [Systemressourcen limitieren](#systemressourcen-limitieren)
  - [Vorbereitung](#vorbereitung)
    - [Windows Updates](#windows-updates)
    - [Virtualisierung](#virtualisierung)
  - [Installation](#installation)
    - [Windows Subsystem für Linux](#windows-subsystem-für-linux)
    - [Ubuntu 20.04 LTS](#ubuntu-2004-lts)
    - [Chocolatey](#chocolatey)
    - [Docker Desktop](#docker-desktop)
    - [Visual Studio Code](#visual-studio-code)
  - [Fragen?](#fragen)
<!-- /TOC -->

## Hinweise

### Keyboard Shortcuts

Die folgenden Shortcuts von Windows sollten bekannt sein:

- Anwendung mit administrativen Rechten ausführen: `Ctrl + Shift + (Enter oder Klicken)`
- Öffnen Windows PowerShell **mit** administrativen Rechten: `Windows + X  A`
- Öffnen Windows PowerShell **ohne** administrativen Rechten: `Windows + X  I`
- Windows Screenshot Tool: `Windows + Shift + S`

Die Keyboard Shortcuts von Visual Studio Code können [hier](https://cheatsheetmaker.com/vs-code-cheat-sheet) und [hier](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf) gefunden werden.

### Systemressourcen limitieren

Falls ihr Docker Desktop dauerhaft an habt, ist es sinnvoll die Systemresourcen welche wsl benutzen kann zu limitieren, sonst verbraucht der vmmem-Prozess sehr schnell viel RAM. Das limitieren kann über die Konfigurationsdatei `%userprofile%/.wslconfig` eingestellt werden. Bearbeiten kann man diese einfach mit `code %userprofile%/.wslconfig`.

_Beispiel (minimal):_

```txt
[wsl2]
memory=2GB # Limits VM memory in WSL2 to 2GB
processors=2 # Makes the WSL2 VM use four virtual processors
```

## Vorbereitung

### Windows Updates

Euer Windows 10 muss auf Version 1903 oder höher sein, mit Build 18362.1049 oder höher. Zum Überprüfen von Version und Build startet `winver` entweder über die Suchleiste oder das Ausführfenster (drückt: Windows-Taste + R). Bitte updated euer System wenn nötig.

### Virtualisierung

Für Windows Subsystem für Linux (WSL) und Docker benötigen wir die Virtualisierungsfunktionen des Rechners. Dazu prüft ihr bitte im Task-Manager, ob diese bereits aktiviert sind. Im erweiterten Task-Manager findet ihr im Reiter "Leistung" unten Rechts eine Ausgabe "Virtualisierung", sollte diese auf "Deaktiviert" eingestellt sein, kontaktiert Ihr mich bitte. ([Screenshot](https://my.hidrive.com/lnk/bWigiKwM#file))

## Installation

### Windows Subsystem für Linux

- Starten der PowerShell als Administrator mit `Windows + X  A`
- Aktivieren der Hyper-V-Funktionen in den Windows-Features

```powershell
Enable-WindowsOptionalFeature -FeatureName "VirtualMachinePlatform" -All -Online -NoRestart
```

- Aktivieren von Windows Subsystem für Linux in den Windows-Features

```powershell
Enable-WindowsOptionalFeature -FeatureName "Microsoft-Windows-Subsystem-Linux" -All -Online -NoRestart
```

- System neu starten
- Update des Linux-Kernels für WSL [hier](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) herunterladen
- Installieren des heruntergeladenen Updates des Linux-Kernels (als Administrator)
- System neu starten
- Ausführen von `wsl --set-default-version 2` in PowerShell mit **und** ohne Administratorrechten

### Ubuntu 20.04 LTS

- Herunterlade [Ubuntu 20.04 LTS](https://www.microsoft.com/de-de/p/ubuntu-2004-lts/9n6svws3rx71) aus dem Microsft Store (Anmeldung **nicht** erforderlich)
- Starte Ubuntu 20.04 LTS
- Gib einen UNIX Benutzernamen an (Kleinschreibung und keine Sonderzeichen)
- Gib ein Passwort ein (sollte einfach zu merken sein)
- Kopiere den kompleten folgenden Befehl:

```bash
sudo bash -c 'cat << EOF > /etc/apt/sources.list
###### Ubuntu Main Repos
deb http://de.archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse

###### Ubuntu Update Repos
deb http://de.archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
deb http://de.archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse

EOF'
```

- Füge den Befehl mit einem rechten Mausklick in das Ubuntu-Fenster ein und führe ihn einfach aus (Passworteingabe erforderlich)
- Führe danach `sudo apt update && sudo apt upgrade` und bestätige mit `y`
- Schließe das Ubuntu-Fenster
- Öffne ein PowerShell-Fenster und führe `wsl -s Ubuntu-20.04` aus

### Chocolatey

[Chocolatey](https://chocolatey.org) ist ein Software-Manager, ihr könnt fast jede Software welche im [Repository](https://chocolatey.org/packages) verfügbar ist, mit diesem Programm herunterladen, installieren, updaten und auch wieder zu deinstallieren. Es wird empfohlen, die automatischen Updates in der mit Chocolatey installierten Software zu deaktivieren.

- Öffne ein Windows PowerShell Fenster mit administrativen Rechten
- Führe den folgenden Befehl aus

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
- Schließe das PowerShell-Fenster

### Docker Desktop

- Öffne ein **neues** Windows PowerShell Fenster mit administrativen Rechten
- Führe `choco install docker-desktop` aus
- Bestätige die installation mit `a`
- Starte den Rechner neu
- Öffne "Docker Desktop"
- In den Einstellungen stelle folgendes ein
  - General
    - OFF: Start Docker Desktop when you log in
    - OFF: Automatically check for updates
    - OFF: Expose daemon on tcp://localhost:2375 without TLS
    - ON: Use the WSL 2 based engine
    - OFF: Send usage statistics
  - Resources -> Network
    - ON: Manual DNS configuration
    - DNS: 9.9.9.9
  - Resources -> WSL Integration
    - ON: Enable integration with my default WSL distro -> On
  - Experimental Features
    - Alles deaktiviert
  - Kubernetes
    - Alles deaktiviert
- Schließen von Docker Desktop geht nur über das Menü vom Tray-Icon

### Visual Studio Code

- Öffne ein Windows PowerShell Fenster **mit** administrativen Rechten
- Führe `choco install vscode` aus
- Bestätige die Installation mit `y`
- Führe `choco install wslgit` aus
- Bestätige die Installation mit `y`
- Führe `New-Item -ItemType SymbolicLink -Path "C:\ProgramData\chocolatey\bin\git.exe" -Target "C:\ProgramData\chocolatey\bin\wslgit.exe"` aus
- Schließe das administrative PowerShell Fenster
- Öffne ein normales PowerShell Fenster
- Führe `code %APPDATA%\Code\User\settings.json` aus
- Füge den folgenden Text in das geöffnete VSCode Fenster ein

```json
{
    "update.mode": "none",
    "extensions.ignoreRecommendations": true,
    "telemetry.enableCrashReporter": false,
    "telemetry.enableTelemetry": false,
    "files.insertFinalNewline": true,
    "files.autoSave": "onFocusChange",
    "typescript.suggest.paths": false,
    "bracket-pair-colorizer-2.forceUniqueOpeningColor": true,
    "git.ignoreWindowsGit27Warning": true,
    "remote.downloadExtensionsLocally": true,
    "remote.containers.defaultExtensions": [
        "ms-vsliveshare.vsliveshare",
        "christian-kohler.path-intellisense",
        "yzhang.markdown-all-in-one",
        "DavidAnson.vscode-markdownlint",
        "EditorConfig.EditorConfig"
    ]
}
```

- Speicher die Datei und schließe VSCode
- Führe danach folgendende Befehle hintereinander aus

```cmd
code --install-extension ms-vscode-remote.remote-containers
code --install-extension ms-vsliveshare.vsliveshare
code --install-extension christian-kohler.path-intellisense
code --install-extension CoenraadS.bracket-pair-colorizer-2
code --install-extension EditorConfig.EditorConfig
code --install-extension yzhang.markdown-all-in-one
code --install-extension DavidAnson.vscode-markdownlint
```

## Fragen?

Bei Probleme oder Fragen einfach melden!
