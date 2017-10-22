# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/community.git/tree/trunk?h=packages/lxc
# 						Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# 						Contributor: Andrea Zucchelli <zukka77@gmail.com>
# 						Contributor: Daniel Micay <danielmicay@gmail.com>
# 						Contributor: Jonathan Liu <net147@gmail.com>
# 						Contributor: Jon Nordby <jononor@gmail.com>

pkgname=lxc
epoch=1
pkgver=2.1.0
pkgrel=3
pkgdesc="Linux Containers"
arch=(x86_64)
url="http://linuxcontainers.org"
depends=('bash' 'perl' 'libseccomp' 'libcap' 'python' 'cgmanager' 'rsync')
makedepends=('docbook2x' 'lua' 'python-setuptools')
optdepends=('arch-install-scripts: for archlinux template'
	    'dnsmasq: lxc-net.service'
	    'lua'
	    'lua-filesystem: lxc-top'
	    'lua-alt-getopt: lxc-top'
	    'cgmanager-s6serv: cgmanager s6 service')
license=('LGPL')
options=('emptydirs')
backup=('etc/lxc/default.conf'
	'etc/default/lxc')
install=lxc.install
validpgpkeys=('602F567663E593BCBD14F338C638974D64792D67')
source=("http://linuxcontainers.org/downloads/${pkgname}-${pkgver}.tar.gz"
		"lxc.tmpfiles.d")
md5sums=('a951c2f6dcfc77fc4efedb2e75115d85'
         'df94c9fb8a753011c86ee664e9f521ff')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare() {
  cd "$srcdir/$pkgname-${pkgver/_/-}"
  sed -i \
    -e 's|"\\"-//Davenport//DTD DocBook V3.0//EN\\""|"\\"-//OASIS//DTD DocBook XML\\" \\"http://www.oasis-open.org/docbook/xml/4.5/docbookx.dtd\\""|' \
    configure.ac
  sed -i \
    -e 's|\${prefix}/||g' \
    lxc.pc.in
  sed -i \
    -e 's|dirlen,|dirlen=0,|' \
    src/lxc/storage/overlay.c
}

build() {
  cd "$srcdir/$pkgname-${pkgver/_/-}"
  ./autogen.sh
  bashcompdir=/usr/share/bash-completion/completions ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --localstatedir=/var \
    --libexecdir=/usr/lib \
    --libdir=/usr/lib \
    --sysconfdir=/etc \
    --disable-apparmor \
    --enable-seccomp \
    --enable-cgmanager \
    --enable-capabilities \
    --enable-lua \
    --with-systemdsystemunitdir=no
    
  make
}

package() {

  cd "$srcdir/$pkgname-${pkgver/_/-}"

  make DESTDIR="$pkgdir" install
  install -d -m755 "$pkgdir/var/lib/lxc"
  install -d -m755 "$pkgdir/usr/lib/lxc/rootfs/dev"
  install -D -m644 ${srcdir}/lxc.tmpfiles.d ${pkgdir}/usr/lib/tmpfiles.d/lxc.conf

  cd doc
  find . -type f -name '*.1' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man1/{}" \;
  find . -type f -name '*.5' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man5/{}" \;
  find . -type f -name '*.7' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man7/{}" \;
}
