# Maintainer: K.Hampf <khampf@users.sourceforge.net>

pkgname=wunderlist
_pkgname=Wunderlist
pkgver=1.2.4
pkgrel=8
pkgdesc="free and easy-to-use task manager"
arch=("i686" "x86_64")
url="https://s3.amazonaws.com/wunderlist/"
license=('APACHE')
depends=(libffi libwebkit libsoup-gnome curl libnotify hspell libxss)
options=(!strip !zipman !purge)

if [ "$CARCH" = "i686" ]; then _arch="32"
  md5sums=(213af281653267e25368d1d09c1c3ca8 0c6f49762bc0043394d5e440f2ff1af1 \
    06794f609ae25b75293816f191ebaf2a)
elif [ "$CARCH" = "x86_64" ]; then _arch="64"
  md5sums=(213af281653267e25368d1d09c1c3ca8 5dbf6df8a161325bb7ff28a883f2fb76 \
    7102bf9898a5ed2f812286ff6f062a7f)
fi

source=("$pkgname.desktop"
        "${url}${pkgname}-${pkgver}-linux-${_arch}.tgz"
        "http://arm.konnichi.com/2012/01/01/core/os/${CARCH}/curl-7.23.1-2-${CARCH}.pkg.tar.xz")

package() {
  local _srcdir="$srcdir/$_pkgname-$pkgver"
  local _pkgdir="$pkgdir/opt/$pkgname"
  local _tlibdir="$_pkgdir/runtime/1.2.0.RC3"
  local _slibdir="$_srcdir/runtime/1.2.0.RC3"

  cd "$srcdir"

  # install desktop file
  mkdir -p "$pkgdir/usr/share/applications"
  install -m 644 $pkgname.desktop "$pkgdir/usr/share/applications/"

  cd "$_srcdir"

  # install licence file
  mkdir -p "$pkgdir/usr/share/licenses/$pkgname"
  cp LICENSE.txt "$pkgdir/usr/share/licenses/$pkgname"

  mkdir -p "$_pkgdir"

  # install app
  cp -R Resources {ti,}manifest tiapp.xml installer modules "$_pkgdir"

  # install binary
  install -T -m 755 "$_pkgname" "$_pkgdir/$pkgname"

  # install libraries
  mkdir -p "$pkgdir/usr/lib/$pkgname"
  mkdir -p "$_tlibdir"

  cd "$_slibdir"
  for lib in libPoco*.so.9 libicu*.so.42 libkhost.so libmodman.so.1; do
    install -m 755 "$_slibdir/$lib" "$pkgdir/usr/lib/$pkgname"
    ln -s "/usr/lib/$pkgname/$lib" "$_tlibdir"
  done

  # install certificate
  install -m 644 "$_slibdir/rootcert.pem" "$_tlibdir"

  # link system libraries
  for lib in gio libhspell.so.0 libsoup-{gnome-,}2.4.so.1 \
    libgcrypt.so libg{lib,io,object,module,thread}-2.0.so.0 \
    libwebkitgtk-1.0.so.0; do
    ln -s "/usr/lib/${lib}" "$_tlibdir"
  done

  ln -s "/usr/share/webkitgtk-1.0/webinspector" "$_tlibdir"
  ln -s "/lib/libgcrypt.so.11" "$_tlibdir"

  # and now... some dirty, dirty hacks.........
  ln -s "/usr/lib/libgnutls.so.28" "$_tlibdir/libgnutls.so.26"
  ln -s "/usr/lib/libproxy.so.1.0.0" "$_tlibdir/libproxy.so.0"
  ln -s "/usr/lib/libnotify.so.4" "$_tlibdir/libnotify.so.1"
  ln -s "/usr/lib/libcrypto.so.1.0.0" "$_tlibdir/libcrypto.so.0.9.8"
  ln -s "/usr/lib/libssl.so.1.0.0" "$_tlibdir/libssl.so.0.9.8"

  # extract library from curl 7.23 as 7.24 stopped working
  tar -x -a -C $_tlibdir --strip 2 \
    -f "$srcdir/curl-7.23.1-2-${CARCH}.pkg.tar.xz" usr/lib/libcurl.so.4.2.0
  ln -s "libcurl.so.4.2.0" "$_tlibdir/libcurl.so.4"
 
  # install 'docs'
  mkdir -p "$pkgdir/usr/share/doc/$pkgname"
  cd "$_pkgdir/Resources/"
  mv CHANGELOG{,.txt} README "$pkgdir/usr/share/doc/$pkgname"

  # install small shell script in /usr/bin
  mkdir -p "$pkgdir/usr/bin"
  cat > "$pkgdir/usr/bin/$pkgname" << EOF
#!/bin/sh
LD_LIBRARY_PATH=/opt/$pkgname/runtime/1.2.0.RC3 /opt/$pkgname/$pkgname
EOF
  chmod 755 "$pkgdir/usr/bin/$pkgname"
}

# vim:set ts=2 sw=2 et:
