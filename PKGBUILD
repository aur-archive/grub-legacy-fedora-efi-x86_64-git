# Maintainer : Keshav P R <(the.ridikulus.rat) (aatt) (gemmaeiil) (ddoott) (ccoomm)>

# This PKGBUILD is based on grub.spec at https://pkgs.fedoraproject.org/gitweb/?p=grub.git;a=blob_plain;f=grub.spec;hb=HEAD .

# This grub-legacy package includes support for GPT (default for any UEFI based boot loader) and for reading EXT4 filesystem (/boot can be ext4).

_actualname="grub-legacy"
_pkgname="${_actualname}-fedora-efi-x86_64"
pkgname="${_pkgname}-git"

pkgver=20120429
pkgrel=1
pkgdesc="Fedora's grub-legacy fork - compiled for UEFI systems"
url="https://git.kernel.org/?p=boot/grub-fedora/grub-fedora.git"
arch=('any')
license=('GPL')

makedepends=('git' 'glibc' 'ncurses' 'diffutils' 'autoconf' 'automake' 'gnu-efi')
depends=('dosfstools' 'efibootmgr')
optdepends=('mtools')
conflicts=('grub-legacy-efi-fedora')

options=('!strip' '!emptydirs' '!libtool' '!buildflags' '!makeflags')
install="${_pkgname}.install"

source=()

_gitroot="git://github.com/vathpela/grub-fedora.git"
_gitname="grub-legacy-fedora"
_gitbranch="master"

_update_git() {
	
	cd "${srcdir}/"
	
	msg "Connecting to GIT server...."
	
	if [[ -d "${srcdir}/${_gitname}/" ]]; then
		cd "${srcdir}/${_gitname}/"
		git reset --hard
		
		git remote rm origin
		git remote add origin "${_gitroot}"
		
		git fetch
		git checkout "${_gitbranch}"
		git merge "remotes/origin/${_gitbranch}"
		msg "The local GIT repo has been updated."
	else
		git clone "${_gitroot}" "${_gitname}"
		cd "${srcdir}/${_gitname}/"
		git checkout "${_gitbranch}"
		msg "GIT checkout done or server timeout"
	fi
	
	echo
	
}

build() {
	
	if [[ "${CARCH}" != "x86_64" ]]; then
		echo "${pkgname} package can be built only in a x86_64 system. Exiting."
		exit 1
	else
		_update_git
		echo
	fi
	
	rm -rf "${srcdir}/${_gitname}_build/" || true
	cp -r "${srcdir}/${_gitname}" "${srcdir}/${_gitname}_build"
	
	cd "${srcdir}/${_gitname}_build/"
	
	sed "s|0\.97|0\.97-${pkgver}|" -i "${srcdir}/${_gitname}_build/configure.in" || true
	# sed "s|0\.97|0\.97-${pkgver}|" -i "${srcdir}/${_gitname}_build/configure.ac" || true
	echo
	
	unset CFLAGS
	unset CXXFLAGS
	
	autoreconf
	echo
	
	autoconf
	echo
	
	_CFLAGS="-Os -g -fno-strict-aliasing -fno-stack-protector -fno-reorder-functions -Wall -Wno-error -Wno-shadow -Wno-unused -Wno-pointer-sign"
	
	CFLAGS="${_CFLAGS}" ./configure \
		--with-platform="efi" \
		--disable-auto-linux-mem-opt \
		--without-curses \
		--libdir="/usr/lib"
	echo
	
	make clean || true
	echo
	
	CFLAGS="${_CFLAGS}" make
	echo
	
}


package() {
	
	cd "${srcdir}/${_gitname}_build/"
	
	install -D -m0644 "${srcdir}/${_gitname}_build/efi/grub.efi" "${pkgdir}/boot/efi/EFI/arch/grub-legacy/grub-legacy-x64.efi"
	
}
