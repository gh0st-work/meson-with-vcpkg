# meson-with-vcpkg
Example vcpkg packages available from meson

## Linux
Add to `project` `default_options`:
```
'pkg_config_path='+run_command('sh', '-c', 'echo $(locate /lib/pkgconfig | grep -F installed | grep -v -F debug | grep -v -F ".pc")').stdout().strip()
```
Like in the example:
```
project(
  'coolproj', 'cpp', version: '0.0.1', 
  default_options : [
    'warning_level=3', 
    'cpp_std=c++23', 
    'optimization=3',
    'pkg_config_path='+run_command('sh', '-c', 'echo $(locate /lib/pkgconfig | grep -F installed | grep -v -F debug | grep -v -F ".pc")').stdout().strip()
  ],
)

...
```

## Windows
Just hardcode path to `[]/vcpkg/installed/[]/pkgconfig.exe` or use `python` like here (not tested, kinda pseudo-code):
```
run_command(import('python3').find_python(), '-c', 'import os; from pathlib import Path; from subprocess import check_output; from pathlib import Path; vcpkg_dir = Path(check_output("where vcpkg", shell=True).decode().strip()).parents[0]; pkgconfig = [os.path.join(root, name) for root, dirs, files in os.walk(vcpkg_dir) for name in files if name == "pkgconfig.exe" and root.contains("installed") and not root.contains("debug")]; print(pkgconfig[0]);')
```
