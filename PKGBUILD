# Maintainer: Masahiro Sakai <masahiro.sakai@gmail.com>

_realname=liblbfgsb
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}"
pkgver=3.0
pkgrel=1
pkgdesc="L-BFGS-B - Software for Large-scale Bound-constrained Optimization (mingw-w64)"
arch=('any')
mingw_arch=('mingw32' 'mingw64' 'clang64')
url='http://users.iems.northwestern.edu/~nocedal/lbfgsb.html'
license=('BSD')
depends=("${MINGW_PACKAGE_PREFIX}-openblas"
        $([[ ${MINGW_PACKAGE_PREFIX} == *-clang-* ]] || echo "${MINGW_PACKAGE_PREFIX}-gcc-libgfortran")
        )
makedepends=($([[ ${MINGW_PACKAGE_PREFIX} == *-clang-* ]] || echo "${MINGW_PACKAGE_PREFIX}-fc")
options=('strip')
source=(Lbfgsb.${pkgver}.tar.gz::http://users.iems.northwestern.edu/~nocedal/Software/Lbfgsb.${pkgver}.tar.gz
        replace-linpack-with-lapack.diff
        silence.diff
        )
sha256sums=('f5b9a1c8c30ff6bcc8df9b5d5738145f4cbe4c7eadec629220e808dcf0e54720'
            '1466cb7f79d233acfd882e0ed4bc75ecabd7d509a071c4997139505de068e2b2'
            '2d5cd46b869b569e72a6d81c45e17224603acf1840263cd394f7ed972f46a621'
            )

# Helper macros to help make tasks easier #
apply_patch_with_msg() {
  for _patch in "$@"
  do
    msg2 "Applying $_patch"
    patch -Np1 -i "${srcdir}/$_patch"
  done
}

prepare() {
  cd "${srcdir}/Lbfgsb.${pkgver}"
  apply_patch_with_msg \
    replace-linpack-with-lapack.diff \
    silence.diff
}

build() {
  if [[ ${MINGW_PACKAGE_PREFIX} != *-clang-* ]]; then
    export FC=gfortran
  else
    export FC=flang
  fi

  [[ -d "${srcdir}/build-${MINGW_CHOST}" ]] && rm -rf "${srcdir}/build-${MINGW_CHOST}"
  mkdir "${srcdir}/build-${MINGW_CHOST}"
  cd "${srcdir}/build-${MINGW_CHOST}"
  ${FC} -O2 -shared -o liblbfgsb.dll -Wl,--out-implib,liblbfgsb.dll.a \
    "${srcdir}/Lbfgsb.${pkgver}/lbfgsb.f" "${srcdir}/Lbfgsb.${pkgver}/timer.f" \
    -lopenblas
}

package() {
  cd "${srcdir}/build-${MINGW_CHOST}"
  install -Dm755 -t "${pkgdir}${MINGW_PREFIX}/bin" liblbfgsb.dll
  install -Dm755 -t "${pkgdir}${MINGW_PREFIX}/lib" liblbfgsb.dll.a

  cd "${srcdir}/Lbfgsb.${pkgver}"
  install -Dm644 -t "${pkgdir}${MINGW_PREFIX}/share/doc/${_realname}" README algorithm.pdf code.pdf
  install -Dm644 -t "${pkgdir}${MINGW_PREFIX}/share/doc/${_realname}" driver1.f driver1.f90 driver2.f driver2.f90 driver3.f driver3.f90
  install -Dm644 -t "${pkgdir}${MINGW_PREFIX}/share/licenses/${_realname}" License.txt
}
