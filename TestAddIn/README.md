TestAddIn — Autodesk Inventor 2026 AddIn

Overview

`TestAddIn` is an educational/sample Autodesk Inventor AddIn project targeting .NET 8 (net8.0-windows). It implements the standard `Inventor.ApplicationAddInServer` pattern and includes the required project metadata, COM references, and a post-build step that installs the AddIn files into Inventor's AddIns folders.

Repository layout (project folder: `TestAddIn`)

- `TestAddIn.csproj` — MSBuild project file. Key settings:
  - `TargetFramework`: `net8.0-windows`
  - `OutputType`: `Library`
  - `UseWindowsForms`: `true`
  - `GenerateAssemblyInfo`: `false` (assembly attributes are provided in `AssemblyInfo.cs`)
  - Custom `PropertyGroup` entries for `Debug` and `Release` (base address, file alignment, code analysis rule set).
  - References: `System`, and `Autodesk.Inventor.Interop` (HintPath: `C:\Program Files\Autodesk\Inventor 2026\Bin\Autodesk.Inventor.Interop.dll`).
  - COM reference: `stdole` (tlbimp wrapper, `EmbedInteropTypes` = true).
  - Post-build `Target` named `PostBuild` (runs after `PostBuildEvent`) that copies the compiled DLL, the `.addin` registration XML, and the manifest to the AddIns folders for All Users and the Current User.

- `StandardAddInServer.cs` — Primary add-in server class. Typical contents:
  - Namespace: `TestAddIn`
  - Class: `StandardAddInServer` implementing `Inventor.ApplicationAddInServer`.
  - Field: `m_inventorApplication` (type `Inventor.Application`) to hold the running Inventor instance.
  - Methods: `Activate`, `Deactivate`, `ExecuteCommand` (legacy), and `Automation` property.
  - Class GUID used to identify the COM class and mapped in the `.addin` file.

- `AssemblyInfo.cs` — Assembly attributes and COM GUID for the AddIn. Example attributes include `AssemblyTitle`, `AssemblyDescription`, `AssemblyCompany`, `AssemblyVersion`, and `Guid`.

- `TestAddIn.X.manifest` — Application manifest used for runtime/COM settings (included as `None` in the project and referenced via `ApplicationManifest`).

- `Autodesk.TestAddIn.Inventor.addin` — AddIn registration XML. Contains the `ClassId`, `Assembly` name (e.g. `TestAddIn.dll`), `LoadOnStartUp` and other flags Inventor reads to load the extension.

- `Readme.txt` — Optional project readme (this file exists in the project root). This `README.md` is an expanded documentation version.

Post-build install behavior

The project contains an MSBuild `PostBuild` `Target` that executes a command to:
- Ensure the target folders exist:
  - All Users: `C:\ProgramData\Autodesk\Inventor 2026\Addins`
  - Current User: `%APPDATA%\Autodesk\Inventor 2026\Addins`
- Copy the compiled assembly `$(TargetPath)`, the `.addin` file, and the manifest into both locations using `xcopy`.

This makes the AddIn available to Inventor for all users or the current user depending on the folder.

Notes and tips

- The project references the Inventor interop assembly using an absolute hint path. On developer machines with Inventor installed to a different location you may need to update the `HintPath` in `TestAddIn.csproj`.
- `GenerateAssemblyInfo` is disabled so that the project uses `AssemblyInfo.cs` for version and COM GUID control.
- The AddIn `ClassId` and assembly GUID are the mechanism Inventor uses to match the registered AddIn to the implementing class. Keep them synchronized between `AssemblyInfo.cs` and the `.addin` file.
- `RegisterForComInterop` is set to `false` in this project; the deployment model relies on copying files and Inventor loading the .NET AddIn rather than using Visual Studio COM registration.

How to install locally for testing

1. Build the project. The `PostBuild` target will try to copy the files into the standard AddIns folders. Administrator privileges may be required to write to `C:\ProgramData`.
2. Start Autodesk Inventor 2026. The AddIn should be loaded on startup if `LoadOnStartUp` is `true` in the `.addin` file. Use the Add-Ins dialog in Inventor to enable/disable or unload the AddIn.

License and attribution

This repository is an educational/sample project intended to demonstrate the structure of an Inventor AddIn. Copyright metadata in `AssemblyInfo.cs` may identify Autodesk or the project author.

If you want the JSON description converted into a file instead (for machine consumption) let me know and I will add `ProjectDescription.json` to the project.
