### Basic first steps

1. Clone the repo from github
2. Checkout a new branch based on the release tag desired
3. Open Visual Studio
4. `File`->`Open`->`CMake` and select `CMakeLists.txt` at the root of the clone

It will take a while to run CMake Cache generation. While doing so it will
detect everything for a regular Windows build. This won't work for UWP so
we'll need to manually correct some things.



### Tell CMake to to build for UWP:

* `CMake`->`Change Settings`->`CMakeLists.txt`
  Choose `Release x86`. This will create a CMakeSettings.json which
  we will further edit.
* Change `generator` to `Visual Studio 15 2017`
* Add to `cmakeCommandArgs`: `-DCMAKE_SYSTEM_NAME=WindowsStore -DCMAKE_SYSTEM_VERSION=10.0 -Dnetwork:BOOL=OFF -Dtranscoder=windows -Dxmlch-type=wchar_t`
* From `buildCommandArgs` remove `-v`.
* Duplicate the entire configuration and change
  1. `Release x86` to `Release arm64`
  2. `msvc_x86` to `msvc_arm64`



### Force Xerces to use the posix filemgr
The Windows APIs used for a typical windows build of Xerces-C++ aren't
supported on UWP. Instead, we'll use POSIX apis.

* Open `cmake/XercesFileMgrSelection.cmake`
* Comment out:
  ```
  if(WIN32)
      set(xerces_filemgr_default "windows")
  endif()
  ```

### Disable CRT security warnings and enable C++/CX support
* Open CMakeLists.txt
* Near line 88, add on their own lines:
  1. `add_definitions(-D_CRT_SECURE_NO_WARNINGS)`
  2. `add_definitions(/ZW)`

It would be possible to correct all the CRT security warnings instead of
disabling the warnings. That's beyond the scope of this text.


### Disable test, sample compilation
The samples don't make sense to compile for UWP, and the test harness
doesn't work in a UWP world.

* Open CMakeLists.txt
* Comment out `add_subdirectory(tests)`
* Comment out `add_subdirectory(samples)`



### Remove calls to APIs not allowed in UWP
* UWP doesn't have a notion of "Current Working Directory", so we remove
  all code that depends on that. This will cause the library to fail when
  passed a relative directory. Always use absolute paths when using the
  library with UWP.
* UWP doesn't allow applications to access the registry. Much of the
  encoding translation support in Xerces depends on reading information from
  the registry. Instead we simply remove all the translation support that is
  not natively implemented in Xerces. This means you won't be able to use
  any encoding besides UTF-16 and UTF-8. These days it should be rare to
  encounter another encoding.

See the commit for specific details of the changes.

