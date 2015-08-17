# Contributor: Mark Foxwell <fastfret79@archlinux.org.uk>
# Contributor: Olivier Ramonat <olivier at ramonat dot fr>
# Contributor: Richard Murri <admin@richardmurri.com>
# Contributor: Bryan Ames <b130610@gmail.com>
 
_pkgname=notmuch
pkgname=notmuch-git
pkgver=0.18.1.r95.56c48dc
pkgrel=1
pkgdesc="Notmuch is not much of an email program"
arch=('i686' 'x86_64')
url="http://notmuchmail.org/"
license=('GPL3')
depends=('xapian-core' 'gmime' 'talloc')
makedepends=('git' 'python2' 'ruby' 'doxygen' 'python-sphinx')
provides=('notmuch' 'notmuch-vim' 'notmuch-runtime' )
conflicts=('notmuch' 'notmuch-vim''notmuch-runtime' )
optdepends=('emacs: for using the emacs interface'
            'vim: for using the vim interface'
            'python: for using the python bindings'
            'python2: for using the python2 bindings'
            'gnupg: for email encryption')
source=("git://notmuchmail.org/git/notmuch")
md5sums=('SKIP')
 
 
pkgver() {
  cd $_pkgname
  git describe --long | sed -r 's|^[a-z]*/*([^-]+)-*[0-9]*-([0-9]+)-g([0-9]+)|\1.r\2.\3|'
}

prepare() {
  # Will build python 2 and python 3 bindings
  cp -r $_pkgname/bindings/python $_pkgname/bindings/python2
  cd $_pkgname/bindings/python2
  find "." -name '*.py' -print0 |xargs -0 \
      sed -i -e 's,^#!/usr/bin/env python$,#!/usr/bin/env python2,' \
      -e 's,^#!/usr/bin/python$,#!/usr/bin/python2,'
  sed -i -e 's/\(^.*\)python\(.*\.py$\)/\1python2\2/' ../../doc/Makefile.local
  ln -s ../../lib/libnotmuch.so.1 libnotmuch.so.1
}
 
build() {
  cd "$srcdir/$_pkgname"
 
  # Workaround to fix uptream error
  # mv emacs/notmuch-version.el.tmpl emacs/notmuch-version.el
 
  ./configure --prefix=/usr \
              --sysconfdir=/etc \
              --zshcompletiondir=/usr/share/zsh/site-functions
  make
 
  cd bindings/python
  env LD_LIBRARY_PATH="." python setup.py build
 
  cd ../python2
  env LD_LIBRARY_PATH="." python2 setup.py build
 
  cd ../ruby
  ruby extconf.rb --vendor
 
}
 
 
package(){
  cd "$srcdir/$_pkgname"
  make DESTDIR="$pkgdir/" LIBDIR_IN_LDCONFIG=0 install
 
  install -D notmuch $pkgdir/usr/bin/notmuch
 
  mkdir -p $pkgdir/usr/share/vim/vimfiles/{plugin,syntax}
 
  cd vim && make prefix="$pkgdir/usr/share/vim/vimfiles" install
 
  # Install ruby bindings
  cd $srcdir/$_pkgname/bindings/ruby
  sed -i -e 's,/site_ruby,,g' Makefile
  make exec_prefix=$pkgdir/usr prefix=/usr install
 
  # Install python bindings
  cd $srcdir/$_pkgname/bindings/python
  env LD_LIBRARY_PATH="." python setup.py install --prefix=/usr --root=$pkgdir
  cd $srcdir/$_pkgname/bindings/python2
  env LD_LIBRARY_PATH="." python2 setup.py install --prefix=/usr --root=$pkgdir
}
