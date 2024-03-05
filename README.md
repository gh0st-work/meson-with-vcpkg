# meson-with-vcpkg
**Make vcpkg packages available from meson - cross-platform example**
- Tested on Linux
- Tested on Windows

## Example
- Add this option to `project` `default_options`:
  ```meson
  'pkg_config_path=' + run_command('python3', '-c', '''
  import platform;
  from subprocess import check_output;
  import os;
  from pathlib import Path;
  vcpkg_dir = Path(check_output("where vcpkg" if platform.system() == "Windows" else "which vcpkg", shell=True).decode().strip()).resolve().parents[0];
  pkgconfig = [
      os.path.join(root, name)
      for root, dirs, files in os.walk(vcpkg_dir)
      for name in dirs 
      if name == "pkgconfig" and "installed" in root and "debug" not in root
  ];
  print(pkgconfig[0]);
  ''').stdout().strip()
  ```
  Like in the example:
  ```meson
  project(
    'coolproj', 'cpp', version: '0.0.1', 
    default_options : [
      'warning_level=3', 
      'cpp_std=c++23', 
      'optimization=3',
      
      'pkg_config_path=' + run_command('python3', '-c', '''
  import platform;
  from subprocess import check_output;
  import os;
  from pathlib import Path;
  vcpkg_dir = Path(check_output("where vcpkg" if platform.system() == "Windows" else "which vcpkg", shell=True).decode().strip()).resolve().parents[0];
  pkgconfig = [
      os.path.join(root, name)
      for root, dirs, files in os.walk(vcpkg_dir)
      for name in dirs 
      if name == "pkgconfig" and "installed" in root and "debug" not in root
  ];
  print(pkgconfig[0]);
  ''').stdout().strip()
  
    ],
  )
  
  ...
  ```
  
- (Windows) Don't forget to install `pkg-config.exe` if it is not installed:
  
  ```
  choco install pkgconfiglite
  ```
  
- Then re-`setup` your project:
  
  ```
  meson setup build --wipe
  ```
- And `compile` as always:
  
  ```
  meson compile -C build
  ```
