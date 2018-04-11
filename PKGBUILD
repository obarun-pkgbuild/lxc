# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/community.git/tree/trunk?h=packages/lxc
# 						Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# 						Contributor: Andrea Zucchelli <zukka77@gmail.com>
# 						Contributor: Daniel Micay <danielmicay@gmail.com>
# 						Contributor: Jonathan Liu <net147@gmail.com>
# 						Contributor: Jon Nordby <jononor@gmail.com>

pkgname=lxc
_commit=33c7c7feb28b7fd217d24b15df139123ede28a37
epoch=1
pkgver=3.0.0+31+g33c7c7fe
pkgrel=1
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
	'etc/default/lxc'
	'etc/default/lxc-net')
install=lxc.install
validpgpkeys=('602F567663E593BCBD14F338C638974D64792D67')
#source=("http://linuxcontainers.org/downloads/${pkgname}-${pkgver}.tar.gz"

source=("git+https://github.com/lxc/lxc/#commit=$_commit"
		"lxc.tmpfiles.d"
		"lxc-net"
		"be_more_verbose_n_skip_chmod.patch")
md5sums=('SKIP'
         'df94c9fb8a753011c86ee664e9f521ff'
         'ef4d5b67d7a73a51fae686d37750957b'
         '62eda2b1c7f39abc830492d3fd0dad73')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal
pkgver() {
	cd "${pkgname}"
	
	git describe --tags | sed -e 's:-:+:g;s:^lxc+::'
}
prepare() {
  #cd "$srcdir/$pkgname-${pkgver/_/-}"
  cd "${pkgname}"
  sed -i \
    -e 's|"\\"-//Davenport//DTD DocBook V3.0//EN\\""|"\\"-//OASIS//DTD DocBook XML\\" \\"http://www.oasis-open.org/docbook/xml/4.5/docbookx.dtd\\""|' \
    configure.ac
  sed -i \
    -e 's|\${prefix}/||g' \
    lxc.pc.in
  sed -i \
    -e 's|dirlen,|dirlen=0,|' \
    src/lxc/storage/overlay.c
    patch -Np1 -i "${srcdir}/be_more_verbose_n_skip_chmod.patch"
}

build() {
  cd "${pkgname}"
  #cd "$srcdir/$pkgname-${pkgver/_/-}"
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

#  cd "$srcdir/$pkgname-${pkgver/_/-}"
	cd "${pkgname}"
  make DESTDIR="$pkgdir" install
  install -d -m0755 "$pkgdir/var/lib/lxc"
  install -d -m0755 "$pkgdir/usr/lib/lxc/rootfs/dev"
  install -D -m0644 ${srcdir}/lxc.tmpfiles.d ${pkgdir}/usr/lib/tmpfiles.d/lxc.conf
  install -D -m0644 ${srcdir}/lxc-net ${pkgdir}/etc/default/lxc-net
  cd doc
  find . -type f -name '*.1' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man1/{}" \;
  find . -type f -name '*.5' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man5/{}" \;
  find . -type f -name '*.7' -exec install -D -m644 "{}" "$pkgdir/usr/share/man/man7/{}" \;
}
