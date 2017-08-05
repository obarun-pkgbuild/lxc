# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/community.git/tree/trunk?h=packages/lxc
# 						Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# 						Contributor: Andrea Zucchelli <zukka77@gmail.com>
# 						Contributor: Daniel Micay <danielmicay@gmail.com>
# 						Contributor: Jonathan Liu <net147@gmail.com>
# 						Contributor: Jon Nordby <jononor@gmail.com>

pkgname=lxc
epoch=1
pkgver=2.0.8
pkgrel=3
pkgdesc="Linux Containers"
arch=(x86_64)
url="http://linuxcontainers.org"
depends=('bash' 'perl' 'libseccomp' 'libcap' 'python' 'cgmanager' 'rsync')
makedepends=('docbook2x' 'lua')
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
validpgpkeys=('602F567663E593BCBD14F338C638974D64792D67')
source=("http://linuxcontainers.org/downloads/${pkgname}-${pkgver}.tar.gz"
	"lxc.tmpfiles.d"
	"https://github.com/lxc/lxc/commit/57927bf2550feccadba2127a193023e7c54d9ca4.patch")
md5sums=('7bfd95280522d7936c0979dfea92cdb5'
         'df94c9fb8a753011c86ee664e9f521ff'
         '66652c56e7e48ba1c6b42f146e289386')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare() {
  cd "$srcdir/$pkgname-${pkgver/_/-}"
  sed -i \
    -e 's|"\\"-//Davenport//DTD DocBook V3.0//EN\\""|"\\"-//OASIS//DTD DocBook XML\\" \\"http://www.oasis-open.org/docbook/xml/4.5/docbookx.dtd\\""|' \
    configure.ac
  sed -i \
    -e 's|$(sysconfdir)/bash_completion.d/|/usr/share/bash-completion/completions/|g' \
    config/bash/Makefile.am
  sed -i \
    -e 's|\${prefix}/||g' \
    lxc.pc.in
  sed -i \
    -e "s|name='_lxc'|name='lxc'|" \
    src/python-lxc/setup.py.in
    
    patch -p1 -i $srcdir/*.patch
}

build() {
  cd "$srcdir/$pkgname-${pkgver/_/-}"
  ./autogen.sh
  ./configure \
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
