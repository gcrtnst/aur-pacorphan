# Maintainer: gcrtnst <52910071+gcrtnst@users.noreply.github.com>

pkgname='pacorphan'
pkgver='0.1.0'
pkgrel=1
pkgdesc='A smarter alternative to "pacman -Qdt"'
arch=('x86_64')
url='https://github.com/gcrtnst/pacorphan'
license=('Unlicense')
depends=('glibc' 'pacman')
makedepends=('go')
checkdepends=('util-linux')
source=("$url/archive/v$pkgver/$pkgname-$pkgver.tar.gz")
sha256sums=('6619194a520dd4f318ef42914398eeccbe4ccff49e18f1450d872e01bf47d99c')

_gobuildflags=(
  '-trimpath'
  '-buildmode=pie'
  '-mod=vendor'
  '-modcacherw'
  '-ldflags=-linkmode=external'
  '-buildvcs=false'
)

_go() {
  local -x CGO_ENABLED=1
  local -x CGO_CFLAGS="${CFLAGS}"
  local -x CGO_CPPFLAGS="${CPPFLAGS}"
  local -x CGO_CXXFLAGS="${CXXFLAGS}"
  local -x CGO_FFLAGS="${FFLAGS}"
  local -x CGO_LDFLAGS="${LDFLAGS}"
  local -x GOPATH="$srcdir"

  go "$@"
}

_go_build() {
  _go build "${_gobuildflags[@]}" "$@"
}

_go_test() {
  _go test "${_gobuildflags[@]}" "$@"
}

prepare() {
  cd "$pkgname-$pkgver"
  _go mod vendor -v
}

build() {
  cd "$pkgname-$pkgver"

  mkdir -p build/
  _go_build -v -o "build/$pkgname" .
}

check() {
  cd "$pkgname-$pkgver"

  mkdir -p build/
  _go_build -v -o "build/alpmtest" ./internal/alpmtest
  _go_build -v -o "build/pacorphantest" ./internal/pacorphantest

  _go_test -v ./...
  build/alpmtest
  build/pacorphantest --cmd "build/$pkgname"
}

package() {
  cd "$pkgname-$pkgver"
  install -vDm755 -t "$pkgdir/usr/bin/" "build/$pkgname"
  install -vDm644 -t "$pkgdir/usr/share/licenses/$pkgname/" LICENSE
}
