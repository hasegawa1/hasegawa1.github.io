# 環境構築
Windows 10 Fall Creators Update 上でC言語とTeXの環境構築をした記録です。(2018年3月1日)

## Windows Subsystem for Linux の有効化と Ubuntu のインストール

Linux の命令を使うために WSL を導入します。仮想環境などの方法もあります。

1. [設定]→[アプリ]→[プログラムと機能]→[Windowsの機能の有効化または無効化]と進む。
2. Windows Subsystem for Linux にチェックを入れる。
3. WSLのインストールが始まる。インストール終了後、再起動する。
4. Microsoft Store の検索窓に WSL と入力すると予測変換で[Windows で Linux を実行する]が出てくるので、それをクリック。
5. Ubuntu を選択し、[入手]をクリックしてインストールした後、[起動]をクリックして起動する。
6. ユーザー名とパスワードを入力する。

参考文献: 
- [WSL(Windows Subsystem for Linux)を使ってみた](https://qiita.com/Brutus/items/f26af71d3cc6f50d1640)
- [Cygwin絶対殺すマン ～物理のオタクがWindows Subsystem for Linuxで数値計算できるようになるまで～](https://qiita.com/PikkamanV/items/d308927c395d6e687a6a)
- [Windows10でWSLとVSCodeを使ってプログラミング環境を整える](https://qiita.com/yokanyukari/items/37421f497b7ffaa75502)
- [Visual Studio CodeでWSLのgitを利用してソース管理を行う](https://qiita.com/xeres/items/ed4d659cfac4a1695f4b)
- [Windows Subsystem for Linux + X Windowを1.024倍くらい使いこなすための方法](https://qiita.com/nishemon/items/bb3aca972404f68bfcd6)


## Visial Studio Code のインストール
エディタには Visual Studio Code を使う。執筆時点での最新バージョンは1.20.1。このバージョンから Python のディストリビューション Anaconda と同時インストールができるようになった。Python も使う場合はそれを利用するのがよい。

1. [https://code.visualstudio.com/](https://code.visualstudio.com/)からダウンロード。
2. .exeファイルを実行。
3. [次へ]
4. [同意する]にチェックを入れて[次へ]
5. インストール先を指定。初期設定で問題なし。
6. スタートメニューにショートカットを作成。初期設定で問題なし。
7. 追加のタスクを選択。PATH だけでよい(?)。
8. インストール
9. 完了


## Git for Windows のインストール

分散型バージョン管理システムの Git をインストールします。wslgit なるものもあります。執筆時点での最新バージョンは2.16.2。

1. [https://git-scm.com/](https://git-scm.com/)からダウンロード。
2. .exeファイルを実行。
3. ライセンスを確認して Next。
4. インストール先を指定。初期設定で問題なし。
5. コンポーネントの設定
    - Additional Icons - On the Desktop
        - アイコンをデスクトップに置く設定。不要なのでチェックを外す。
    - Windows Explorer Integration
        - ファイルやディレクトリを右クリックしたときのメニューに追加するかの設定。不要なのでチェックを外す。
    - Git LFS
        - 使うのでチェック。
    - Use a TrueType font in all console windows
        - 文字化けが起きるのでチェックを外す。 
6. スタートメニューにショートカットを作成。初期設定で問題なし。
7. デフォルトのエディターを聞かれる。Visual Studio Code を選択。
8. 環境変数の設定。今回はデフォルトのまま進める。
    - Use Git Bash only
        - 環境変数を汚さない。Cygwin等を導入している場合はこれ。
    - Use Git from the Windows Command Prompt
        - コマンドプロンプトやPowerShellからGitのコマンドだけが使用できるように。
    - Use Git and included Unix tools from the Windows Command Prompt
        - gitだけでなく、他のlsやlessなどをコマンドプロンプトやPowerShellから使用可能になります。 
9. SSLライブラリの選択。初期設定で問題なし。
10. 改行の取り扱いの設定。今回はデフォルトのまま進める。
    - Checkout Windows-style, commit Unix-style line endings
        - チェックアウト時に改行コードはCR LFに変更され、コミット時には全てLFに変換される。
    - Checkout as-is, commit Unix-style line endings
        - チェックアウト時は改行コードについては何もしない、コミット時には全てLFに変換される。
    - Checkout as-is, commit as-is
        - 改行コードについては何もしない。
11. Gitで使用するコンソールをCygwinで使用されているMinTTYを使用するか、Windowsの標準コンソールを使用するか選択。Windowsの標準コンソールを選択。
12. gitconfigについての設定。とりあえずデフォルトのまま進める。
    - Enable file system caching
        - git status時などでの応答速度を改善するfscacheキャッシュを有効にするかしないかオプション
    - Enable Git Credential Manager
        - GitHub等にアクセスする際の二段階認証に対応するかのオプション
    - Enable symbolic links
        - シンボリックリンクの使用/不使用のオプション 
13. Install をクリックするとインストール開始。
14. Finish で完了。

参考文献: 
- [自分用 Git For Windowsのインストール手順](https://qiita.com/toshi-click/items/dcf3dd48fdc74c91b409)
- [こっそり始めるGit／GitHub超入門](http://www.atmarkit.co.jp/ait/series/3190/)
- [今日からはじめるGitHub 〜 初心者がGitをインストールして、プルリクできるようになるまでを解説](https://employment.en-japan.com/engineerhub/entry/2017/01/31/110000#%E7%92%B0%E5%A2%83%E3%81%AE%E6%A7%8B%E7%AF%892-SSH%E3%81%AE%E9%8D%B5%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B)

## VcXsrv のインストール
Windows 用の X Server の VcXsrv をインストールする。Xming は寄付版ならまだいいらしい。執筆時点での最新バージョンは1.19.6.0。

1. [https://sourceforge.net/projects/vcxsrv/files/vcxsrv/](https://sourceforge.net/projects/vcxsrv/files/vcxsrv/)からダウンロード。
2. .exeファイルを実行。
3. コンポーネントの選択。デスクトップへのショートカットを外した。
4. インストール先を指定。初期設定で問題なし。
5. Close で終了。
6. スタートメニューで vcxsrv を検索すれば起動できる。
7. タスクバーの通知領域を開くことで VcXsrv が常駐していることが確認できる。

## ソフトウェアのインストール

### ダウンロードサーバーの変更
```
sudo sed -i 's/\/archive\.ubuntu/\/jp\.archive\.ubuntu/' /etc/apt/sources.list
```
デフォルトでは、ソフトウェアパッケージのダウンロード元が海外にあるメインサーバーに設定されている。これを、日本のサーバーに変更して早くダウンロードできるようにする。以上のコマンドを実行すると、/etc/apt/sources.list が書き換わる。

### パッケージ情報の更新
```
sudo apt update
```
パッケージのリポジトリから、パッケージの名前やバージョン、依存関係を取得するコマンド。このコマンドで/var/lib/apt以下にあるパッケージ情報が更新される。この情報を元に、パッケージのダウンロードが行われる。Ubuntuを普通にインストールすると定期的に自動で実行されるが、パッケージの更新やインストール前に手動で実行しておくと良い。

### パッケージの更新
```
sudo apt upgrade
or
sudo apt full-upgrade
```
アップグレード可能なパッケージを更新する。full-upgrade は、「保留」とされるパッケージがある場合に実行する。すると、パッケージを削除しないと更新できないパッケージも処理できる。

### 日本語化
```
sudo apt install language-pack-ja
sudo update-locale LANG=ja_JP.UTF-8
```

### gcc
```
sudo apt install build-essential
or
sudo apt install gcc
```

### gfortran
```
sudo apt install gfortean
```

### Git
```
sudo apt install git
```

### LaTeX
```
sudo apt install texlive-full
```

### Emacs
```
sudo apt install emacs
```

### Gnuplot
```
sudo apt install gnuplot-x11
```
-x11 をつけないと terminal の設定がうまくいかないらしい。

参考文献：
- [Windows Subsystem for Linuxをインストールしてみよう！](https://qiita.com/Aruneko/items/c79810b0b015bebf30bb)
- [Bash on Ubuntu on Windows上でTeXLiveのコンパイル&プレビュー環境を構築する（その1）](https://qiita.com/kuro_23/items/6bb7e3d470d04209bd03)
- [Cygwin絶対殺すマン ～物理のオタクがWindows Subsystem for Linuxで数値計算できるようになるまで～](https://qiita.com/PikkamanV/items/d308927c395d6e687a6a)
- [「apt-get」はもう古い？新しい「apt」コマンドを使ったUbuntuのパッケージ管理](https://linuxfan.info/package-management-ubuntu)
- [「Windows Subsystem for Linux(WSL)」セットアップガイド【スクリーンショットつき解説】](https://linuxfan.info/wsl-setup-guide)

## ターミナルの設定
Terminal として [Tilix](https://gnunn1.github.io/tilix-web/) を使う。まずはインストール。
```
sudo apt install tilix
```
また、日本語入力ができるよう umi もインストールする。
```
sudo apt install uim-fep uim-anthy
```
次に、 umi の設定を書く。
```
cat << EOF > ~/.uim
(define default-im-name 'anthy)
(define-key generic-on-key? '("<Control> "))
(define-key generic-off-key? '("<Control> "))
EOF
```
これで Tilix を使っているときに、Ctrl+Space で日本語入力の切り替えができるようになる。もしここを変えたい場合は、<Control> の部分を書き換えればよい。
ここで、Tilix を起動してみる。
```
export DISPLAY=:0.0
gnome-terminal -e uim-fep
```

参考文献：
- [最高のBoW(Bash on Ubuntu on Windows)環境のために](http://mjhd.hatenablog.com/entry/2016/11/27/185902)
- [お前らのWSLはそれじゃダメだ](http://xztaityozx.hatenablog.com/entry/2017/12/01/001544)
- [Windows10でWSLとVSCodeを使ってプログラミング環境を整える](https://qiita.com/yokanyukari/items/37421f497b7ffaa75502)