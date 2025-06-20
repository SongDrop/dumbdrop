# DumbDrop Electron Manager

This is an Electron app that manages the DumbDrop file sharing server with easy setup and PIN protection.

## Features

- Runs quietly in the system tray
- Prompts for a secure 4-10 digit PIN on first run
- Clones or updates the DumbDrop repository automatically
- Installs all necessary Node.js dependencies
- Starts the DumbDrop server in the background
- Opens required firewall port on Windows
- Allows changing the PIN anytime from the tray menu
- Saves PIN securely using `electron-store`
- Prevents accidental app closing by minimizing to tray
- Registers for auto-start on Windows

## Usage

1. Ensure Node.js (v20+) is installed on your system.
2. Run `npm install` to install Electron and dependencies.
3. Start the app with `npm start`.
4. On first run, enter your desired 4-10 digit PIN when prompted.
5. Access DumbDrop at `http://localhost:4531` (or your configured port).
6. Use the tray icon menu to show the app, change the PIN, or exit.

## Build Prerequisites

## Setting up Node.js and Electron Environment

Before running or building the app, you may want to set up your shell environment to properly load Node.js (via NVM) and set necessary options for Electron.

Add and run the following in your shell or script on MacOSX:

```bash
brew install npm
brew install mono
brew install --cask wine-stable
```

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
export NODE_OPTIONS=--openssl-legacy-provider

node -v
npm -v

# Install Electron globally or locally as needed
npm install electron electron-builder electron-winstaller --save-dev --force
```

Before building and signing the Windows executable, you need to generate the required certificates:

1. Make sure you have **OpenSSL** and **osslsigncode** installed and available in your PATH.

   - On Windows, you can use Git Bash or WSL to run the scripts.
   - On Linux/macOS, install via your package manager (e.g., `apt`, `brew`).

2. Generate certificates by running:

   ```bash
   npm run gen-certs
   ```

   This runs the `gen-certs.sh` script, which creates a local Certificate Authority (CA) and signing certificate stored in the `/certs` directory.

3. Build the Electron app for Windows:

   ```bash
   npm run build-win
   ```

   This outputs the unsigned Windows executable in the `./dist/win-unpacked/` folder.

4. Sign the executable with your generated certificate:

   ```bash
   npm run sign-win
   ```

   This uses `osslsigncode` to apply your certificate to the binary, reducing Windows security warnings.

5. Verify the signed executable:

   ```bash
   npm run verify-win
   ```

   This confirms that the executable is properly signed with your local CA certificate.

6. Build the installer executable (which bundles the signed app EXE):
   npm run build-installer

7. Sign the installer executable:
   npm run sign-installer

8. Verify the signed installer executable:
   npm run verify-installer

Alternatively, run all steps in sequence with:

```bash
npm run full-build
```

This automates certificate generation, building, signing, and verification.

## Code Signing & Verification Explained

- The signing process uses a self-signed certificate created locally. This improves trust for development and internal use but is not trusted by Windows by default.

- For production, purchase an official code signing certificate from providers such as:

  - DigiCert: https://www.digicert.com
  - Sectigo: https://sectigo.com
  - GlobalSign: https://www.globalsign.com

- Official certificates generally cost around $1000 per application.

- The `osslsigncode` tool signs your executable and attaches a timestamp, which helps Windows verify the authenticity and integrity of your app.

- Verification checks that the executable's signature matches the CA and certificate you created or purchased.

## License

MIT
