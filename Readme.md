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
- [お前らのWSLはそれじゃダメだ](http://xztaityozx.hatenablog.com/entry/2017/12/01/001544)

## Visial Studio Code のインストール

エディタには Visual Studio Code を使います。執筆時点での最新バージョンは1.20.1です。

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

分散型バージョン管理システムの Git をインストールします。wslgit なるものもあります。執筆時点での最新バージョンは2.16.2です。

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
8. 環境変数の設定
- Use Git Bash only <br>
    環境変数を汚さない。Cygwin等を導入している場合はこれ。
- Use Git from the Windows Command Prompt <br>
    コマンドプロンプトやPowerShellからGitのコマンドだけが使用できるように。
- Use Git and included Unix tools from the Windows Command Prompt <br>
    gitだけでなく、他のlsやlessなどをコマンドプロンプトやPowerShellから使用可能になります。 

今回はデフォルトのまま進める。
9. SSLライブラリの選択。初期設定で問題なし。
10. 改行の取り扱いの設定
- Checkout Windows-style, commit Unix-style line endings <br>
    チェックアウト時に改行コードはCR LFに変更され、コミット時には全てLFに変換される。
- Checkout as-is, commit Unix-style line endings <br>
    チェックアウト時は改行コードについては何もしない、コミット時には全てLFに変換される。
- Checkout as-is, commit as-is <br>
    改行コードについては何もしない。

今回はデフォルトのまま進める。
11. Gitで使用するコンソールをCygwinで使用されているMinTTYを使用するか、Windowsの標準コンソールを使用するか選択。Windowsの標準コンソールを選択。
12. gitconfigについての設定
- Enable file system caching <br>
    git status時などでの応答速度を改善するfscacheキャッシュを有効にするかしないかオプション
- Enable Git Credential Manager <br>
    GitHub等にアクセスする際の二段階認証に対応するかのオプション
- Enable symbolic links <br>
    シンボリックリンクの使用/不使用のオプション 

とりあえずデフォルトのまま進める。
13. Install をクリックするとインストール開始。
14. Finish で完了。

参考文献: 
- [自分用 Git For Windowsのインストール手順](https://qiita.com/toshi-click/items/dcf3dd48fdc74c91b409)
- [こっそり始めるGit／GitHub超入門](http://www.atmarkit.co.jp/ait/series/3190/)

