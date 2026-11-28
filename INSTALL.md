# Install and run StuLang 26.40.0

## Windows executable (no Node.js required)

Download `stu.exe` from the **stu-windows-x64** artifact of the latest successful [Build Windows CLI](../.github/workflows/windows-cli.yml) run, or from a GitHub release when one is published. Save it in a folder on your `PATH`, such as `%LOCALAPPDATA%\\StuLang`, add that folder to `PATH`, then open a new terminal:

```powershell
stu --version
stu check path\\to\\hello.stu
stu run path\\to\\program.stu
```

This is a portable single-file command line program for 64-bit Windows. It includes its runtime; it does not need Node.js or the StuLang repository. To build it yourself, use `npm run package:win` on the repository (the packaging step requires Node.js and an internet connection).

## Setup

Install Node.js (current LTS recommended; CLI minimum 18) and download or clone this repository. Open a terminal in the folder containing `package.json`:

```sh
npm ci
npm link
STU --version
STU --help
```

`npm link` installs the `STU` command globally as a link to this project folder, so keep the folder on your computer. Node.js is required to use the command. No compilation of the tools is required. On Windows, use `npm.cmd` instead of `npm` if PowerShell blocks npm scripts; if it blocks `STU.ps1`, use `STU.cmd` instead of `STU`.

## Inspect a program

```sh
STU check examples/hello.stu
STU tokens examples/hello.stu
STU ast examples/calculator.stu
STU ast examples/coinflip.stu
```

Replace the example path with your own `.stu` file. Quote paths containing spaces. These commands inspect syntax; they do not execute programs.

## Run a program

Run directly from the terminal:

```sh
STU run examples/coinflip.stu
STU run examples/calculator.stu --port 3000 --no-open
```

`run` serves the program on localhost and opens your default browser. It chooses a free port unless you supply `--port`. Use `--no-open` to open the printed URL yourself. Press Ctrl+C to stop; restart after editing the source.

Compile a standalone HTML file to open in a browser or share:

```sh
STU compile examples/coinflip.stu
STU build examples/calculator.stu -o dist/calculator.html
```

`build` is an alias for `compile`. The default output is `dist/<source-name>.html`; `-o` or `--output` selects another HTML path. Parent folders are created and existing output files are replaced. The HTML includes its JavaScript and styles and needs no server or dependencies to run.

The existing `stulang` command also works. Without `npm link`, use `node main.js` in place of `STU` from the project folder. Run `STU --help` for all commands. These commands use the same supported subset as the VS Code preview, not a native executable compiler. Unsupported syntax fails compilation with a diagnostic.

The preview runtime supports variables, constants, functions, returns, arrays, arithmetic and comparisons, `Random.Choice`, named-variable interpolation, views, visual objects, editable text, `Clicked` events, built-in presets, layout and CSS properties, and browser `JS.Run_JS` blocks. See the [complete language reference](LANGUAGE.md) for the parser and preview feature matrix.
The existing `stulang` command also works. Without `npm link`, use `node main.js` in place of `STU` from the project folder. Run `STU --help` for all commands. These commands use the same supported subset as the VS Code preview, not a native executable compiler. Unsupported syntax fails compilation with a diagnostic.

## Run in VS Code

Install VS Code 1.85 or newer. From the repository root:

```sh
npm run extension:package
code --install-extension stulang-26.40.0.vsix --force
```

Packaging copies the current toolchain and icon, installs extension dependencies, and downloads the VS Code packaging tool using `npx`. Internet access and a current Node.js LTS release are recommended.

Alternatively, open the Command Palette and use **Extensions: Install from VSIX...** to select the generated package. Reload if prompted. To replace the old local 2.0.0 extension, use `--force` or uninstall it first.

1. Open [calculator.stu](../examples/calculator.stu) in VS Code.
2. Click the play button or choose **StuLang: Run File**.
3. Enter two numbers in the preview and choose **Add**, **Subtract**, **Multiply**, or **Divide**. With the initial values `10` and `20`, **Add** displays `30`.
4. Edit and run again to restart the preview.

The shortcut is **Ctrl+F5** on Windows/Linux and **Cmd+F5** on macOS. Unsaved editor changes are included. Try [hello.stu](../examples/hello.stu) and [coinflip.stu](../examples/coinflip.stu) as well.

## Troubleshooting

| Problem                   | Solution                                                                                                  |
| ------------------------- | --------------------------------------------------------------------------------------------------------- |
| `node` or `npm` not found | Install Node.js and reopen your terminal.                                                                 |
| `STU` not found           | Run `npm link` in the project folder and ensure the npm global bin directory is on your PATH.             |
| Cannot find `acorn`       | Run `npm ci` in the repository root.                                                                      |
| `code` not found          | Install through **Extensions: Install from VSIX...**.                                                     |
| No play button            | Confirm the extension is installed and the editor language is StuLang. Reload VS Code.                    |
| Syntax error              | Follow the reported line and column and compare with the [reference](LANGUAGE.md).                        |
| Preview error             | Check the supported subset in the [README](../README.md). Syntax validity does not imply runtime support. |

Commit source, documentation, examples, and both lockfiles. Dependency folders, generated toolchain files, and VSIX packages are ignored. Attach a freshly built VSIX separately to a GitHub release if desired.
