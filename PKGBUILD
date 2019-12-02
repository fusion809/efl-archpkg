# Maintainer: Ronald van Haren <ronald@archlinux.org>
# Contributor: Enlightenment Developers <enlightenment-devel@enlightenment.org>

pkgname=efl
pkgver=1.23.3
pkgrel=1
pkgdesc="Enlightenment Foundation Libraries"
arch=('x86_64')
url="https://www.enlightenment.org"
license=('BSD' 'LGPL2.1' 'GPL2' 'custom')
depends=('bullet' 'libjpeg-turbo' 'gst-plugins-base'
         'luajit' 'curl' 'fribidi' 'libpulse' 'libxcomposite'
         'libxinerama' 'libxrandr' 'libxss' 'libinput'
         'libxcursor' 'libxp' 'libwebp' 'shared-mime-info'
         'libxkbcommon' 'libxkbcommon-x11' 'wayland' 'lz4' 'openjpeg' 'avahi'
         'libspectre' 'libraw' 'librsvg' 'wayland-protocols'
         'mesa' 'hicolor-icon-theme' 'poppler')
optdepends=('python2: einabench-cmp' 'libreoffice: thumbnailing for DOC/PPT/XLS files')
makedepends=('scim' 'check' 'python' 'texlive-core' 'ghostscript' 'imagemagick')
options=('!emptydirs')
source=(https://download.enlightenment.org/rel/libs/${pkgname}/$pkgname-$pkgver.tar.xz)
sha256sums=('53cea69eaabe443a099fb204b7353e968e7bb62b41fbb0da24451403c7a56901')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  export CFLAGS=`echo -n $CFLAGS | sed 's/-mfpu=vfpv3-d16/-mfpu=neon-vfpv4/g'`
  export CFLAGS="$CFLAGS -fvisibility=hidden"
  export CXXFLAGS="$CXXFLAGS -fvisibility=hidden"

  MEM=`free -m | head -2 | tail -1 | awk '{printf("%s", $7);}'`
  if test "$MEM" -lt 300; then MEM=300; fi
  J=`expr $MEM / 300`
  CPUS=`echo /sys/devices/system/cpu/cpu[0-9]* | wc -w`
  if test "$J" -gt $CPUS; then J=$CPUS; fi
  if test "$J" -lt 1; then J=1; fi
  echo "Free Mem: $MEM M, using $J threads for build"

  rm -rf build
  meson --prefix=/usr \
    -Dfb=true \
    -Ddrm=true \
    -Dwl=true \
    -Dnetwork-backend=connman \
    -Devas-loaders-disabler=json \
    -Dbindings= \
    -Dbuild-examples=false \
    -Dbuild-tests=false \
    -Decore-imf-loaders-disabler= \
    . build

  ninja -j $J -C build

  #make -j1 doc || return 0  # don't fail on the docs
}

package(){
  replaces=('elementary' 'evas_generic_loaders' 'emotion_generic_players')

  cd "${srcdir}/${pkgname}-${pkgver}"
  DESTDIR="$pkgdir" ninja -C build install

# compile python files
  python2 -m compileall -q "$pkgdir"
  python2 -O -m compileall -q "$pkgdir"

  install -Dm644 -t "$pkgdir/usr/share/doc/$pkgname-$pkgver/" ChangeLog NEWS README
  install -Dm644 -t "$pkgdir/usr/share/licenses/$pkgname/" AUTHORS COMPLIANCE COPYING COPYING.images licenses/COPYING.{BSD,SMALL}
}
