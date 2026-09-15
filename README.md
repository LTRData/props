# props

Shared Visual C++/MSBuild property sheets used by LTRData's Windows projects. They capture common include/library settings and legacy compiler, CRT, and operating-system compatibility settings.

This repository contains build configuration only. There is no standalone application, solution, or library to build; the sheets take effect when imported by a consuming `.vcxproj`.

## Property sheets

| File | Settings |
| --- | --- |
| [winstrct.props](winstrct.props) | Adds `../include` and `../lib/$(PlatformTarget)`, selects warning level 4, treats warnings as errors, and disables warning 4201. |
| [wdk7.props](wdk7.props) | Adds WDK 7 headers and CRT/Windows 7 library paths, selects the STL70 headers and compatibility macros, disables C++ exception handling, links `msvcprt.lib`, and sets linker minimum version 5.02. |
| [wdk7_libs.props](wdk7_libs.props) | Adds the WDK 7 and associated VC/SDK library search paths, plus `_CRT_SECURE_NO_WARNINGS`. |
| [wdk7_secondary.props](wdk7_secondary.props) | Uses WDK 7/STL60 headers, x64 VC/Windows SDK library-path macros, CRT function-name mappings, disabled exception handling, and linker minimum version 5.02. |
| [wdk7_w2k_x86.props](wdk7_w2k_x86.props) | Windows 2000 CRT compatibility object for x86, `BufferoverflowU.lib`, disabled manifest generation and buffer security checks, `/d2noftol3`, and a post-build console subsystem version change to 4.00. |
| [wdk7_wxp_x86.props](wdk7_wxp_x86.props) | Windows XP CRT compatibility object for x86, Unicode and `_WIN32_WINNT=0x0501` definitions, disabled manifest generation, and a post-build console subsystem version change to 4.00. |
| [wdk7_wnet_x86.props](wdk7_wnet_x86.props) | Windows Server 2003 CRT compatibility object for x86, Unicode and `_WIN32_WINNT=0x0502` definitions, and disabled manifest generation. |
| [wdk7_wnet_x64.props](wdk7_wnet_x64.props) | Windows Server 2003 CRT compatibility object for AMD64 and disabled buffer security checks. |
| [vs2013_sse.props](vs2013_sse.props) | Sets enhanced instruction-set selection to `NoExtensions`, basic runtime checks to `Default`, and buffer security checks to false. |
| [clrpure.props](clrpure.props) | Legacy pure managed C++ compilation and pure IL linking, with a post-build `CorFlags.exe /32BIT-` command for non-library outputs. |
| [compexch32.props](compexch32.props) | Adds `../lib/compexch.lib` to linker dependencies. |
| [dbglibs.props](dbglibs.props) | Excludes `msvcrt.lib` from default-library linking. |

## Using the sheets

Import the required files through Visual Studio's C++ Property Manager or the project's `PropertySheets` import group. For example, with `props` checked out beside the consuming project directory:

```xml
<ImportGroup Label="PropertySheets">
  <Import Project="..\props\winstrct.props" />
</ImportGroup>
```

Adapt the import path and select sheets for the intended project configuration and architecture. Existing LTRData projects may use older relative layouts or absolute paths; for example, [fdf.vcxproj](https://github.com/LTRData/fdf/blob/master/fdf.vcxproj) imports shared sheets from both a parent directory and machine-specific locations.

The paths inside the sheets also need to match the consuming build:

- `winstrct.props` expects sibling `include` and `lib` directories relative to the project. Headers are available in [LTRData/include](https://github.com/LTRData/include), with related support sources in [LTRData/libsrc](https://github.com/LTRData/libsrc). Compiled libraries are not included here.
- The WDK sheets hard-code `C:\WINDDK\7600.16385.1`; some also refer to Windows SDK 7.1A paths or macros and older VC directory layouts. Install the corresponding dependencies or adapt the paths and settings for the consuming build.
- Check `PlatformTarget` and the actual library-directory names. Architecture-specific sheets contain fixed `i386` or `amd64` object paths; `wdk7_secondary.props` uses x64 library macros.
- `clrpure.props` requires a compiler that supports pure managed C++ and `CorFlags.exe` at `$(SDK40ToolsPath)`. The Windows 2000/XP sheets run `editbin` after building.

Import order matters: some values inherit earlier settings, while others replace them, including compiler options and post-build commands. Review the effective project settings when combining sheets.

## Compatibility

These sheets were published in 2023 and preserve older build environments. Their names describe the intended settings; they do not establish that a consuming program runs on those Windows versions. Compatibility also depends on the compiler, runtime, linked libraries, and APIs used by that program.

Several sheets disable compiler buffer security checks, exception handling, or manifest generation, and some modify the output's subsystem metadata. Choose them deliberately for the legacy configuration being built.

## License

[MIT License](LICENSE), copyright Olof Lagerkvist.
