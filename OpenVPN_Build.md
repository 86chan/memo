
# OpenVPN Build

- [OpenVPN Build](#openvpn-build)
  - [Source](#source)
  - [OS](#os)
  - [追加パッケージ](#追加パッケージ)
  - [ディレクトリ構成](#ディレクトリ構成)
  - [必須なやつ](#必須なやつ)
    - [OpenSSL build](#openssl-build)
    - [libnl-genl-3.0](#libnl-genl-30)
      - [ソースからインストール](#ソースからインストール)
    - [libcap-ng](#libcap-ng)
      - [ソースからインストール](#ソースからインストール-1)
    - [libsystemd-daemon](#libsystemd-daemon)
    - [libpam](#libpam)
    - [cmocka](#cmocka)
      - [ソースからインストール](#ソースからインストール-2)
    - [OpenVPN build](#openvpn-build-1)
  - [参考](#参考)

## Source
* OpenVPN  
  <https://github.com/OpenVPN/openvpn/releases>

* OpenSSL  
  <https://github.com/openssl/openssl/releases>

* libnl 3  
  <https://github.com/thom311/libnl/releases>

* libcap-ng  
  <https://github.com/stevegrubb/libcap-ng/releases>

* cmocka  
  <https://gitlab.com/cmocka/cmocka/-/tags>

## OS
Oracle Linux 8.10

## 追加パッケージ
* perl
* cmake
* Development Tools
* libnl3-devel
* pkgconfig
* libcap-ng
* libsystemd-daemon
* libpam
* cmocka

## ディレクトリ構成
```text
User dir
  └── openvpn_build/
        ├── cmocka/                // 成果物
        ├── cmocka-cmocka-1.1.7/   // 最新ソース
        ├── libnl/
        ├── libnl-3.10.0/
        ├── openssl/
        ├── openssl-3.3.1/
        ├── openvpn/
        └── openvpn-2.6.12/
```

## 必須なやつ

```sh
# Development Tools
sudo yum group install "Development Tools"

# pkgconfig, perl, cmake
sudo yum install pkgconfig \
                 perl \
                 cmake

# ビルド用ディレクトリ作成
mkdir ${HOME}/openvpn_build

# エイリアスしとく
export OVPN_BUILD_HOME=${HOME}/openvpn_build
```

### OpenSSL build
1. ダウンロード・解凍
    ```sh
    # openvpn_buildに移動
    cd ${OVPN_BUILD_HOME}

    # DL
    wget # https://github.com/openssl/openssl/releases から最新バージョンを落とす

    # 解凍
    tar -xf openssl-*.tar.gz
    ```

2. 構成
    ```sh
    # 移動
    cd openssl-*

    # shared 静的ライブラリ
    # --prefix インストール先
    ./config shared --prefix=${OVPN_BUILD_HOME}/openssl
    ```

3. ビルド
    ```sh
    # ビルド
    make

    # ビルド確認
    make test

    # インストール
    make install
    ```

4. 環境変数(一時)
    ```sh
    # エイリアス
    alias openssl=${OVPN_BUILD_HOME}/openssl/bin/openssl

    # 環境変数
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${OVPN_BUILD_HOME}/openssl/lib64
    export LIB_OPENSSL=${OVPN_BUILD_HOME}/openssl/lib64

    export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:${LIB_OPENSSL}/pkgconfig
    ```

### libnl-genl-3.0
1. 必要なパッケージをインストール
    ```sh
    # インストール
    sudo yum install libnl3-devel

    # 確認
    pkg-config --modversion libnl-3.0
    pkg-config --modversion libnl-genl-3.0

    ```

#### ソースからインストール
2. ダウンロード & 解凍
    ```sh
    # openvpn_buildに移動
    cd ${OVPN_BUILD_HOME}

    # DL
    wget # https://github.com/thom311/libnl/releases から最新バージョンを落とす

    # 解凍
    tar -xf libnl-*.tar.gz
    ```

3. 構成
    ```sh
    # 移動
    cd libnl-*
    
    # 構成
    ./configure --prefix=${OVPN_BUILD_HOME}/libnl
    ```

4. ビルド & インストール
    ```sh
    # ビルド
    make

    # インストール
    make install
    ```

5. 環境変数
    ```sh
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${OVPN_BUILD_HOME}/libnl/lib/
    ```

### libcap-ng
1. 必要なパッケージをインストール
    ```sh
    # インストール
    sudo yum makecache && sudo yum install libcap-ng-devel pkgconfig

    # 確認
    pkg-config --modversion libcap-ng
    ```

#### ソースからインストール
1. ダウンロード & 解凍
    ```sh
    # openvpn_buildに移動
    cd ${OVPN_BUILD_HOME}

    # DL
    wget # https://github.com/stevegrubb/libcap-ng/releases から最新バージョンを落とす

    # 解凍
    tar -xf libcap-ng-*.tar.gz
    ```

2. 構成
    ```sh
    # 移動
    cd libcap-ng-*
    
    # 構成
    ./configure --prefix=${OVPN_BUILD_HOME}/libcap-ng
    ```

5. ビルド & インストール
    ```sh
    # ビルド
    make

    # インストール
    make install
    ```

6. 環境変数
    ```sh
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${OVPN_BUILD_HOME}/libcap-ng/lib/
    ```

### libsystemd-daemon
1. 必要なパッケージをインストール
    ```sh
    sudo yum install systemd-devel pkgconfig
    ```

2. 環境変数
    ```sh
    export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:/usr/lib/pkgconfig:/usr/share/pkgconfig
    
    export libsystemd_CFLAGS=$(pkg-config --cflags libsystemd)
    export libsystemd_LIBS=$(pkg-config --libs libsystemd)
    ```

### libpam
1. 必要なパッケージをインストール
    ```sh
    sudo yum install cmocka cmocka-devel pkgconfig
    ```

2. 環境変数
    ```sh
    export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:/usr/lib/pkgconfig:/usr/share/pkgconfig
    
    export libpam_CFLAGS=$(pkg-config --cflags libpam)
    export libpam_LIBS=$(pkg-config --libs libpam)
    ```

### cmocka
1. 必要なパッケージをインストール
    ```sh
    sudo yum install cmocka cmocka-devel pkgconfig
    ```

#### ソースからインストール
1. ダウンロード & 解凍
    ```sh
    # openvpn_buildに移動
    cd ${OVPN_BUILD_HOME}

    # DL
    wget # https://gitlab.com/cmocka/cmocka/-/tags から最新(or 任意)バージョンを落とす

    # 解凍
    tar -xf cmocka-cmocka-*.tar.gz
    ```

2. 構成
    ```sh
    # 移動
    cd cmocka-cmocka-*
    
    # 準備
    mkdir build
    cd build

    # 構成
    cmake -DCMAKE_INSTALL_PREFIX=${OVPN_BUILD_HOME}/cmocka -DCMAKE_BUILD_TYPE=Release ..
    ```

3. ビルド & インストール
    ```sh
    # ビルド
    make

    # テスト
    make tset

    # インストール
    make install
    ```

4. 環境変数
    ```sh
    export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:${OVPN_BUILD_HOME}/cmocka/lib64/pkgconfig
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${OVPN_BUILD_HOME}/cmocka/lib64/
    ```

### OpenVPN build
1. ダウンロード・解凍
    ```sh
    # openvpn_buildに移動
    cd ${OVPN_BUILD_HOME}

    # DL
    wget # https://github.com/OpenVPN/openvpn/releases から最新バージョンを落とす

    # 解凍
    tar -xf openvpn-*.tar.gz
    ```

2. 構成
    ```sh
    # 移動
    cd openvpn-*

    # 構成
    # export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:/usr/lib/pkgconfig:/usr/share/pkgconfig:${OVPN_BUILD_HOME}/openssl/lib64/pkgconfig:${OVPN_BUILD_HOME}/cmocka/lib64/pkgconfig
    # export PKG_CONFIG_PATH=/usr/lib/pkgconfig:/usr/share/pkgconfig:${OVPN_BUILD_HOME}/openssl/lib64/pkgconfig:${OVPN_BUILD_HOME}/cmocka/lib64/pkgconfig
    
    ./configure --enable-systemd \
                --disable-lz4 \
                --disable-lzo \
                --prefix ${OVPN_BUILD_HOME}/openvpn
                PKG_CONFIG_PATH=/usr/lib/pkgconfig:/usr/share/pkgconfig:${OVPN_BUILD_HOME}/openssl/lib64/pkgconfig:${OVPN_BUILD_HOME}/cmocka/lib64/pkgconfig
    ```

3. ビルド
    ```sh
    # ビルド
    make

    # ビルド確認
    make test

    # インストール
    make install
    ```

4. 確認
    ```sh
    ../openvpn/sbin/openvpn --version
    ```

## 参考
[Qiita: さくらのVPS上にVPNサーバーを建てる](https://qiita.com/girlfellfromsky/items/31229c96783027a85579)
