_reponame=recaf
_installname=recaf
pkgname=${_reponame}-git
pkgver=r4448.02ba1332c
pkgrel=1
pkgdesc="A modern Java bytecode editor."
arch=("any")
url="https://github.com/Col-E/Recaf/tree/master"
license=("MIT")
depends=("java-runtime" "java-openjfx-bin" "ttf-font")
makedepends=("git" "java-environment" "jdk-openjdk")
provides=($_installname)
source=("recaf::git+https://github.com/Col-E/Recaf"
        "atlantafx::git+https://github.com/Col-E/atlantafx.git#branch=recaf-theme-2x-alt"
        "recaf-wrapper"
        "recaf.desktop")
sha256sums=('SKIP'
            'SKIP'
            '8611012fed174565ea32c1979f7a627a879721a5f089dc1122ce707dd9c4a068'
            'b932bc9c97ab892c0ee40899a40795176c75bf186d7627e68ae5086ccbf3047d')

pkgver() {
    cd "$srcdir/$_reponame"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
    cd "$srcdir/$_reponame"
    patch --forward --strip=1 --input="${startdir}/light-theme-recaf.patch"
    patch --forward --strip=1 --input="${startdir}/light-theme-syntax-highlighting.patch"

    patch --forward --strip=1 --input="${startdir}/gradle.patch"
    patch --forward --strip=1 --input="${startdir}/ignore_JavacCompilerTest.patch"

    cd "$srcdir/atlantafx"
    patch --forward --strip=1 --input="${startdir}/light-theme-atlantafx.patch"
    mvn clean install --batch-mode --file styles/pom.xml
    cp styles/dist/recaf.css "$srcdir/$_reponame/recaf-ui/src/main/resources/style/"
}

build() {
    cd "$srcdir/$_reponame"
    ./gradlew build
}

package() {
    cd "$srcdir/$_reponame"

    install -Dm644 "LICENSE" "$pkgdir/usr/share/licenses/$_installname/LICENSE"

    install -Dm755 \
            "recaf-ui/build/libs/recaf-ui-4.0.0-SNAPSHOT-all.jar" \
            "$pkgdir/usr/share/java/$_installname/$_installname.jar"

    install -Dm644 "recaf-ui/src/main/resources/icons/logo.png" "$pkgdir/usr/share/pixmaps/recaf.png"
    install -Dm644 "$srcdir/recaf.desktop" "$pkgdir/usr/share/applications/recaf.desktop"


    install -Dm755 "$srcdir/recaf-wrapper" "$pkgdir/usr/bin/recaf"
}
