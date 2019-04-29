# Maintainer: Hal Gentz <zegentzy@protonmail.com>
# Contributor: Lone_Wolf <lonewolf at xs4all dot nl>
# Contributor: Armin K. <krejzi at email dot com>
# Contributor: Kristian Klausen <klausenbusk@hotmail.com>
# Contributor: Egon Ashrafinia <e.ashrafinia@gmail.com>
# Contributor: Tavian Barnes <tavianator@gmail.com>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Antti "Tera" Oja <antti.bofh@gmail.com>
# Contributor: Diego Jose <diegoxter1006@gmail.com>

pkgbase=mesa-git
pkgname=('mesa-git' 'lib32-mesa-git')
pkgdesc="an open-source implementation of the OpenGL specification, git version"
pkgver=19.1.0_devel.110182.59deb41b06d
pkgrel=1
arch=('x86_64')
optdepends=('opengl-man-pages: for the OpenGL API man pages')
url="https://www.mesa3d.org"
license=('custom')
makedepends=(
    'git' 'python-mako' 'xorgproto' 'libomxil-bellagio' 'ocl-icd'
    'vulkan-icd-loader' 'wayland-protocols' 'meson' 'lib32-gcc-libs'
    'lib32-llvm-svn' 'lib32-libxml2' 'lib32-libx11' 'lib32-libvdpau'
    'lib32-libva' 'lib32-elfutils' 'lib32-libxrandr' 'lib32-libgcrypt'
    'lib32-wayland' 'lib32-libelf' 'llvm-svn' 'libxml2' 'libx11' 'libvdpau'
    'libva' 'elfutils' 'libxrandr' 'libgcrypt' 'wayland' 'libelf' 'clang-svn'
)
groups=('gentz_custom')

source=(
    'git+https://github.com/freedesktop/mesa.git'
    'LICENSE'
    '0001.patch'
    '0002.patch'
    'llvm32.native'
)

sha512sums=(
    'SKIP'
    '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
    'ec9c5f1e228f881ee2ad579b442ae3a3623758bd86c2c235cd9ef5d7aa6a41f4b3daf0ad6f228076d59e7996f4cdc0a79d9d13b703945b46d5a51c9b01d8df77'
    '2838c24aea0c3da3b9f5b5e89fd0ff2d4f9c3b3bb34cba713707425f9dadb8b277ff023ae254cd897aac962beaf3f2a92f5a31e127bef37f68de353abbf75af6'
    'c7dbb390ebde291c517a854fcbe5166c24e95206f768cc9458ca896b2253aabd6df12a7becf831998721b2d622d0c02afdd8d519e77dea8e1d6807b35f0166fe'
)

pkgver() {
    cd mesa
    read -r _ver <VERSION
    echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

exports_32() {
    export CC="gcc -m32 -g"
    export CXX="g++ -m32 -g"
    export PKG_CONFIG=/usr/bin/pkg-config-32  
}

exports_64() {
    export CC="gcc -g"
    export CXX="g++ -g"
}

prepare_meson() {
    meson setup mesa _build$1 \
        $2 \
        -D b_ndebug=true \
        -D buildtype=plain \
        --wrap-mode=nofallback \
        -D prefix=/usr \
        -D sysconfdir=/etc \
        -D platforms=x11,wayland,drm,surfaceless \
        -D dri-drivers=i915,i965,r200,r100,nouveau \
        -D gallium-drivers=r300,r600,radeonsi,nouveau,svga,swrast,virgl,iris \
        -D vulkan-drivers=amd,intel \
        -D dri3=true \
        -D egl=true \
        -D gallium-extra-hud=true \
        -D gallium-nine=true \
        -D gallium-omx=bellagio \
        -D gallium-va=true \
        -D gallium-vdpau=true \
        -D gallium-xa=true \
        -D gallium-xvmc=false \
        -D gbm=true \
        -D gles1=true \
        -D gles2=true \
        -D glvnd=true \
        -D glx=dri \
        -D libunwind=true \
        -D llvm=true \
        -D lmsensors=true \
        -D osmesa=gallium \
        -D shared-glapi=true \
        -D valgrind=false \
        -D tools=[] \
        -D vulkan-overlay-layer=true
    meson configure _build$1
}

prepare() {
    cd mesa
    git am --no-gpg < "../0001.patch"
    git am --no-gpg < "../0002.patch"
    cd ..

    if [ -d _build64 ]; then
        rm -rf _build64
    fi

    if [ -d _build32 ]; then
        rm -rf _build32
    fi

    exports_64
    _opts="-D gallium-opencl=icd"
    prepare_meson 64 "$_opts"

    exports_32
	_opts="--native-file llvm32.native --libdir=/usr/lib32"
    prepare_meson 32 "$_opts"

}

build () {
    exports_64
    ninja "$MAKEFLAGS" -C _build64
    exports_32
    ninja "$MAKEFLAGS" -C _build32
}

package_lib32-mesa-git () {
    depends+=(
        'libomxil-bellagio' 'libclc' 'lib32-libelf' 'lib32-libdrm'
        'lib32-libxxf86vm' 'lib32-libxdamage' 'lib32-libxshmfence'
        'lib32-llvm-libs-svn' 'lib32-libunwind' 'lib32-libglvnd'
        'lib32-wayland' 'lib32-lm_sensors'
    )
    provides+=(
        'lib32-mesa' 'lib32-opencl-mesa' 'lib32-vulkan-intel'
        'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau'
        'lib32-vulkan-driver' 'lib32-opengl-driver' 'lib32-opencl-driver'
    )
    conflicts+=(
        'lib32-mesa' 'lib32-opencl-mesa' 'lib32-vulkan-intel'
        'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau'
        'lib32-vulkan-driver' 'lib32-opengl-driver' 'lib32-opencl-driver'
    )
    DESTDIR="$pkgdir" ninja -C _build32 install

    # remove files provided by mesa-git
    rm -rf "$pkgdir"/etc
    rm -rf "$pkgdir"/usr/include
    rm -rf "$pkgdir"/usr/share/glvnd/
    rm -rf "$pkgdir"/usr/share/drirc.d/
    rm -rf "$pkgdir"/usr/share/vulkan/explicit_layer.d/

    # indirect rendering
    ln -s /usr/lib32/libGLX_mesa.so.0 "${pkgdir}/usr/lib32/libGLX_indirect.so.0"
}

package_mesa-git() {
    depends+=(
        'libomxil-bellagio' 'libclc' 'libelf' 'libdrm' 'libxxf86vm'
        'libxdamage' 'libxshmfence' 'llvm-libs-svn' 'libunwind' 'libglvnd'
        'wayland' 'lm_sensors' 'glslang'
    )
    provides+=(
        'mesa' 'opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'libva-mesa-driver'
        'mesa-vdpau' 'vulkan-driver' 'opengl-driver' 'opencl-driver'
    )
    conflicts+=(
        'mesa' 'opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'libva-mesa-driver'
        'mesa-vdpau' 'vulkan-driver' 'opengl-driver' 'opencl-driver'
    )

    DESTDIR="$pkgdir" ninja -C _build64 install

    # indirect rendering
    ln -s /usr/lib/libGLX_mesa.so.0 ${pkgdir}/usr/lib/libGLX_indirect.so.0

    install -Dt "$pkgdir"/usr/share/licenses/$pkgbase "$srcdir"/LICENSE
}
