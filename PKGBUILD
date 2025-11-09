# Maintainer: dreamscache.d <dreamscache.d@gmail.com>
_pkgname=recaf
pkgname=${_pkgname}-git
pkgver=r5065.7bbe60022
pkgrel=1
pkgdesc="A modern Java bytecode editor"
arch=("any")
url="https://github.com/Col-E/Recaf"
license=("MIT")
depends=("java-runtime" "java-openjfx-bin" "ttf-font")
makedepends=("git" "java-environment" "jdk-openjdk")
provides=("$_pkgname")
conflicts=("$_pkgname" "$_pkgname-bin")
replaces=("recaf")
source=("recaf::git+https://github.com/Col-E/Recaf#branch=master"
        "atlantafx::git+https://github.com/Col-E/atlantafx.git#branch=recaf-theme-2x-alt")
md5sums=("SKIP"
         "SKIP")

pkgver() {
  cd "$srcdir/$_pkgname"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  export MAVEN_OPTS=""
  MAVEN_OPTS+="-Dmaven.repo.local=${startdir}/maven "
  MAVEN_OPTS+="-Daether.dependencyCollector.impl=bf "
  MAVEN_OPTS+="-Dmaven.artifact.threads=16"

  cd "$srcdir/$_pkgname"

  patch --forward --strip=1 --input="${startdir}/gradle.patch"

  patch --forward --strip=1 --input="${startdir}/skip-downgrade-test.diff"

  patch --forward --strip=1 --input="${startdir}/light-theme-recaf.patch"
  patch --forward --strip=1 --input="${startdir}/light-theme-syntax-highlighting.patch"

  cd "$srcdir/atlantafx"

  patch --forward --strip=1 --input="${startdir}/light-theme-atlantafx.patch"

  hebi_path="$srcdir/$_pkgname/.hebi"
  pom_paths=(
    "pom.xml"
    "base/pom.xml"
    "sampler/pom.xml"
    "styles/pom.xml"
  )
  for pom_path in "${pom_paths[@]}"; do
    sed --in-place --regexp-extended \
      "s@^(\s*)(<sassVersion>[^<]+</sassVersion>)@\1<downloadDirectory>${hebi_path}</downloadDirectory>\n\1\2@g" \
      $pom_path
  done

  mvn clean install --batch-mode --file styles/pom.xml
  cp styles/dist/recaf.css "$srcdir/$_pkgname/recaf-ui/src/main/resources/style/"
}

build() {
  cd "$srcdir/$_pkgname"
  export GRADLE_USER_HOME="$srcdir/$_pkgname/.gradle"
  ./gradlew --no-daemon build
}

package() {
  cd "$srcdir/$_pkgname"
  install -Dm644 "LICENSE" "$pkgdir/usr/share/licenses/$_pkgname/LICENSE"
  install -Dm755 "recaf-ui/build/libs/recaf-ui-"*"-all.jar" "$pkgdir/usr/share/java/$_pkgname/$_pkgname.jar"
  install -Dm644 "recaf-ui/src/main/resources/icons/logo-full.png" "$pkgdir/usr/share/pixmaps/recaf.png"
  printf '#!/usr/bin/env bash\nexec java -jar "/usr/share/java/%s/%s.jar" "$@"' "$_pkgname" "$_pkgname" > "recaf"
  printf \
"[Desktop Entry]\n"\
"Type=Application\n"\
"Version=1.0\n"\
"Name=Recaf\n"\
"Comment=%s\n"\
"Path=/usr/bin\n"\
"Exec=sh -c \\\"if [ -n \"\$0\" ]; then recaf --input=\"\$0\"; else recaf; fi\\\" %%u\n"\
"Icon=recaf\n"\
"Terminal=false\n"\
"MimeType=application/java-archive\n"\
"Categories=Development;Java"\
"$pkgdesc" > "recaf.desktop"
  install -Dm755 "recaf" "$pkgdir/usr/bin/recaf"
  install -Dm644 "recaf.desktop" "$pkgdir/usr/share/applications/recaf.desktop"
}
