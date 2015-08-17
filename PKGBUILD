#Maintainer: Jakub Lansky <jakub@lansky.biz> 

pkgname=remotefs-svn
pkgver=20130221
pkgrel=4
pkgdesc="remotefs is a network file system designed for use with home NAS. Simple to use, few dependencies, reasonable security, completely in user space."
arch=(i686 x86_64 armv7h)
license=('GPL')
url="http://remotefs.sourceforge.net"
depends=('fuse')
makedepends=('fuse' 'subversion')
conflicts=('remotefs')
backup=('etc/rfs-exports' 'etc/default/rfsd')
source=(werror.patch
	rfsd)
sha1sums=('931d9fa056ef352d222fd2acfce120690645feb7'
	'230f31b5994783adb629cc5d621def9ba6c7c889')

_svntrunk="http://svn.code.sf.net/p/remotefs/code/trunk/"
build() {
  cd "${srcdir}"

  msg "Connecting to $_svntrunk ..."
  if [ -d $_svnmod/.svn ]; then
    (cd $_svnmod && svn up -r $pkgver) || return 1
  else
    svn co $_svntrunk --config-dir ./ || return 1
  fi
  msg "SVN checkout done or server timeout"

  cd "${srcdir}/trunk"

	patch -p1 < ../werror.patch

	make release -j1
}

package() {
	cd "${srcdir}/trunk"
	install -d "${pkgdir}/usr"
	make INSTALL_DIR="${pkgdir}/usr" install

	install -d "${pkgdir}/usr/bin"
	install "build/sbin/mount.rfs" "${pkgdir}/usr/bin"
	install "build/sbin/rfsnsswitch.sh" "${pkgdir}/usr/bin"
	install "build/sbin/umount.fuse.rfs" "${pkgdir}/usr/bin"
	
	install -d "${pkgdir}/etc/default"
	install -m 600 "build/etc/rfs-exports" "${pkgdir}/etc"
	install -m 600 "build/debian/default/rfsd" "${pkgdir}/etc/default"
	
	install -d "${pkgdir}/etc/rc.d"
	install "../rfsd" "${pkgdir}/etc/rc.d/rfsd"
}
