# meson-with-vcpkg
Example vcpkg packages available from meson

## Example
- Add to `project` `default_options`:
  ```
  'pkg_config_path='+run_command(import('python3').find_python(), '-c', '''
  import platform;
  from subprocess import check_output;
  if platform != "Windows":
      print(check_output("""locate /lib/pkgconfig | grep -F installed | grep -v -F debug | grep -v -F ".pc""""))
  else:
      import os;
      from pathlib import Path;
      from pathlib import Path;
      vcpkg_dir = Path(check_output("where vcpkg", shell=True).decode().strip()).parents[0];
      pkgconfig = [
          os.path.join(root, name)
          for root, dirs, files in os.walk(vcpkg_dir)
          for name in files
          if name == "pkgconfig.exe" and root.contains("installed") and not root.contains("debug")
      ];
      print(pkgconfig[0]);
  ''').stdout().strip()
  ```
  Like in the example:
  ```
  project(
    'coolproj', 'cpp', version: '0.0.1', 
    default_options : [
      'warning_level=3', 
      'cpp_std=c++23', 
      'optimization=3',
  
      'pkg_config_path='+run_command(import('python3').find_python(), '-c', '''
  import platform;
  from subprocess import check_output;
  if platform != "Windows":
      print(check_output("""locate /lib/pkgconfig | grep -F installed | grep -v -F debug | grep -v -F ".pc""""))
  else:
      import os;
      from pathlib import Path;
      from pathlib import Path;
      vcpkg_dir = Path(check_output("where vcpkg", shell=True).decode().strip()).parents[0];
      pkgconfig = [
          os.path.join(root, name)
          for root, dirs, files in os.walk(vcpkg_dir)
          for name in files
          if name == "pkgconfig.exe" and root.contains("installed") and not root.contains("debug")
      ];
      print(pkgconfig[0]);
  ''').stdout().strip()
  
    ],
  )
  
  ...
  ```
- (Linux) Don't forget to run:
  
  ```
  sudo updatedb
   ```
  Otherwise `locate` may not find `pkgconfig`
- Then re-setup your project settings:
  
  ```
  meson setup build
  ```
- And compile as always:
  
  ```
  meson compile -C build
  ```
