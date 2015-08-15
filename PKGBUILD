# Submitter: Nathan Hulse <nat.hulse@gmail.com>

pkgname=compiz-plugins-main-bzr
pkgver=32
pkgrel=1
pkgdesc="Main plugins pack for Compiz"
url="https://launchpad.net/compiz-plugins-main"
license=('GPL' 'LGPL' 'MIT')
arch=('i686' 'x86_64')
depends=('compiz-core-bzr')
makedepends=('bzr' 'intltool' 'cmake')
provides=('compiz-plugins-main')
conflicts=('compiz-plugins-main')
install='compiz-plugins-main-bzr.install'

# GSettings backend support
GSETTINGS="on"

_bzrtrunk=lp:compiz-plugins-main
_bzrmod=compiz-plugins-main

build() {
  cd "$srcdir"

  msg "Connecting to Launchpad..."
  if [[ -d "$_bzrmod" ]]; then
    cd "$_bzrmod" && bzr pull "$_bzrtrunk" -r "$pkgver"
  else
    bzr branch "$_bzrtrunk" "$_bzrmod" -q -r "$pkgver"
  fi
  msg "Bazaar checkout done or server timeout."

  rm -rf "$srcdir/$_bzrmod-build"
  msg "Creating build directory..."
  cp -r "$srcdir/$_bzrmod" "$srcdir/$_bzrmod-build"
  mkdir "$srcdir/$_bzrmod-build"/build
  cd "$srcdir/$_bzrmod-build/build"

  msg "Running cmake..."   
  cmake .. \
    -DCMAKE_INSTALL_PREFIX="/usr"                     \
    -DCOMPIZ_DESTDIR="${pkgdir}"                      \
    -DCOMPIZ_PLUGIN_INSTALL_TYPE="package"            \
    -DCOMPIZ_BUILD_WITH_RPATH=FALSE                   \
    -DCOMPIZ_PACKAGING_ENABLED=TRUE                   \
    -DUSE_GSETTINGS="${GSETTINGS}"                    \
    -DCOMPIZ_BUILD_TESTING=Off                        \
    -DCOMPIZ_DISABLE_SCHEMAS_INSTALL=On               \
    -DCOMPIZ_DISABLE_PLUGIN_COLORFILTER=Off           \
    -DCOMPIZ_DISABLE_PLUGIN_WORKAROUNDS=Off           \
    -DCOMPIZ_DISABLE_PLUGIN_TEXT=Off                  \
    -DCOMPIZ_DISABLE_PLUGIN_SCALEADDON=Off            \
    -DCOMPIZ_DISABLE_PLUGIN_MOUSEPOLL=Off             \
    -DCOMPIZ_DISABLE_PLUGIN_MAG=Off                   \
    -DCOMPIZ_DISABLE_PLUGIN_TITLEINFO=Off             \
    -DCOMPIZ_DISABLE_PLUGIN_SHIFT=Off                 \
    -DCOMPIZ_DISABLE_PLUGIN_SESSION=Off               \
    -DCOMPIZ_DISABLE_PLUGIN_ANIMATION=Off             \
    -DCOMPIZ_DISABLE_PLUGIN_EXPO=Off                  \
    -DCOMPIZ_DISABLE_PLUGIN_SNAP=Off                  \
    -DCOMPIZ_DISABLE_PLUGIN_RING=Off                  \
    -DCOMPIZ_DISABLE_PLUGIN_PUT=Off                   \
    -DCOMPIZ_DISABLE_PLUGIN_WALL=Off                  \
    -DCOMPIZ_DISABLE_PLUGIN_IMGJPEG=Off               \
    -DCOMPIZ_DISABLE_PLUGIN_STATICSWITCHER=Off        \
    -DCOMPIZ_DISABLE_PLUGIN_VPSWITCH=Off              \
    -DCOMPIZ_DISABLE_PLUGIN_RESIZEINFO=Off            \
    -DCOMPIZ_DISABLE_PLUGIN_WINRULES=Off              \
    -DCOMPIZ_DISABLE_PLUGIN_OPACIFY=Off               \
    -DCOMPIZ_DISABLE_PLUGIN_KDECOMPAT=Off             \
    -DCOMPIZ_DISABLE_PLUGIN_NEG=Off                   \
    -DCOMPIZ_DISABLE_PLUGIN_THUMBNAIL=Off             \
    -DCOMPIZ_DISABLE_PLUGIN_EZOOM=Off                 \
    -DCOMPIZ_DISABLE_PLUGIN_GRID=Off
  make
}

package() {
  cd "$srcdir/$_bzrmod-build/build"
  make install

  # Merge the gconf schema files
  if [ -d "${pkgdir}/usr/share/gconf/schemas" ]; then    
    gconf-merge-schema "${pkgdir}/usr/share/gconf/schemas/compiz-plugins-main.schemas.in" "{$pkgdir}"/usr/share/gconf/schemas/*.schemas
    sed -i '/<schemalist\/>/d' "${pkgdir}/usr/share/gconf/schemas/compiz-plugins-main.schemas.in"
    rm -f "${pkgdir}"/usr/share/gconf/schemas/*.schemas
    mv "${pkgdir}/usr/share/gconf/schemas/compiz-plugins-main.schemas.in" "${pkgdir}/usr/share/gconf/schemas/compiz-plugins-main.schemas"
  fi

  # Add the pesky gsettings schema files manually
  ls generated | grep -qm1 .gschema.xml
  if [ $? -eq 0 ]; then
    msg "Adding GSettings schema files..."
    install -dm755 "${pkgdir}/usr/share/glib-2.0/schemas/" 
    install -m644 generated/*.gschema.xml "${pkgdir}/usr/share/glib-2.0/schemas/" 
  fi
}
