# Mesa - The 3D Graphics Library - git version, multilib with userpatches support

https://gitlab.freedesktop.org/mesa/mesa

You can customize key features in the `customization.cfg` file such as :
- Selecting which LLVM backend version to use (respecting Lone_Wolf's MESA_WHICH_LLVM variable values)
- Enabling/disabling compilation of the lib32 package
- Change userpatches behaviour

To not edit `customization.cfg` directly, create a new file `override.cfg` and set variables there.

You can use your own mesa patches by putting them in the userpatches folder and giving them the .patch extension.
You can also revert mesa patches by putting them in the userpatches folder and giving them the .revert extension.

Note about Lone_Wolf's MESA_WHICH_LLVM for clean chroot users using an aur helper: Selecting AUR's llvm-git as the LLVM backend in a clean chroot will fail to find the package due to naming. Two workarounds can be used: installing llvm-git beforehand (that will provide the `aur-llvm-git` dependency required), or selecting LordHeavy's llvm-git option instead (that will actually install AUR's llvm-git package unless you have LordHeavy's package installed in your chroot, due to the package names being the same).


```
git clone https://github.com/Frogging-Family/mesa-git.git
cd mesa-git
makepkg -si
```

