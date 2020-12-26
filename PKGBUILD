# Maintainer: Atom Long <atom.long@hotmail.com>

pkgname=('drone-server' 'drone-agent')
_pkgname=drone
pkgver=1.10.1
pkgrel=1
pkgdesc="Drone is a Continuous Integration platform built on Docker, written in Go."
arch=('i686' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="http://github.com/drone/drone"
license=('Apache 2')
makedepends=('git' 'go')
backup=('etc/drone/server' 'etc/drone/agent')
source=("$_pkgname-$pkgver.tar.gz::https://github.com/$_pkgname/$_pkgname/archive/v$pkgver.tar.gz"
        'drone-server.service'
        'drone-agent.service'
        'server.conf'
        'agent.conf')
sha256sums=('22e934f1d8da385082d6704781c4a89e6a8594fbd0292ea9a5bcb472cafce22e'
		    '5be757ee7375ec0b264a0f61745a045c1a605a460b22b8ccd3d86e5cbc2764c2'
			'afa143befcc65bd2f9cdfb0b5b2c435385eaf6ad9bd12aeec81eb060b441f007'
			'4939f041cacbcab38aa2ea1af0fb229bc5afddfac28b8acf62f931ae632d913a'
			'0ee9a5d644eeb13a6bfc61fde8d3926dea2527d0eb71bdfc7e683abd0c20ea51')

prepare() {
  # Remove pkgver from folder name
  mv -vf $srcdir/$_pkgname{-${pkgver},}
  # setup local go env
  export GOPATH="$srcdir/go"
  mkdir -p "$GOPATH/src/github.com/drone"
  ln -sf "$srcdir/$_pkgname" "$GOPATH/src/github.com/drone/"

  # install go dependencies manually
  msg2 'installing go dependencies'
  pushd "$GOPATH/src/github.com/drone/drone" >/dev/null
    go get ./...
  popd
}

build() {
  msg2 'building drone-server'
  pushd "$GOPATH/src/github.com/drone/$_pkgname" >/dev/null
    go build \
      -o release/drone-server \
        github.com/drone/drone/cmd/drone-server
    go build \
      -o release/drone-agent \
        github.com/drone/drone/cmd/drone-agent
  popd
}

package_drone-server() {
  pkgdesc="Drone CI - Server"
  install='drone-server.install'
  provides=('drone-server')
  conflicts=('drone')
  # binaries
  install -Dm755 "$srcdir/$_pkgname/release/drone-server" "$pkgdir/usr/bin/drone-server"

  # license
  install -Dm644 "$_pkgname/LICENSE" "$pkgdir/usr/share/$_pkgname"

  # service
  install -Dm644 drone-server.service "$pkgdir/usr/lib/systemd/system/drone-server.service"

  # config
  install -Dm644 server.conf "$pkgdir/etc/drone/server"

  # db path
  install -dm700 "$pkgdir/var/lib/drone"
  chown -R 633:633 "$pkgdir/var/lib/drone"
}

package_drone-agent() {
  pkgdesc="Drone CI - Agent"
  install='drone-agent.install'
  provides=('drone-agent')
  depends=('docker')
  # binaries
  install -Dm755 "$srcdir/$_pkgname/release/drone-agent" "$pkgdir/usr/bin/drone-agent"

  # license
  install -Dm644 "$_pkgname/LICENSE" "$pkgdir/usr/share/$_pkgname"

  # service
  install -Dm644 drone-agent.service "$pkgdir/usr/lib/systemd/system/drone-agent.service"

  # config
  install -Dm644 agent.conf "$pkgdir/etc/drone/agent"
}

# vim:set ts=2 sw=2 ft=sh et:
