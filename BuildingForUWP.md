### Building the artifacts

1.  Clone the repo from github
2.  Checkout the `UWP` branch.
3.  Rebase the branch on top of the latest release tag if desired.
4.  Launch Visual Studio (ensure you have CMake integration installed)
5.  `File`->`Open`->`CMake` and select `CMakeLists.txt` from the root of the clone.
6.  Wait for CMake to finish project construction.
7.  `CMake`->`Install`->`xerces-c`
8.  Find the output at `~/CmakeBuilds/{GUID}/install/x86-Release/`
9.  Change the configuration to `arm64-Release`
10. `CMake`->`Install`->`xerces-c`
11. Find the output at `~/CmakeBuilds/{GUID}/install/arm64-Release/`

### Packaging for Nuget:
You need the following artifacts:

* grab all of `include/` recursively, hierarchy preserved
* from `bin/` grab `xerces-c_3_2.dll` (for each platform)
* from `lib/` grab `xerces-c_3.lib`   (for each platform)




