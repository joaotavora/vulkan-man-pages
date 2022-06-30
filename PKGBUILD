# Maintainer: otreblan <otreblain@gmail.com>
# Modified PKGBUILD from vulkan-html-docs

pkgname="vulkan-man-pages"
pkgver=1.2.179
pkgrel=1
pkgdesc="Vulkan man pages as manpages"
arch=("any")
url="https://github.com/KhronosGroup/Vulkan-Docs"
license=('custom')
makedepends=(
	"asciidoctor"
	"cmake"
	"ghostscript"
	"git"
	"nodejs"
	"npm"
	"python3"
	"source-highlight"
)
source=("$url/archive/v$pkgver.tar.gz" "manpage.patch")
sha256sums=(
	'0005b0b7b3019e82a73e95fafcb419f92c8187a527b55e337f0b0d25f0ae17e1'
	'd986ff98bf57dfbab25ea11b487f38402394ef923e9f6702c1f1c56560df7f92'
)

prepare() {
	npm install escape-string-regexp@4.0.0
	npm install he
	cd "$srcdir"

	mv ${url##*/}-$pkgver $pkgname-$pkgver

	patch --strip=1 < ../manpage.patch
}

build() {
	cd "$srcdir/$pkgname-$pkgver"

	./makeAllExts -j html manhtmlpages styleguide registry
}

package() {
	cd "$srcdir/$pkgname-$pkgver"

	install -dm755 "$pkgdir/usr/share/man/man3v"
	install -dm755 "$pkgdir/usr/share/licenses/$pkgname"

	# correct artifacts of generated manpages
	local fixcode='-e s/<code>/\\fB/g -e s|</code>|\\fR|g'
	local fixstrong='-e s|<strong\sclass=\"purple\">|\\fI|g -e s|</strong>|\\fR|g'
	local fixlinks_e='s/.URL.*"(.*)\.html"\s"[^"]*"([\s]+"(,)")?/.IR "\\fI\1\\fR\\^(3v\\|)"\3/g'
	find gen/out/man/html -iname 'vk*.3v' |
	    xargs -I{} sed -E -i ${fixcode} ${fixstrong} -e "${fixlinks_e}" {}
	find gen/out/man/html -name 'PFN*.3v' |
	    xargs -I{} sed -E -i ${fixcode} ${fixstrong} -e "${fixlinks_e}" {}

	cp -v gen/out/man/html/* "$pkgdir/usr/share/man/man3v"

	install -m644 config/copyright-ccby.txt $pkgdir/usr/share/licenses/$pkgname/copyright-ccby.txt
	install -m644 copyright-spec.txt $pkgdir/usr/share/licenses/$pkgname/copyright-spec.txt
}
