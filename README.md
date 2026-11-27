## StuLang

Welcome to the very first release of **StuLang**! StuLang is an easier way to build websites without all the repetitive boilerplate code. It transpiles directly into clean **HTML**, letting you build the sites you love faster and with less friction.

* **Documentation:** Check out [`LANGUAGE.md`](LANGUAGE.md) to learn the syntax.
* **Video Tutorial:** Watch the walkthrough on the [TechnicallyStupidest YouTube channel](https://youtube.com) *(yayyy)*.
* **Transparency Note:** AI helped create StuLang and its documentation *(wow, boo)*.

### Want to Contribute?
---
I would love some advice on if i should make it open source or not. I want control but i can't do this by myself. I am also just bad at coding so i might need some help but I want this to be useful so uhh pls help
### Quick Install

1. **CLI (`stu.exe`):** Download `stu.exe` from the **Assets** section below and run it directly in your terminal (no Node.js installation required):
   ```powershell
   .\stu.exe --version
   .\stu.exe run examples\coinflip.stu
   ```
   *(Note: If Windows Defender SmartScreen appears on first launch, click **More info → Run anyway**.)*

2. **VS Code Extension (`stulang-26.40.0.vsix`):** Download `stulang-26.40.0.vsix` from **Assets**, open VS Code, press `Ctrl+Shift+P`, select **Extensions: Install from VSIX...**, and choose the downloaded file.

---

### Versioning & Support Lifecycle (`YY.[Quarter][QoL].[BugFix]`)

StuLang follows a predictable quarterly release schedule shipping every **February**, **May**, **August**, and **November**:

* **`26` (Year):** The two-digit release year (`2026`).
* **`4` (Quarter & Support Tier):**
  * **Q1 (Feb), Q2 (May), Q3 (Aug) — Medium-Term Support (MTS):** Supported with updates and fixes for **2 years**.
  * **Q4 (November) — Long-Term Support (LTS):** Supported for **4 years**, plus a **1-year grace period** for critical/major bug fixes (5 years total) so teams never face a sudden cutoff.
  * *(Note: StuLang is maintained by a solo developer on a best-effort basis, so patches and updates may take a little time to roll out.)*
* **`0` (Quality-of-Life Updates):** Increments (`26.41.0`, `26.42.0`, etc.) whenever backward-compatible polish, developer-experience improvements, or QoL upgrades are shipped.
* **`.0` (Bug Fixes):** Increments (`26.40.1`, `26.40.2`, etc.) strictly for bug fixes and stability patches.
