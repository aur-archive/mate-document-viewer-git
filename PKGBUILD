# Maintainer: Perberos <perberos@gmail.com>
pkgname=mate-document-viewer-git
pkgver=20130401
pkgrel=1
pkgdesc="Simply a document viewer"
url="https://github.com/mate-desktop/mate-document-viewer"
arch=('i686' 'x86_64')
license=('GPL')
depends=('gtk2' 'libspectre' 'gsfonts' 'poppler-glib' 'libgxps' 'djvulibre'
         'mate-icon-theme' 't1lib' 'libmatekeyring' 'desktop-file-utils'
         'texlive-bin') #  'dconf' 'gsettings-desktop-schemas'
provides=('mate-document-viewer')
conflicts=('mate-document-viewer')
makedepends=('git' 'mate-doc-utils' 'mate-file-manager' 'intltool' 'gobject-introspection')
optdepends=('texlive-bin: DVI support')
groups=('mate-extra')
install=mate-document-viewer.install
options=('!libtool' '!emptydirs')
source=()
sha256sums=()

_gitroot=git://github.com/mate-desktop/mate-document-viewer.git
_gitname=mate-document-viewer

build() {
	cd "$srcdir"
	msg "Connecting to GIT server...."

	if [[ -d "$_gitname" ]]; then
		cd "$_gitname" && git pull origin
		msg "The local files are updated."
	else
		git clone "$_gitroot" "$_gitname"
	fi

	msg "GIT checkout done or server timeout"
	msg "Starting build..."

	rm -rf "$srcdir/$_gitname-build"
	git clone "$srcdir/$_gitname" "$srcdir/$_gitname-build"
	cd "$srcdir/$_gitname-build"

    ./autogen.sh \
        --prefix=/usr \
        --sysconfdir=/etc \
        --localstatedir=/var \
        --libexecdir=/usr/lib/mate-document-viewer \
        --disable-static \
        --enable-caja \
        --enable-pdf \
        --enable-tiff \
		--enable-xps \
        --enable-djvu \
        --enable-dvi \
        --enable-t1lib \
        --enable-comics \
        --enable-pixbuf \
        --enable-impress \
        --disable-scrollkeeper \
        --enable-introspection \
        --disable-schemas-compile \
        --disable-schemas-install || return 1

    make || return 1
}

package() {
	cd "$srcdir/$_gitname-build"
	
    make MATECONF_DISABLE_MAKEFILE_SCHEMA_INSTALL=1 DESTDIR="${pkgdir}" install || return 1

    ln -s atril ${pkgdir}/usr/bin/mate-document-viewer

    install -m755 -d "${pkgdir}/usr/share/mateconf/schemas"
    mateconf-merge-schema "${pkgdir}/usr/share/mateconf/schemas/${pkgname}.schemas" --domain ${pkgname} ${pkgdir}/etc/mateconf/schemas/*.schemas
    rm -f ${pkgdir}/etc/mateconf/schemas/*.schemas
}
