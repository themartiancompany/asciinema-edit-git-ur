# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Malachi Soord <me@malachisoord.com>

_git="true"
_pkg=asciinema-edit
pkgname="${_pkg}"
pkgver=0.0.6.1
_commit="28a7d761cd7cb112f23e43e2f2f948542cc0fc9f"
pkgrel=1
pkgdesc="asciinema casts post-production tools."
_http="https://github.com"
_ns="themartiancompany"
url="https://github.com/${_ns}/${_pkg}"
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'armv7l'
  'armv6l'
  'mips'
  'pentium4'
  'powerpc'
)
makedepends=(
  'go'
)
license=(
  'AGPL3'
)
provides=(
  "${_pkg}=${pkgver}"
  "go-${_pkg}=${pkgver}"
)
conflicts=(
  "${_pkg}"
  "go-${_pkg}"
)
_branch="master"
_tag_name="branch"
_tag="${_branch}"
_tarname="${_pkg}-${_tag}"
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    "git"
  )
  _src="${_tarname}::git+${url}#${_tag_name}=${_tag}"
  _sum="SKIP"
elif [[ "${_git}" == "false" ]]; then
  _src="${_tarname}.zip::${url}/archive/refs/heads/${_tag}.zip"
  _sum='SKIP'
fi
source=(
  "${_src}"
)
sha512sums=(
  "${_sum}"
)

_nth() {
  local \
    _str="${1}" \
    _n="${2}"
  echo \
    "${_str}" | \
    awk \
      -F '+' \
      '{print $'"${_n}"'}'
}

_jq_pkgver() {
  local \
    _version \
    _rev \
    _commit
  _version="$( \
    curl \
      --silent \
      "${_gh_api}/tags" | \
      jq \
        '.[0].name')"
  _version_commit="$( \
    curl \
      --silent \
      "${_gh_api}/tags" | \
      jq \
        '.[0].commit.sha')"
  _rev="$( \
    curl \
      --silent \
      "${_gh_api}/commits" | \
      jq \
        'map(.sha == '${_version_commit}' ) | index(true)')"
  _commit="$( \
    curl \
      --silent \
      "${_gh_api}/commits" | \
      jq \
        '.[0].sha')"
  printf \
    "%s.r%s.g%s" \
    "${_version}" \
    "${_rev}" \
    "${_commit}"
}

_parse_ver() {
  local \
    _pkgver="${1}" \
    _out="" \
    _ver \
    _rev \
    _commit
  _ver="$( \
    _nth \
      "${_pkgver}" \
      "1")"
  _rev="$( \
    _nth \
      "${_pkgver}" \
      "2")"
  _commit="$( \
    _nth \
      "${_pkgver}" \
      "3")"
  _out=${_ver}
  [[ "${_rev}" != "" ]] && \
    _out+=".r${_rev}"
  [[ "${_commit}" != "" ]] && \
    _out+=".${_commit}"
  echo \
    "${_out}"
}

_git_pkgver() {
  local \
    _pkgver
  _pkgver="$( \
    git \
      describe \
      --tags \
      --long | \
      sed \
        's/-/+/g')"
  _parse_ver \
    "${_pkgver}"
}

pkgver() {
  cd \
    "${_tarname}"
  if [[ "${_git}" == true ]]; then
    _git_pkgver
  elif [[ "${_git}" == false ]]; then
    _jq_pkgver
  fi
}



prepare() {
  cd \
    "${_tarname}"
  git \
    submodule \
      update \
        --init \
        --recursive
}

build() {
  local \
    _go_opts=() \
    _go_flags=()
  _go_flags=(
    -buildmode=pie
    -trimpath
    -ldflags=-linkmode=external
    -mod=readonly
    -modcacherw
  )
  _go_opts=(
    -tags
      "netgo"
    -v
    -a
    -ldflags
      "-X main.version=${pkgver} -extldflags \"-static\""
  )
  export \
    CGO_CPPFLAGS="${CPPFLAGS}" \
    CGO_CFLAGS="${CFLAGS}" \
    CGO_CXXFLAGS="${CXXFLAGS}" \
    CGO_LDFLAGS="${LDFLAGS}" \
    GOFLAGS="${_go_flags[*]}"
  cd \
    "${_tarname}"
  go \
    build \
      -o build \
      "${_pkg}"
      # "${_go_opts[@]}" \
      # "main.go"
}

package() {
  install \
    -Dm755 \
    "${_pkg}" \
    "${pkgdir}/usr/bin/${_pkg}"
  install \
    -Dm644 \
    "LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}"
  install \
    -Dm644 \
    "README.md" \
    "${pkgdir}/usr/share/doc/${pkgname}/README.md"
}


