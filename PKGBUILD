# Originally based on AUR pkgbuilds from Lone_Wolf <lonewolf at xs4all dot nl> - https://aur.archlinux.org/packages/mesa-git/ - https://aur.archlinux.org/packages/lib32-mesa-git/
# Glitched by: Tk-Glitch <ti3nou at gmail dot com>

plain '       .---.`               `.---.'
plain '    `/syhhhyso-           -osyhhhys/`'
plain '   .syNMdhNNhss/``.---.``/sshNNhdMNys.'
plain '   +sdMh.`+MNsssssssssssssssNM+`.hMds+'
plain '   :syNNdhNNhssssssssssssssshNNhdNNys:'
plain '    /ssyhhhysssssssssssssssssyhhhyss/'
plain '    .ossssssssssssssssssssssssssssso.'
plain '   :sssssssssssssssssssssssssssssssss:'
plain '  /sssssssssssssssssssssssssssssssssss/'
plain ' :sssssssssssssoosssssssoosssssssssssss:'
plain ' osssssssssssssoosssssssoossssssssssssso'
plain ' osssssssssssyyyyhhhhhhhyyyyssssssssssso'
plain ' /yyyyyyhhdmmmmNNNNNNNNNNNmmmmdhhyyyyyy/'
plain '  smmmNNNNNNNNNNNNNNNNNNNNNNNNNNNNNmmms'
plain '   /dNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNd/'
plain '    `:sdNNNNNNNNNNNNNNNNNNNNNNNNNds:`'
plain '       `-+shdNNNNNNNNNNNNNNNdhs+-`'
plain '             `.-:///////:-.`'

_where="$PWD" # track basedir as different Arch based distros are moving srcdir around
source "$_where"/customization.cfg

# Load variables from override configuration file if present.
if [ -e "$_where/override.cfg" ]; then
  source "$_where/override.cfg" && msg2 "override.cfg config loaded"
elif [ -e "$CONFIG_OVERRIDE_PATH" ]; then
  source "$CONFIG_OVERRIDE_PATH" && msg2 "External override file $CONFIG_OVERRIDE_PATH will be used to override customization.cfg values.\n"
fi

# custom mesa commit to pass to git
if [ -n "$_mesa_commit" ]; then
  _mesa_version="#commit=${_mesa_commit}"
else
  _mesa_version="#branch=${_mesa_branch}"
fi

_mesa_srcdir="mesa"
_rusticl="false"
_makepkg_options=()
_depends32=()
_depends64=(
    'libdrm'
    'libxxf86vm'
    'libxdamage'
    'libxshmfence'
    'libelf'
    'libunwind'
    'libglvnd'
    'wayland'
    'lm_sensors'
    'vulkan-icd-loader'
    'zstd'
    'expat'
    'libxfixes'
    'libx11'
    'systemd-libs'
    'libxext'
    'libxcb'
    'glibc'
    'zlib'
    'python'
    'xcb-util-keysyms'
)

pkgname=('mesa-tkg-git')
pkgdesc="Mesa 3D graphics library, git version"
pkgver=0
pkgrel=1
arch=('x86_64')
url="https://www.mesa3d.org"
license=('custom')
makedepends=(
    'git'
    'xorgproto'
    'libxml2'
    'libvdpau'
    'libva'
    'elfutils'
    'libxrandr'
    'meson'
    'ninja'
    'glslang'
    'directx-headers'
    'python-mako'
    'python-ply'
    'cbindgen'
    'wayland-protocols'
    'python-packaging'
    'python-pyaml'
)
optdepends=('opengl-man-pages: for the OpenGL API man pages')
source=("mesa::git+${_mesa_source}${_mesa_version}"
        'LICENSE'
        'llvm32.native'
)
sha256sums=('SKIP'
            '7fdc119cf53c8ca65396ea73f6d10af641ba41ea1dd2bd44a824726e01c8b3f2'
            '3ea259740141b862e152e07c58f05cad539680541dc181a7233be0c93414e6fb')

if [[ "$_compiler" == "gcc" ]]; then
  _depends64+=('gcc-libs')
fi

if pacman -Qq ccache &> /dev/null; then
  msg2 "ccache was found and will be used\n"
  _makepkg_options+=('ccache')
fi

if [[ "$_lto" == "false" ]]; then
  _makepkg_options+=('!lto')
fi

if [[ "$_additional_meson_flags" =~ "--buildtype debug" ]]; then
  _makepkg_options+=('debug !strip')
else
  _makepkg_options+=('!debug strip')
fi

options=(${_makepkg_options[@]})

if [[ "$_lib32" == "true" ]]; then
  _depends32=(
      'lib32-libdrm'
      'lib32-wayland'
      'lib32-libxxf86vm'
      'lib32-libxdamage'
      'lib32-libxshmfence'
      'lib32-libelf'
      'lib32-libunwind'
      'lib32-lm_sensors'
      'glslang'
      'lib32-vulkan-icd-loader'
      'lib32-zstd'
      'lib32-libxcb'
      'lib32-libxfixes'
      'lib32-expat'
      'lib32-libxext'
      'lib32-libx11'
      'lib32-zlib'
      'lib32-glibc'
      'lib32-spirv-tools'
  )

  if [[ "$_compiler" == "gcc" ]]; then
    _depends32+=('lib32-gcc-libs')
  fi

  pkgname+=('lib32-mesa-tkg-git')
  makedepends+=(
      'lib32-libxml2'
      'lib32-libvdpau'
      'lib32-libglvnd'
      'lib32-libva'
      'lib32-libxrandr'
  )
fi

# NINJAFLAGS is an env var used to pass commandline options to ninja
# NOTE: It's your responbility to validate the value of $NINJAFLAGS. If unsure, don't set it.

# MESA_WHICH_LLVM is an environment variable that determines which llvm package tree is used to built mesa-git against.
# Adding a line to ~/.bashrc  that sets this value is the simplest way to ensure a specific choice.
#
# 1: llvm-minimal-git (aur) preferred value
# 2: AUR llvm-git
# 3: llvm-git from LordHeavy unofficial repo 
# 4  llvm (stable from extra) Default value

if [[ ! $MESA_WHICH_LLVM ]] && [ ! -e "$_where"/frogminer ]; then
  plain "Which llvm package tree do you want to use to build mesa-tkg-git against ?"
  read -rp "`echo $'     1.llvm-minimal-git (AUR)\n     2.llvm-git (AUR)\n     3.llvm-git from LordHeavy unofficial repo\n   > 4.llvm (default)\n    choice[1-4?]: '`" MESA_WHICH_LLVM;
  touch "$_where"/frogminer
fi

case $MESA_WHICH_LLVM in
    1)
        msg2 "Using llvm-minimal-git (AUR)"
        echo "Using llvm-minimal-git (AUR)" >> "$_where"/last_build_config.log

        _depends64+=(
            'llvm-libs-minimal-git'
            'spirv-llvm-translator-minimal-git'
            'libclc-minimal-git'
            'clang-libs-minimal-git'
            'clang-opencl-headers-minimal-git'
            'spirv-tools'
        )
        _rusticl="true"

        makedepends+=(
            'llvm-minimal-git'
            'libclc-minimal-git'
            'spirv-llvm-translator-minimal-git'
            'clang-minimal-git'
            'clang-opencl-headers-minimal-git'
            'rust'
            'rust-bindgen'
            'spirv-tools'
        )

        if [[ "$_lib32" == "true" ]]; then
          _depends32+=(
            'lib32-llvm-libs-minimal-git'
            'lib32-spirv-tools'
          )

          makedepends+=(
            'lib32-llvm-minimal-git'
            'lib32-spirv-llvm-translator-minimal-git'
            'lib32-clang-libs-minimal-git'
            'clang-minimal-git'
            'clang-opencl-headers-minimal-git'
            'lib32-clang-opencl-headers-minimal-git'
          )
        fi
      ;;
    2) # aur llvm-git, depending on aur-llvm-* to avoid mixup with LH llvm-git
        msg2 "Using llvm-git (AUR)"
        echo "Using llvm-git (AUR)" >> "$_where"/last_build_config.log

        _depends64+=('aur-llvm-libs-git')

        makedepends+=(
            'aur-llvm-git'
            'libclc-git'
            'spirv-llvm-translator-git'
            'clang-git'
            'clang-opencl-headers-git'
        )
        optdepends+=('aur-llvm-git: opencl')

        if [[ "$_lib32" == "true" ]]; then
          _depends32+=('aur-lib32-llvm-libs-git')

          makedepends+=('aur-lib32-llvm-git')
        fi
      ;;
    3) # mesa-git/llvm-git (lordheavy unofficial repo)
        msg2 "Using llvm-git from LordHeavy unofficial repo"
        echo "Using llvm-git from LordHeavy unofficial repo" >> "$_where"/last_build_config.log

        _depends64+=('llvm-libs-git')

        makedepends+=(
            'llvm-git'
            'clang-git'
            'libclc-git'
            'spirv-tools'
            'spirv-llvm-translator-git'
        )
        optdepends+=('clang-git: opencl' 'compiler-rt: opencl')

        if [[ "$_lib32" == "true" ]]; then
          _depends32+=('lib32-llvm-libs-git')

          makedepends+=('lib32-llvm-git')
        fi
      ;;
    *) # extra/llvm
        msg2 "Using llvm (default)"
        echo "Using llvm (default)" >> "$_where"/last_build_config.log

        _depends64+=(
            'llvm-libs=19.1.7'
            'clang'
            'libclc'
            'spirv-llvm-translator'
            'spirv-tools'
        )
        _rusticl="true"

        makedepends+=(
            'llvm=19.1.7'
            'clang=19.1.7'
            'libclc'
            'spirv-llvm-translator'
            'spirv-tools'
            'rust'
            'rust-bindgen'
        )

        if [[ "$_lib32" == "true" ]]; then
          _depends32+=(
            'lib32-clang'
            'lib32-llvm-libs=1:19.1.7'
            'lib32-spirv-llvm-translator'
            'lib32-spirv-tools'
          )

          makedepends+=(
            'lib32-llvm=1:19.1.7'
            'lib32-spirv-llvm-translator'
            'lib32-spirv-tools'
          )

        fi
    ;;
esac

depends=(${_depends64[@]} ${_depends32[@]})

function exit_cleanup {
  if [[ "$pkgver" != "0" ]]; then
    sed -i "s/pkgver=$pkgver.*/pkgver=0/g" "$_where"/PKGBUILD
  fi

  # Remove temporarily copied patches
  sleep 1 # Workarounds a race condition with ninja
  rm -rf "$_where"/*.patch
  rm -f "$_where"/frogminer

  remove_deps
  msg2 "Cleanup done"
}

user_patcher() {
  # To patch the user because all your base are belong to us
  local _reverts=("$_where"/*."revert")
  local _patches=("$_where"/*."patch")

  if [ ${#_reverts[@]} -ge 2 ] || [ -e "${_reverts}" ]; then
    if [[ "$_user_patches_no_confirm" != "true" ]]; then
      msg2 "Found ${#_reverts[@]} 'to revert' patches for ${_userpatch_target}:"
      printf '%s\n' "${_reverts[@]//*\//}"
      read -rp "Do you want to apply them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
    fi

    if [ "$_CONDITION" == "y" ] || [ "$_user_patches_no_confirm" == "true" ]; then
      for _f in "${_reverts[@]}"; do
        if [ -e "${_f}" ]; then
          msg2 "######################################################"
          msg2 ""
          msg2 "Reverting your own ${_userpatch_target} patch ${_f}"
          msg2 ""
          msg2 "######################################################"
          patch -Np1 -R < "${_f}"
          echo "Reverted your own patch ${_f}" >> "$_where"/last_build_config.log
        fi
      done
    fi
  fi

  if [ ${#_patches[@]} -ge 2 ] || [ -e "${_patches}" ]; then
    if [[ "$_user_patches_no_confirm" != "true" ]]; then
      msg2 "Found ${#_patches[@]} patches for ${_userpatch_target}:"
      printf '%s\n' "${_patches[@]//*\//}"
      read -rp "Do you want to apply them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
    fi

    if [[ "$_CONDITION" == "y" ]] || [[ "$_user_patches_no_confirm" == "true" ]]; then
      for _f in "${_patches[@]}"; do
        if [ -e "${_f}" ]; then
          msg2 "######################################################"
          msg2 ""
          msg2 "Applying your own ${_userpatch_target} patch ${_f}"
          msg2 ""
          msg2 "######################################################"
          patch -Np1 < "${_f}"
          echo "Applied your own patch ${_f}" >> "$_where"/last_build_config.log
        fi
      done
    fi
  fi
}

pkgver() {
    cd "$_mesa_srcdir"
    _ver=$( cat VERSION )
    echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

prepare() {
    # cleanups
    cd "$srcdir/$_mesa_srcdir"
    git reset --hard HEAD
    git clean -xdf

    if [ -n "$_mesa_commit" ]; then
      git checkout "${_mesa_commit}"
    fi

    msg2 "Tree cleaned"

    # copy userpatches inside the PKGBUILD's dir
    for _file in "$_where"/userpatches/*; do
        [ -e "$_file" ] && cp "$_file" "$_where"
    done

    if [ -n "$_mesa_prs" ]; then
      for _pr in ${_mesa_prs[@]}; do
        wget -O "$_where"/"$_pr".patch https://gitlab.freedesktop.org/mesa/mesa/merge_requests/"$_pr".diff
      done
    fi

    # Community patches
    if [ -n "$_community_patches" ]; then
      if [ ! -d "$_where/../community-patches" ]; then
        cd "$_where/.." && git clone https://github.com/Frogging-Family/community-patches.git && cd "$srcdir"/$_mesa_srcdir
      fi

      _community_patches=($_community_patches)
      for _p in ${_community_patches[@]}; do
        ln -s "$_where"/../community-patches/mesa-git/$_p "$_where"/
      done
    fi

    # mesa user patches
    if [[ "$_user_patches" == "true" ]]; then
      _userpatch_target="mesa"
      echo -e "# Last ${pkgname} ${pkgver} configuration - $(date) :\n" > "$_where"/last_build_config.log
      echo -e "Gallium drivers: ${_gallium_drivers}" >> "$_where"/last_build_config.log
      echo -e "Vulkan drivers: ${_vulkan_drivers}\n" >> "$_where"/last_build_config.log
      user_patcher
    fi

    # Community/prs patches removal
    for _p in ${_community_patches[@]}; do
      rm -f "$_where"/$_p
    done

    for _pr in ${_mesa_prs[@]}; do
      rm -f "$_where"/$_pr.patch
    done

    cd "$srcdir"

    # although removing _build folder in build() function feels more natural,
    # that interferes with the spirit of makepkg --noextract
    if [ -d _build64 ] && [ "$_NUKR" != "false" ]; then
      if [[ "$_additional_meson_flags" = *-Db_pgo=use* ]]; then
        find ./_build64 -type f ! -name '*.gcda' -delete
      else
        rm -rf _build64
      fi
    fi

    if [  -d _build32 ] && [ "$_NUKR" != "false" ]; then
      if [[ "$_additional_meson_flags" = *-Db_pgo=use* ]]; then
        find ./_build32 -type f ! -name '*.gcda' -delete
      else
        rm -rf _build32
      fi
    fi
    cd "$_where"
}

build () {
    if [ -n "$_custom_opt_flags" ]; then
      export CFLAGS="${_custom_opt_flags}"
      export CPPFLAGS="${_custom_opt_flags}"
      export CXXFLAGS="${_custom_opt_flags}"
    fi

    # gallium_vdpau requires either r300, r600, radeonsi or nouveau
    if [[ $_gallium_drivers != *r300* ]] && [[ $_gallium_drivers != *r600* ]] && [[ $_gallium_drivers != *radeonsi* ]] && [[ $_gallium_drivers != *nouveau* ]]; then
      warning "Gallium VDPAU disabled (gallium driver r300, r600, radeonsi or nouveau required)"
      _gallium_vdpau="disabled"
    else
      _gallium_vdpau="enabled"
    fi

    # gallium_va requires either r600, radeonsi or nouveau
    if [[ $_gallium_drivers != *r600* ]] && [[ $_gallium_drivers != *radeonsi* ]] && [[ $_gallium_drivers != *nouveau* ]]; then
      warning "Gallium VA disabled (gallium driver r600, radeonsi or nouveau required)"
      _gallium_va="disabled"
    else
      _gallium_va="enabled"
    fi

    # intel-rt
    if ( cd "$srcdir/$_mesa_srcdir" && git merge-base --is-ancestor 876db9135048be34d84bd74b18411678e15c7c3f HEAD ); then
      if [[ ${_vulkan_drivers[*]} =~ "intel" ]]; then
        _intel_rt="-D intel-rt=enabled"
      else
        _intel_rt="-D intel-rt=disabled"
      fi

      _intel_rt_32="-D intel-rt=disabled"
    fi

    # intel-clc cross compile fix
    if ( cd "$srcdir/$_mesa_srcdir" && git merge-base --is-ancestor 28c1053c07c177854520f6283fa665f17618adb5 HEAD ); then
      if [[ "$_lib32" == "true" ]]; then
         _intel_clc_32="-D intel-clc=system"
        # Workaround cross compilation error by using intel_clc binary from _build64
        export PATH=$srcdir/_build64/src/intel/compiler:${PATH}
      fi
    fi

    if [[ "$_rusticl" == "true" ]]; then
      _rust_std="-D rust_std=2021"
    else
      _rust_std=""
    fi

    if [ -n "${CUSTOM_GCC_PATH}" ] && [ "$_compiler" != "clang" ]; then
      export PATH=$( find "$CUSTOM_GCC_PATH/" -maxdepth 1 -printf "%p:" || ( warning "Custom compiler path seems wrong.." && exit 1 ) )${PATH}
      msg2 "CUSTOM_GCC_PATH = ${CUSTOM_GCC_PATH}"
    fi

    if [[ "$_compiler" == "clang" ]]; then
      export CC="clang"
      export CXX="clang++"
    else
      export CC="gcc"
      export CXX="g++"
    fi

    meson setup $_mesa_srcdir _build64 \
        --wrap-mode=nofallback \
        --force-fallback-for=syn,paste \
        -D android-libbacktrace=disabled \
        -D b_ndebug=true \
        -D b_lto=${_lto} \
        -D egl=enabled \
        -D gallium-drivers=${_gallium_drivers} \
        -D vulkan-drivers=${_vulkan_drivers} \
        -D gallium-extra-hud=true \
        -D gallium-nine=${_gallium_nine} \
        -D gallium-opencl=disabled \
        -D gallium-rusticl=${_rusticl} \
        -D gallium-va=${_gallium_va} \
        -D gallium-vdpau=${_gallium_vdpau} \
        -D gallium-xa=disabled \
        -D gbm=enabled \
        -D gles1=disabled \
        -D gles2=enabled \
        -D glvnd=enabled \
        -D glx=dri \
        -D libunwind=enabled \
        -D llvm=enabled \
        -D lmsensors=enabled \
        -D microsoft-clc=disabled \
        -D platforms=x11,wayland \
        -D shared-glapi=enabled \
        -D video-codecs=all \
        -D vulkan-layers=device-select,overlay \
        -D tools=[] \
        -D zstd=enabled \
        -D prefix=/usr \
        -D sysconfdir=/etc \
        -D valgrind=disabled \
        $_rust_std $_intel_rt $_additional_meson_flags $_additional_meson_flags_64
       
    meson configure --no-pager _build64
    ninja $NINJAFLAGS -C _build64

    if [[ "$_lib32" == "true" ]]; then
      cd "$srcdir"

      if [[ "$_compiler" == "clang" ]]; then
        export CC="clang -m32"
        export CXX="clang++ -m32"
      else
        export CC="gcc -m32"
        export CXX="g++ -m32"
      fi

      export PKG_CONFIG=/usr/bin/i686-pc-linux-gnu-pkg-config
      export BINDGEN_EXTRA_CLANG_ARGS="--target=i686-unknown-linux-gnu"

      meson setup $_mesa_srcdir _build32 \
        --native-file llvm32.native \
        --wrap-mode=nofallback \
        --libdir=/usr/lib32 \
        -D b_ndebug=true \
        -D b_lto=${_lto} \
        -D prefix=/usr \
        -D sysconfdir=/etc \
        -D platforms=x11,wayland \
        -D gallium-drivers=${_gallium_drivers} \
        -D vulkan-drivers=${_vulkan_drivers} \
        -D egl=enabled \
        -D gallium-extra-hud=true \
        -D vulkan-layers=device-select,overlay \
        -D gallium-nine=${_gallium_nine} \
        -D gallium-opencl=disabled \
        -D gallium-va=${_gallium_va} \
        -D gallium-vdpau=${_gallium_vdpau} \
        -D gallium-xa=disabled \
        -D gbm=enabled \
        -D gles1=disabled \
        -D gles2=enabled \
        -D glvnd=true \
        -D glx=dri \
        -D libunwind=enabled \
        -D llvm=enabled \
        -D lmsensors=enabled \
        -D shared-glapi=enabled \
        -D valgrind=disabled \
        -D tools=[] \
        -D zstd=enabled \
        -D microsoft-clc=disabled \
        $_intel_rt_32 $_intel_clc_32 $_additional_meson_flags $_additional_meson_flags_32
       
      meson configure --no-pager _build32
      ninja $NINJAFLAGS -C _build32
    fi
}

package_mesa-tkg-git() {
  depends=(${_depends64[@]})
  provides=(
      'vulkan-mesa-layers'
      'opencl-driver'
      'opengl-driver'
      'vulkan-driver'
      'vulkan-intel'
      'vulkan-nouveau'
      'vulkan-radeon'
      'vulkan-swrast'
      'vulkan-virtio'
      'libva-mesa-driver'
      'mesa-vdpau'
      'mesa-libgl'
      'mesa'
  )
  conflicts=(
      'vulkan-mesa-layers'
      'opencl-clover-mesa'
      'vulkan-intel'
      'vulkan-nouveau'
      'vulkan-radeon'
      'vulkan-swrast'
      'vulkan-virtio'
      'libva-mesa-driver'
      'mesa-vdpau'
      'mesa-libgl'
      'mesa'
  )

  if [[ "$_rusticl" == "true" ]]; then
    conflicts+=('opencl-rusticl-mesa')
    provides+=('opencl-rusticl-mesa')
  fi

  DESTDIR="$pkgdir" ninja $NINJAFLAGS -C _build64 install

  # remove script file from /usr/bin
  # https://gitlab.freedesktop.org/mesa/mesa/issues/2230
  rm -f "${pkgdir}/usr/bin/mesa-overlay-control.py"

  # indirect rendering
  ln -s /usr/lib/libGLX_mesa.so.0 "${pkgdir}/usr/lib/libGLX_indirect.so.0"

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname[0]}" "${srcdir}/LICENSE"
}

package_lib32-mesa-tkg-git() {
  depends=(
      "${pkgname[0]}"
      ${_depends32[@]}
  )
  provides=(
      'lib32-mesa'
      'lib32-vulkan-intel'
      'lib32-vulkan-radeon'
      'lib32-vulkan-mesa-layers'
      'lib32-libva-mesa-driver'
      'lib32-mesa-vdpau'
      'lib32-mesa-libgl'
      'lib32-opengl-driver'
      'lib32-vulkan-driver'
  )
  conflicts=(
      'lib32-mesa'
      'lib32-vulkan-intel'
      'lib32-vulkan-radeon'
      'lib32-vulkan-mesa-layers'
      'lib32-libva-mesa-driver'
      'lib32-mesa-vdpau'
      'lib32-mesa-libgl'
  )

  DESTDIR="$pkgdir" ninja $NINJAFLAGS -C _build32 install

  # remove files provided by mesa-git
  rm -rf "$pkgdir"/etc
  rm -rf "$pkgdir"/usr/include
  rm -rf "$pkgdir"/usr/share/glvnd/
  rm -rf "$pkgdir"/usr/share/drirc.d/
  rm -rf "$pkgdir"/usr/share/vulkan/explicit_layer.d/
  rm -rf "$pkgdir"/usr/share/vulkan/implicit_layer.d/VkLayer_MESA_device_select.json

  # indirect rendering
  ln -s /usr/lib32/libGLX_mesa.so.0 "${pkgdir}/usr/lib32/libGLX_indirect.so.0"
  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname[1]}" "${srcdir}/LICENSE"
}
 
trap exit_cleanup EXIT
