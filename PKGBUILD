# Maintainer: Radioactiveman <thomas-lange2@gmx.de>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Lauri Niskanen <ape@ape3000.com>
# Contributor: Sebastian.Salich@gmx.de
# Contributor: Doc Angelo

_pkgname=mumble
pkgname="$_pkgname-git"
_pkgver=1.4.0
pkgver=1.4.0.r7810.856eefa9b
pkgrel=1
arch=('i686' 'x86_64')
pkgdesc='A voice chat application similar to TeamSpeak'
url='https://www.mumble.info/'
license=('BSD')
depends=('qt5-base' 'qt5-svg' 'speex' 'lsb-release' 'avahi' 'protobuf' 'libpulse' 'opus'
         'xdg-utils' 'libspeechd' 'libpng' 'freetype2' 'fontconfig' 'libxrender')
makedepends=('boost' 'jack' 'mesa' 'python' 'qt5-tools' 'git')
optdepends=('jack: JACK audio output'
            'espeak-ng: Text to Speech support'
            'speech-dispatcher: Text to Speech support')
conflicts=("$_pkgname")
provides=("$_pkgname")
source=('git://github.com/mumble-voip/mumble.git' 'https://git.xiph.org/celt.git'
        'git://github.com/mumble-voip/fx11.git' 'git://github.com/mumble-voip/opus.git'
        'git://github.com/mumble-voip/sbcelt.git' 'https://git.xiph.org/speex.git')
sha256sums=('SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP')

pkgver() {
  cd "$srcdir/$_pkgname"
  printf "$_pkgver.r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  cd "$srcdir/$_pkgname"

  git submodule init
  git config submodule.3rdparty/fx11-src.url "$srcdir/fx11"
  git config submodule.3rdparty/celt-0.7.0-src.url "$srcdir/celt"
  git config submodule.3rdparty/opus-src.url "$srcdir/opus"
  git config submodule.3rdparty/sbcelt-src.url "$srcdir/sbcelt"
  git config submodule.3rdparty/speex-src.url "$srcdir/speex"
  git submodule update
}

build() {
  cd "$srcdir/$_pkgname"

  # Building mumble
  qmake-qt5 main.pro \
    CONFIG+="bundled-celt no-bundled-opus no-bundled-speex no-g15 no-xevie \
             no-server no-embed-qt-translations no-update packaged" \
    DEFINES+="PLUGIN_PATH=/usr/lib/mumble" \
    INCLUDEPATH+="/usr/include/speech-dispatcher" \
    LIBS+="-lpng16 -lfreetype -lXrender -lfontconfig"

  make release
}

package() {
  cd "$srcdir/$_pkgname"

  # bin stuff
  install -m755 -D ./release/mumble "$pkgdir"/usr/bin/mumble
  install -m755 -D ./scripts/mumble-overlay "$pkgdir"/usr/bin/mumble-overlay

  # lib stuff
  install -m755 -D ./release/libmumble.so.$_pkgver "$pkgdir"/usr/lib/mumble/libmumble.so.$_pkgver
  ln -s libmumble.so.$_pkgver "$pkgdir"/usr/lib/mumble/libmumble.so
  ln -s libmumble.so.$_pkgver "$pkgdir"/usr/lib/mumble/libmumble.so.1
  ln -s libmumble.so.$_pkgver "$pkgdir"/usr/lib/mumble/libmumble.so.${_pkgver:0:3}
  install -m755 -D ./release/plugins/liblink.so "$pkgdir"/usr/lib/mumble/liblink.so
  install -m755 -D ./release/libcelt* "$pkgdir"/usr/lib/mumble/

  # other stuff
  install -m644 -D ./scripts/mumble.desktop "$pkgdir"/usr/share/applications/mumble.desktop
  install -m755 -d "$pkgdir"/usr/share/man/man1
  install -m644 -D ./man/mum* "$pkgdir"/usr/share/man/man1/
  install -m644 -D ./icons/mumble.svg "$pkgdir"/usr/share/icons/hicolor/scalable/apps/mumble.svg
  install -m644 -D ./LICENSE "$pkgdir"/usr/share/licenses/$_pkgname/LICENSE
}
