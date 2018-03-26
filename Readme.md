<script type="text/javascript" async
   src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

[View on Github](https://github.com/hasegawa1/hasegawa1.github.io)

論文執筆の環境構築やテンプレートなどの覚書。これを読めばC言語でプログラムを書き、Gnuplotで図を作成し、LuaLaTeXでレポート・論文を書くという工程をGitで管理できるというようなものにするつもり。最初にバージョン情報などをまとめておく。

|date                |2018/3/1|
|OS                  |Windows 10 Home Fall Creators Update|
|bash                |?|
|Git                 |?|
|VSCode              |1.21.1|
|Clang               |?|
|gnuplot             |?|
|LuaTeX-ja           |?|
|LaTeX Workshop      ||
|MathJax             |2.7.3|

# 環境構築

## Windows Subsystem for Linux の有効化と Ubuntu のインストール

Linux の命令を使うために WSL を導入します。仮想環境などの方法もあります。

1. [設定]→[アプリ]→[プログラムと機能]→[Windowsの機能の有効化または無効化]と進む。
2. Windows Subsystem for Linux にチェックを入れる。
3. WSLのインストールが始まる。インストール終了後、再起動する。
4. Microsoft Store の検索窓に WSL と入力すると予測変換で[Windows で Linux を実行する]が出てくるので、それをクリック。
5. 好みのディストリビューションを選択し、[入手]をクリックしてインストールした後、[起動]をクリックして起動する。現在入手可能なのは
- Ubuntu
- OpenSUSE Leap
- OpenSUSE Linux Enterprise Server
- Kali Linux
- Debian GNU/Linux
6. ユーザー名とパスワードを入力する。

参考文献: 
- [WSL(Windows Subsystem for Linux)を使ってみた](https://qiita.com/Brutus/items/f26af71d3cc6f50d1640)
- [Cygwin絶対殺すマン ～物理のオタクがWindows Subsystem for Linuxで数値計算できるようになるまで～](https://qiita.com/PikkamanV/items/d308927c395d6e687a6a)
- [Windows10でWSLとVSCodeを使ってプログラミング環境を整える](https://qiita.com/yokanyukari/items/37421f497b7ffaa75502)
- [Visual Studio CodeでWSLのgitを利用してソース管理を行う](https://qiita.com/xeres/items/ed4d659cfac4a1695f4b)
- [Windows Subsystem for Linux + X Windowを1.024倍くらい使いこなすための方法](https://qiita.com/nishemon/items/bb3aca972404f68bfcd6)

## VcXsrv のインストール
Windows 用の X Server の VcXsrv をインストールする。Xming は寄付版ならまだいいらしい。執筆時点での最新バージョンは1.19.6.0。

1. [https://sourceforge.net/projects/vcxsrv/files/vcxsrv/](https://sourceforge.net/projects/vcxsrv/files/vcxsrv/)からダウンロード。
2. .exeファイルを実行。
3. コンポーネントの選択。デスクトップへのショートカットを外した。
4. インストール先を指定。初期設定で問題なし。
5. Close で終了。
6. スタートメニューで vcxsrv を検索すれば起動できる。
7. タスクバーの通知領域を開くことで VcXsrv が常駐していることが確認できる。

## シェルの基本設定

### ダウンロードサーバーの変更
```
$ sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://ftp.jaist.ac.jp/pub/Linux/ubuntu/%g" /etc/apt/sources.list
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

### タイムゾーンの設定
初期状態では、タイムゾーンが DST となっている。これを日本標準時にするには、以下のコマンドを実行する。
```
sudo dpkg-reconfigure tzdata
```
パスワードを要求されるので、最初に設定したパスワードを入力してEnterを押す。カーソルキーで Asia > Tokyo を選択する。

### 日本語化
```
sudo apt install language-pack-ja
sudo update-locale LANG=ja_JP.UTF-8
```
ここで、`date`コマンドを実行すると
```
2018年 3月 1日 金曜日 12:00:00 JST
```
のように表示される。

### プロンプトの変更
bashでは、PS1 という環境変数によってプロンプトを定義している。従って、export コマンドで PS1 を変更すればいい。デフォルト設定を
```
echo "$PS1"
```
で調べると、
```
\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$
```
というプロンプトになっている。これを「時間:カレントディレクトリ$」というプロンプトに変更するには、以下のコマンドを実行する。ついでに色も変更した。端末の制御文字については参考文献を参照。
```
export PS1="\[\e]0;\t: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;36m\]\t\[\033[00m\]: \[\033[01;35m\]\w\[\033[00m\]\$ "
```
これを ~.bashrc に書けば設定が保持される。
bashのプロンプトには、以下のような特殊な文字を使用できる。詳しくは参考文献を参照。

|\a|	ASCIIのベル文字（07）を表示する（ビープ音を鳴らす）|
|\d|	「曜 日 月 日」の形式で日付を表示する|
|\e|	ASCIIのエスケープ文字（033）を表示する|
|\h|	ホスト名のうち最初の「.」までの部分を表示する|
|\H|	ホスト名を表示する|
|\n|	改行する|
|\r|	復帰する|
|\s|	シェルの名前を表示する|
|\t|	現在の時刻を24時間の「HH:MM:SS」形式で表示する|
|\T|	現在の時刻を12時間の「HH:MM:SS」形式で表示する|
|\@|	現在の時刻を12時間の「am/pm」形式で表示する|
|\u|	現在のユーザー名を表示する|
|\v|	bashのバージョンを表示する|
|\V|	bashのリリースを表示する|
|\w|	現在の作業ディレクトリを、ユーザーのホームディレクトリからの絶対パスで表示する|
|\W|	現在の作業ディレクトリを表示する|

参考文献：
- [bashのプロンプトを変更するには](http://www.atmarkit.co.jp/flinux/rensai/linuxtips/002cngprmpt.html)
- [ANSIエスケープコード](https://www.mm2d.net/main/prog/c/console-02.html)

### zsh の利用
```
sudo apt install zsh
```
でインストール。ここで、 ~/.bashrc を編集して以下を書き足す。
```
if test -t 1; then
 exec zsh
fi 
```
これで zsh が起動できる。

参考文献：
- [Windows Subsystem for Linuxをインストールしてみよう！](https://qiita.com/Aruneko/items/c79810b0b015bebf30bb)
- [「apt-get」はもう古い？新しい「apt」コマンドを使ったUbuntuのパッケージ管理](https://linuxfan.info/package-management-ubuntu)
- [「Windows Subsystem for Linux(WSL)」セットアップガイド【スクリーンショットつき解説】](https://linuxfan.info/wsl-setup-guide)
- [apt-getの利用リポジトリを日本サーバーに変更する](https://qiita.com/fkshom/items/53de3a9b9278cd524099)
- [Running oh my zsh on Windows 10](https://winsmarts.com/running-oh-my-zsh-on-windows-10-6fcb0fbc736b)

## ターミナルの設定
この辺りはうまくいかなかったので調べる必要有り。

### Tilix のインストール
ターミナルとして [Tilix](https://gnunn1.github.io/tilix-web/) を使う。まずはインストール。
```
sudo add-apt-repository ppa:webupd8team/terminix
sudo apt update
sudo apt install tilix
```
また、日本語入力ができるよう uim もインストールする。
```
sudo apt install uim-fep uim-anthy
```
次に、 uim の設定を書く。
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

### ショートカットの作成

### テーマの設定
[www.gnome-look.org](www.gnome-look.org) で好きなテーマを探す。今回は OSX-Arc を適用する。
```
mkdir ~/tmp
cd tmp
wget https://github.com/LinxGem33/OSX-Arc-Darker/releases/download/v1.4.5/osx-arc-collection_1.4.5_amd64.deb
sudo dpkg --install ./osx-arc-collection_1.4.5_amd64.deb
rm ./osx-arc-collection_1.4.5_amd64.deb
```
テーマを適用する。
```
GTK_THEME=OSX-Arc-Shadow tilix
```

参考文献：
- [最高のBoW(Bash on Ubuntu on Windows)環境のために](http://mjhd.hatenablog.com/entry/2016/11/27/185902)
- [お前らのWSLはそれじゃダメだ](http://xztaityozx.hatenablog.com/entry/2017/12/01/001544)
- [Windows10でWSLとVSCodeを使ってプログラミング環境を整える](https://qiita.com/yokanyukari/items/37421f497b7ffaa75502)
- [Tilix - GNOME HIGに準拠したタイル型ターミナルエミュレータ](https://ubuntuapps.blog.fc2.com/blog-entry-973.html)

## Visual Studio Code のインストール
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

分散型バージョン管理システムの Git をインストールします。wslgit なるものもあります。執筆時点での最新バージョンは2.16.2。飛ばしてよい。

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

## VSCode の統合ターミナルを WSL に設定する
VSCode では`Ctrl+@`で統合ターミナルが起動できる。初期設定では PowerShell になっているが、これを zsh に変更する。`Ctrl+,`でユーザー設定を開き、上部の検索バーで
```
terminal.integrated.shell.windows
```
と検索し、terminal.integrated.shell.windows の部分にカーソルを合わせると、ペンのマークが出てくるので[設定をコピー]をクリック。右のタブに移るので、
```
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\bash.exe,
"terminal.integrated.shellArgs.windows": ["-c", "zsh"],
```
というように書くと zsh が起動するようになる。上の行だけだと bash が起動する。

参考文献：
- [Run oh my zsh as integrated shell in VSCode on Windows](https://winsmarts.com/run-oh-my-zsh-as-integrated-shell-in-vscode-on-windows-7d69f72bafa3)

# ソフトウェアのインストール

### gcc
既にインストールされていた。バージョンは5.4.0。
```
sudo apt install build-essential
or
sudo apt install gcc
```

### gfortran
既にインストールされていた。バージョンは5.4.0。
```
sudo apt install gfortean
```

### Git
既にインストールされていた。バージョンは2.7.4。
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
既にインストールされていた。バージョンは 5.0 patchlevel 3。-x11 はインストールされていなかった。-x11 をつけないと terminal の設定がうまくいかないらしい。
```
sudo apt install gnuplot-x11
```

参考文献：
- [Windows Subsystem for Linuxをインストールしてみよう！](https://qiita.com/Aruneko/items/c79810b0b015bebf30bb)
- [Bash on Ubuntu on Windows上でTeXLiveのコンパイル&プレビュー環境を構築する（その1）](https://qiita.com/kuro_23/items/6bb7e3d470d04209bd03)
- [Cygwin絶対殺すマン ～物理のオタクがWindows Subsystem for Linuxで数値計算できるようになるまで～](https://qiita.com/PikkamanV/items/d308927c395d6e687a6a)

## VSCode の拡張機能
VSCode では左のタブの一番下の四角いマークをクリックすると拡張機能を追加できる。よく使うものを上げておく。使いたいものを検索してインストールし、再読み込みすると使えるようになる。あるいは、 VSCode 上で F1 を押して、
```
ext install runner
```
などとしてもよい。

C/C++の拡張
: C/C++

コードの整形
: Beautify

コードの自動実行
: Runner

コードの印刷
: PrintCode

LaTeX を使う
: LaTeX Workshop

Git を使う
: Git Lens

### C/C++
### Beautify
### Runner
VSCode はプロジェクトでファイルを実行するには launch.json という設定に書くしかなく、実質一つしか実行出来ない為、非常に面倒だ。そこで、Runner という拡張機能を使う。これはファイルタイプや拡張子に従って実行出来る。`Ctrl+,`でユーザー設定を開き、setting.json に以下を追加する。
```
"runner.languageMap": {
    "c": "C:/VSCodeRunner/crun.bat",
    "cpp": "C:/VSCodeRunner/cpprun.bat"
},
```
次に、C:/VSCodeRunnerというディレクトリを作成し、その中に以下のファイルを作成する。
```bash
// crun.bat
@echo off

if "%1" equ "" goto :eof

setlocal
set tempfile=%date:~4%%time::=%
set tempfile=%tempfile:/=%
set tempfile=%tempfile:.=%
gcc -lm -o %tempfile%.out "%1"
%tempfile%.out
del %tempfile%.out
endlocal
```
```bash
// cpprun.bat
@echo off

if "%~dpnx1" equ "" goto :eof

setlocal
set tempfile=%date:~4%%time::=%
set tempfile=%tempfile:/=%
set tempfile=%tempfile:.=%
set tempfile=%tempfile: =%
gcc -o %tempfile%.out "%~dpnx1" -std=c++1z
%tempfile%.out
del %tempfile%.out
endlocal
```
ここで、VSCode 上で `Ctrl+Shift+R`を押すと、実行できる。複雑な場合は Make を使ったほうがいいかもしれない。

参考文献：
- [Visual Studio Code の拡張を2本書いた。](https://mattn.kaoriya.net/software/vscode/20151122113659.htm)
- [Visual Studio CodeのRunnerを試してみた](http://nekko1119.hatenablog.com/entry/2015/11/26/040531)
- [Mac の Visual Studio Code で C/C++/C# を実行する](https://qiita.com/dockurage/items/49e7295e7ff7458e26cd)

### PrintCode
`Ctrl+Shift+P`でブラウザ経由で印刷できる。

参考文献：
- [VSCode用に表示中のコードを印刷できる拡張機能を作った](https://blog.bulkus.net/post/printcode/)

### LaTeX Workshop
LuaLaTeX でコンパイルするために setting.json に以下を追加する。LuaJITLaTeXを利用する場合は適宜JITを書き足せばよい。
```json
"latex-workshop.latex.toolchain": [
    {
        "command": "lualatex",
        "args": [
            "--cmdx",
            "--fmt=lualatex.fmt",
            "%DOCFILE%"
        ]
    }
]
```
これで .tex を保存すれば自動的にコンパイルされるようになる。`Ctrl+Alt+L`でLaTeX Workshop Actions が開く。主なアクションは

Build Latex Project
: latex-workshop.latex.toolchainにある一連のコマンドを実行。

View PDF file in new tab
: VScodeのnew tabでPDFを開く。`Ctrl+L+T`

SyncTeX from Cursor
: SyncTeXの機能を使用して、文字をPDFに合わす。

Clean up auxiliary files
: auxiliaryファイルを削除する。

Count words in LaTeX document
: 文字数をカウントしする。句読点、節の見出しなどはカウントされないらしい。

主な設定項目を挙げる。setting.json に追記すればよい。自分はファイルの自動削除を設定。
- 自動コンパイルのキャンセル
```json
"latex-workshop.latex.autoBuild.onSave.enabled": false,
```
- Auxiliaryファイルの自動削除
```json
"latex-workshop.latex.clean.enabled": true,
```
- ChkTeXによる構文チェック
```json
"latex-workshop.chktex.enabled": true,
```

参考文献：
- [VS CodeでTeXを使う方法](http://elecho.hatenablog.com/entry/2017/04/27/175500)
- [VScodeで快適LaTeX環境を構築する方法](https://qiita.com/ichigism/items/707e8a5def368a69e9a6)

### Git Lens

# Linux の操作

## コマンド
とりあえず超基本的なものだけ。

|コマンド|実行内容|
|pwd|現在のディレクトリを表示|
|ls|現在のディレクトリの中身を表示|
|mkdir|ディレクトリを作成|
|cd|ディレクトリに移動|
|cd ../|親ディレクトリに移動|
|mv|ファイルの移動|
|cp|ファイルのコピー|
|rm|ファイルの削除|
|echo|文字列の表示|
|cat|ファイルの中身を表示|
|diff|2つのファイルの差分を表示|
|tar|圧縮 tar -cvzf ooo.tar.gz directory<br>解凍 tar -xvzf ooo.tar.gz|

## リダイレクション
標準入力や標準出力を切り替えること。
- 標準入力の切り替え
```
コマンド < infile
```
- 標準出力の切り替え
```
コマンド > outfile
```
- 両方切り替え
```
コマンド < infile > outfile
```
標準出力で既存ファイルにリダイレクションすると、元のファイルは上書きされる。元のファイルに追記するには
```
コマンド >> outfile
```
とする。
- ヒアドキュメント<br>
複数の文字列を簡潔に記述する機能。コマンドに`<< EOF`が渡されると、「次の行から`EOF`が現れる行まで」が標準入力としてコマンドに渡される。例えば
```
cat << EOF infile > outfile
here
document
EOF
```
とすればヒアドキュメントの内容と infile の中身が結合されて outfile に書き出される。別に EOF 以外の文字列を用いても問題ない。
- 標準エラー<br>
コマンドの処理結果以外のエラーメッセージで標準出力とは独立しており、リダイレクションやパイプの対象外。

## パイプ
コマンドの処理結果を他のコマンドに引き渡すこと。
```
command1 | cammand2
```
のようにして使う。例えば、
```
ps aux | grep apache | sort -r | tee apache.txt
```
とすると現在実行中のプロセスのうち、apache に関するものを抽出して、逆順ソートし、テキストに保存できる。

参考文献：
- [bash: 標準出力、標準エラー出力をファイル、画面それぞれに出力する方法](https://qiita.com/laikuaut/items/e1cc312ffc7ec2c872fc)
- [覚えてると案外便利なBashのリダイレクト・パイプの使い方9個](https://orebibou.com/2016/02/%E8%A6%9A%E3%81%88%E3%81%A6%E3%82%8B%E3%81%A8%E6%A1%88%E5%A4%96%E4%BE%BF%E5%88%A9%E3%81%AAbash%E3%81%AE%E3%83%AA%E3%83%80%E3%82%A4%E3%83%AC%E3%82%AF%E3%83%88%E3%83%BB%E3%83%91%E3%82%A4%E3%83%97/#2)
- [bashのヒアドキュメントを活用する](https://qiita.com/take4s5i/items/e207cee4fb04385a9952)

# C言語と数値計算

## テスト
とりあえずいつものやつ。VSCode の統合ターミナルでやってみる。
```bash
cd /mnt/c
mkdir -p git/hello
cd git/hello
cat <<EOF > hello.c
#include <stdio.h>
int main (void) {
    printf("Hello World!\n");
}
EOF
gcc hello.c -o hello.out;
ls
./hello.out
```
ターミナルに`Hello World!`と表示されればOK。

## Euler 法

## Symplectic Euler 法

## Runge Kutta 法

## Newton 法

## Monte Carlo 法

# Gnuplot で描画

# TeX で文書作成

## tex とその歴史
ナントカTeX のまとめ的な何か。

### TeX
1978年、Donald Knuth によって最初の TeX が作られた。TeX は tex 言語から DVI ファイルを生成できる。

### pTeX
pTeX は TeX に縦書き組版、日本語禁則処理、日本語と欧文が混在した時の処理、など日本語向けの機能を追加した処理系である。理系の文書ではよくある日本語と欧文の混在も pTeX からサポートされている。

### eTeX
同時期に、レジスタ上限の増加、新たな条件式の追加、などの拡張が TeX に加えられて eTeX ができた。

### e-pTeX
こうしてできた2つの処理系 pTeX と eTeX を合体させることにより e-pTeX ができた。現在の日本語組版ではこれが主に用いられている。

### upTeX, e-upTeX
pTeX で扱えるようになった日本語はあくまでJIS第二水準まででしかなかった。pTeX をさらに改造して内部文字コードをUnicodeにしたのが upTeX。これを eTeX と合体させたのが e-upTeX。

###  XeTeX
XeTeX は eTeX を改造したものの一つで、Unicode への対応とフォントに関する拡張が行われている。XeTeX では DVI を拡張した XDV なる形式を経由して PDF を生成する。

### pdfTeX
これも eTeX を改造したものの一つで、 DVI を経由せずに直接 PDF を生成する。欧米ではこれが主に用いられている。

### LuaTeX
LuaTeX はpdfTeXの派生で、名前にある通り Lua のコードを TeX ファイルに入れることが出来る。pdfTeX と、内部エンコーディングを Unicode にした Omega という処理系の血統である。pdfTeX の後継になることがもう決定していて、将来的にはこれが普及する予定である。

参考文献：
- [TeX処理系御伽話](https://qiita.com/yyu/items/6404656f822ce14db935)
- [日本人の知らないTeX - TeXの過去・現在・未来](https://oku.edu.mie-u.ac.jp/texconf10/presentations/yato.pdf)

## LuaLaTeX-ja
$$ Lua \TeX -ja パッケージの冒頭から引用 $$
> Lua$$ \TeX $$-ja パッケージは，次世代標準 $$ \TeX $$ である Lua$$ \TeX $$ の上で，p$$ \TeX $$ と同等／それ以上の品質の日本語組版を実現させようとするマクロパッケージである．従来，「$$ \TeX $$ を用いて日本語組版を行う」といったとき，エンジンとしては ASCII p$$ \TeX $$ やそれの拡張物が用いられることが一般的であった．p$$ \TeX $$ は $$ \TeX $$ のエンジン拡張であり，（少々仕様上不便な点
はあるものの）商業印刷の分野にも用いられるほどの高品質な日本語組版を可能としている．だが，それは弱点にもなってしまった．p$$ \TeX $$ という（組版的に）満足なものがあったため，海外で行われている数々の $$ \TeX $$ の拡張――例えば 𝜀-$$ \TeX $$ や pdf$$ \TeX $$――や，TrueType, OpenType, Unicode といった計算機で日本語を扱う際の状況の変化に追従することを怠ってしまったのだ．ここ数年，若干状況は改善されてきた．現在手に入る大半の p$$ \TeX $$ バイナリでは外部 UTF-8 入力が利用可能となり，さらに Unicode 化を推進し，p$$ \TeX $$ の内部処理まで Unicode 化した up$$ \TeX $$ も開発されている．また，p$$ \TeX $$ に 𝜀-$$ \TeX $$ 拡張をマージした 𝜀-p$$ \TeX $$ も登場し，$$ \TeX $$ Live 2011 では pLa$$ \TeX $$が 𝜀-p$$ \TeX $$ の上で動作するようになった．だが，pdf$$ \TeX $$ 拡張（PDF 直接出力や micro-typesetting）をp$$ \TeX $$ に対応させようという動きはなく，海外との gap は未だにあるのが現状である．しかし，Lua$$ \TeX $$ の登場で，状況は大きく変わることになった．Lua コードで “callback” を書くことにより，Lua$$ \TeX $$ の内部処理に割り込みをかけることが可能となった．これは，エンジン拡張という真似をしなくても，Lua コードとそれに関する $$ \TeX $$ マクロを書けば，エンジン拡張とほぼ同程度のことができるようになったということを意味する．Lua$$ \TeX $$-ja は，このアプローチによって Lua コード・$$ \TeX $$ マクロによって日本語組版を Lua$$ \TeX $$ の上で実現させようという目的で開発が始まったパッケージである．

参考文献：
- [LuaTEX-ja パッケージ](http://mirrors.ibiblio.org/CTAN/macros/luatex/generic/luatexja/doc/luatexja-ja.pdf)
- [LuaLATEX-ja 用 jsclasses 互換クラス](http://mirrors.ibiblio.org/CTAN/macros/luatex/generic/luatexja/doc/ltjsclasses.pdf)
- [LuaTeX-ja の使い方](https://ja.osdn.net/projects/luatex-ja/wiki/LuaTeX-ja%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9)

## テンプレート
```tex
\documentclass[10pt,a4paper,papersize,onecolumn]{ltjsarticle}
```

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

### ドキュメントクラス
LaTeX の文書では最初にドキュメントクラスを指定する。LuaLaTeX-ja の場合は ltjsarticle を使う。論文の場合は ltjsbook とする。長いレポートの場合はオプション [report] を付ければ ltjsreport 相当になる。

オプション
|10pt      | 本文の欧文文字サイズを10ポイントにする(デフォルト)| 
|11pt      | 本文の欧文文字サイズを11ポイントにする           |
|12pt      | 本文の欧文文字サイズを12ポイントにする           |
|a4paper   | 297mm × 210mm (デフォルト)                     |
|a5paper   | 210mm × 148mm                                 |
|b4paper   | 364mm × 257mm                                 |
|b5paper   | 257mm × 182mm                                 |
|papersize | jsシリーズのみのオプション。用紙サイズオプションに従ってPDFのサイズを設定する。|
|onecolumn | 一段組にする。(デフォルト)                     |
|twocolumn | 二段組にする。                                |

## よく使う命令

## 論文執筆の注意点
