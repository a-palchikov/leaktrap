  * Download [Detours library](http://research.microsoft.com/sn/detours/), follow the readme steps to build it (at least you will need the import library `detours.lib`, the detoured indicator `detoured.dll` and the `setdll` example compiled.)
  * You could also compile the leaktrap project (the VC71 project file is inside the source code distribution) -> `leaktrap_vc71.dll`
  * You could also compile the leakdbg debugger extension (Windows DDK makefile is inside the source code distribution) -> `leakdbg.dll`
  * Alternatively simply grab the binary distribution, which has been compiled using the Visual Studio 2003 (`leaktrap_vc71.dll`) and DDK version 1.1.6001.000 (`leakdbg.dll`) plus additional binary extras archive that contains the vc71 runtime libraries and precompiled detours binaries
  * Copy `leaktrap_vc71.dll`, `leaktrap_vc71.pdb` (<b>very important for debugger extension to work</b>!!), `detoured.dll`, and `setdll.exe` to the folder of your choice (usually the binary folder for your application).
  * Configure your application's executable to load the leaktrap client library by binding it as its first import:
```
  setdll.exe /d:LeakTrap_vc71.dll yourapp.exe
```
  * Make sure you have the [Windows Debugging Tools package](http://www.microsoft.com/whdc/devtools/debugging/installx86.mspx)
  * Copy `leakdbg.dll` to your Windows Debugging Tools package's extensions folder (usually winxp, for windows xp systems and later)
  * If you're getting `LoadLibrary(leakdbg) failed. Win32 error 0n2` errors in debugger, you need to fix the extension path:
```
  .extpath+ path_to_leakdbg.dll
```
  * Fix the path to your application's pdb files by issuing:
```
  .sympath+ path_to_pdb_files
  .reload
```
> prior to using the leak tracker. If you're not sure which modules you need .pdb files for, watch out for debugger messages:
```
*** ERROR: Symbol file could not be found.  Defaulted to export symbols for C:\blah-blah\yourmodule1.dll -
```
> Once you know the name of the module, fix its symbols:
```
  .sympath+ path_to_yourmodule1.pdb
  .reload /f yourmodule1.dll
```
  * **You're all set**

**A word of caution**: any time an exported (system) API that is to be detoured is not found or stack capture routine is not available (should not happen on systems with Windows XP and later!), the client library will return `FALSE` from its `DllMain` and **will fail** the complete application initialization.

System requirements:
  * Windows XP and later (since it relies on a system stack capture routine available in Windows XP - _it could be written manually though and thus made available to w2k_)
  * Since the client library has been compiled with VC71, its runtime is also a requirement (msvcr71.dll + msvcp71.dll) (if you do not have vc71 runtime, download bin\_extras archive from Downloads section!)
  * With the update, it is now usable on Windows 7

**Important**
> You need to have proper .pdb files for all modules you're inspecting for resource leaks. It is also important that these modules be compiled without frame pointer optimizations (**-Oy-** switch for visual studio) as the used stack trace routine is very simple and relies solely on proper function frames to walk the stack!