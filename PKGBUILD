# vim:set ts=2 sw=2 et:
# Maintainer: BlackIkeEagle < ike DOT devolder AT gmail DOT com >
# Contributor: DonVla <donvla@users.sourceforge.net>
# Contributor: Ulf Winkelvos <ulf [at] winkelvos [dot] de>
# Contributor: Ralf Barth <archlinux dot org at haggy dot org>
# Contributor: B & monty - Thanks for your hints :)
# Contributor: marzoul
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Brad Fanella <bradfanella@archlinux.us>
# Contributor: [vEX] <niechift.dot.vex.at.gmail.dot.com>
# Contributor: Zeqadious <zeqadious.at.gmail.dot.com>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Maxime Gauduin <alucryd@gmail.com>
#
# Original credits go to Edgar Hucek <gimli at dark-green dot com>
# for his xbmc-vdpau-vdr PKGBUILD at https://archvdr.svn.sourceforge.net/svnroot/archvdr/trunk/archvdr/xbmc-vdpau-vdr/PKGBUILD

pkgbase=kodi-git
pkgname=('kodi-git' 'kodi-eventclients-git' 'kodi-tools-texturepacker-git' 'kodi-dev-git')
_gitname='xbmc'
pkgver=20170923.f97ea3ce16
pkgrel=1
arch=('i686' 'x86_64')
url="http://kodi.tv"
license=('GPL2')
makedepends=(
  'afpfs-ng' 'bluez-libs' 'boost' 'cmake' 'curl' 'cwiid' 'doxygen' 'git' 'glew'
  'gperf' 'hicolor-icon-theme' 'jasper' 'java-runtime' 'libaacs' 'libass'
  'libbluray' 'libcdio' 'libcec' 'libgl' 'libmariadbclient' 'libmicrohttpd'
  'libmodplug' 'libmpeg2' 'libnfs' 'libplist' 'libpulse' 'libssh' 'libva'
  'libvdpau' 'libxrandr' 'libxslt' 'lzo' 'nasm' 'nss-mdns' 'python2-pillow'
  'python2-pybluez' 'python2-simplejson' 'rtmpdump'
  'shairplay' 'smbclient' 'swig' 'taglib' 'tinyxml' 'unzip' 'upower' 'yajl' 'zip'
  'mesa' 'libcrossguid-git' 'libfmt-git' 'rapidjson-git'
)
source=(
  "$_gitname::git://github.com/xbmc/xbmc.git"
  '0001-include-fmt-printf.h-to-allow-the-use-of-fmt-sprintf.patch'
)
sha512sums=('SKIP'
            '39c84be54caa0e4a9b57ec92a903d688703e3fc2cf6ea566efad9f2509eb286d981837e4968af1c28ffb2f5fe7a872fa546535c787a56dbdb335991514fb1a06')

pkgver() {
  cd "$srcdir/$_gitname"
  git log -1 --date=short --format="%cd.%h" | tr -d '-'
}

prepare() {
  [[ -d kodi-build ]] && rm -rf kodi-build
  mkdir kodi-build
  cd $_gitname
  patch -p1 -i "$srcdir/0001-include-fmt-printf.h-to-allow-the-use-of-fmt-sprintf.patch"
}

build() {
  cd "$_gitname"
  cmake -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_LIBDIR=/usr/lib \
    -DENABLE_EVENTCLIENTS=ON \
    -DLIRC_DEVICE=/run/lirc/lircd \
    .
  make VERBOSE=1
  make preinstall
}

# kodi
# components: kodi, kodi-bin

package_kodi-git() {
  pkgdesc="A software media player and entertainment hub for digital media (master branch)"

  # depends expected for kodi plugins:
  # 'python2-pillow' 'python2-pybluez' 'python2-simplejson'
  # depends expeced in FEH.py
  # 'mesa-demos' 'xorg-xdpyinfo'
  depends=(
    'python2-pillow' 'python2-pybluez' 'python2-simplejson' 'xorg-xdpyinfo'
    'bluez-libs' 'fribidi' 'freetype2' 'glew' 'hicolor-icon-theme' 'libcdio'
    'libjpeg-turbo' 'libmariadbclient' 'libmicrohttpd' 'libpulse' 'libssh'
    'libva' 'libvdpau' 'libxrandr' 'libxslt' 'lzo' 'smbclient' 'taglib' 'tinyxml'
    'yajl' 'mesa' 'desktop-file-utils'
  )
  optdepends=(
    'gdb: for meaningful backtraces in case of trouble - STRONGLY RECOMMENDED'
    'afpfs-ng: Apple shares support'
    'bluez: Blutooth support'
    'python2-pybluez: Bluetooth support'
    'libnfs: NFS shares support'
    'libplist: AirPlay support'
    'libcec: Pulse-Eight USB-CEC adapter support'
    'lirc: Remote controller support'
    'lsb-release: log distro information in crashlog'
    'pulseaudio: PulseAudio support'
    'shairplay: AirPlay support'
    'unrar: Archives support'
    'unzip: Archives support'
    'upower: Display battery level'
  )
  provides=('xbmc' 'kodi')
  conflicts=('xbmc' 'kodi')
  replaces=('xbmc-svn' 'xbmc-git')

  _components=(
    'kodi'
    'kodi-bin'
  )

  cd "$_gitname"
  # install eventclients
  for _cmp in ${_components[@]}; do
    DESTDIR="$pkgdir" /usr/bin/cmake \
      -DCMAKE_INSTALL_COMPONENT="$_cmp" \
      -P cmake_install.cmake
  done

  # Licenses
  install -dm755 "$pkgdir/usr/share/licenses/$pkgname"
  for licensef in LICENSE.GPL copying.txt; do
    mv "$pkgdir/usr/share/doc/kodi/$licensef" \
      "$pkgdir/usr/share/licenses/$pkgname"
  done

  # python2 is being used
  cd "$pkgdir"
  grep -lR '#!.*python' * | while read file; do sed -s 's/\(#!.*python\)/\12/g' -i "$file"; done
}

# kodi-eventclients
# components: kodi-eventclients-common kodi-eventclients-ps3 kodi-eventclients-wiiremote kodi-eventclients-xbmc-send

package_kodi-eventclients-git() {
  pkgdesc="Kodi Event Clients (master branch)"
  conflicts=('kodi-eventclients')

  depends=('cwiid')

  _components=(
    'kodi-eventclients-common'
    'kodi-eventclients-ps3'
    'kodi-eventclients-wiiremote'
    'kodi-eventclients-xbmc-send'
  )

  cd "$_gitname"
  # install eventclients
  for _cmp in ${_components[@]}; do
    DESTDIR="$pkgdir" /usr/bin/cmake \
      -DCMAKE_INSTALL_COMPONENT="$_cmp" \
      -P cmake_install.cmake
  done

  # python2 is being used
  cd "$pkgdir"
  grep -lR '#!.*python' * | while read file; do sed -s 's/\(#!.*python\)/\12/g' -i "$file"; done
}

# kodi-tools-texturepacker
# components: kodi-tools-texturepacker

package_kodi-tools-texturepacker-git() {
  pkgdesc="Kodi Texturepacker tool (master branch)"
  depends=('libpng' 'giflib' 'libjpeg-turbo' 'lzo')

  _components=(
    'kodi-tools-texturepacker'
  )

  cd "$_gitname"
  # install eventclients
  for _cmp in ${_components[@]}; do
    DESTDIR="$pkgdir" /usr/bin/cmake \
      -DCMAKE_INSTALL_COMPONENT="$_cmp" \
      -P cmake_install.cmake
  done
}

# kodi-dev
# components: kodi-addon-dev kodi-audio-dev kodi-eventclients-dev kodi-game-dev kodi-inputstream-dev kodi-peripheral-dev kodi-pvr-dev kodi-screensaver-dev kodi-visualization-dev

package_kodi-dev-git() {
  pkgdesc="Kodi dev files (master branch)"
  depends=('kodi')

  _components=(
    'kodi-addon-dev'
    'kodi-audio-dev'
    'kodi-eventclients-dev'
    'kodi-game-dev'
    'kodi-inputstream-dev'
    'kodi-peripheral-dev'
    'kodi-pvr-dev'
    'kodi-screensaver-dev'
    'kodi-visualization-dev'
  )

  cd "$_gitname"
  # install eventclients
  for _cmp in ${_components[@]}; do
    DESTDIR="$pkgdir" /usr/bin/cmake \
      -DCMAKE_INSTALL_COMPONENT="$_cmp" \
      -P cmake_install.cmake
  done

  # python2 is being used
  cd "$pkgdir"
  grep -lR '#!.*python' * | while read file; do sed -s 's/\(#!.*python\)/\12/g' -i "$file"; done
}
