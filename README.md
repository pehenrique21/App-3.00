name: Build APK
on:
  workflow_dispatch:
  push:
    branches: [main, master]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Abrir o projeto
        run: unzip -q YarisDash.zip
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: android-actions/setup-android@v3
      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: 8.9
      - name: Criar chave de assinatura
        run: keytool -genkeypair -keystore $HOME/ci.jks -alias key -keyalg RSA -keysize 2048 -validity 10000 -storepass android -keypass android -dname "CN=YarisDash"
      - name: Compilar APK otimizado
        working-directory: YarisDash
        run: gradle assembleRelease --no-daemon
      - uses: actions/upload-artifact@v4
        with:
          name: YarisDash-apk
          path: YarisDash/app/build/outputs/apk/release/*.apk
