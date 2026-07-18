# 用語集(Glossary)

この教科書全体で使用する用語の定義集です。
各章で用語が初出したときにここへ追記し、以後の章ではこの定義に統一します。

## 運用ルール

- 追記は**五十音順・アルファベット順のセクション内**に行う(セクションごと分類)
- 各エントリのフォーマット:

```markdown
### 用語名(英語表記 / 略語の場合はフルスペル)

- **定義**: 1〜3行の簡潔な定義
- **初出章**: `XX_ディレクトリ/YY_ファイル名.md`
- **関連一次情報源**: あれば記載(例: カーネルドキュメント、POSIX、man page)
- **関連用語**: あれば記載
```

- 同じ概念に複数の呼び名がある場合は、**どちらを本文の標準表記とするか**を
  ここで決めて明記する
- `network-guide` 側の用語集(`../../shared/glossary.md`)と概念が重複する場合
  (例: ネットワークネームスペース、netfilter)は、どちらの用語集を正とするか、
  および相互参照の方法をここに明記する

---

## 数字・記号

(まだ登録された用語はありません)

## A〜Z

### ABI(Application Binary Interface)

- **定義**: コンパイル済みのプログラムとシステムの間の、機械語レベルの取り決め。
  システムコールの文脈では「番号と引数をどのレジスタに載せ、どの命令で
  カーネルに渡すか」というアーキテクチャごとの規約を指す。POSIX が C 関数の
  インタフェースを定めるのに対し、ABI は呼び出しの機構を定める
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: `man 2 syscall`(アーキテクチャ別規約一覧)
- **関連用語**: システムコール、レジスタ

### CFS(Completely Fair Scheduler / 完全公平スケジューラ)

- **定義**: Linux 2.6.23(2007年)から 6.5 まで使われた fair クラスの
  スケジューリングアルゴリズム。「常に vruntime 最小のタスクを選ぶ」ことで
  重み比の公平分配を実現した。応答性(レイテンシ要求)を原理の中で表現する
  手段を持たず、一律のヒューリスティクスに頼った点が EEVDF への置き換え
  (Linux 6.6)の動機。本書では歴史的経緯・対比の文脈で扱う
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/scheduler/sched-design-CFS.rst)
- **関連用語**: EEVDF、vruntime、スケジューラ、nice値

### dentry(directory entry / ディレクトリエントリ)

- **定義**: パス名の1成分、すなわち「この名前はこの inode を指す」という
  対応1件をメモリ上に持つ VFS のオブジェクト。集合体は dcache
  (dentry cache)と呼ばれ、パス解決のディスク I/O を省く。存在しない
  名前も「負の dentry」としてキャッシュされ、ENOENT の応答を高速化する
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/vfs.rst)
- **関連用語**: VFS、inode、パス解決

### EEVDF(Earliest Eligible Virtual Deadline First)

- **定義**: Linux 6.6 以降の fair クラスの標準スケジューリングアルゴリズム。
  「資格のある(lag ≥ 0 = もらいすぎていない)タスクの中で、仮想締切が
  最も早い者を選ぶ」。受け取る総量の公平は lag と資格が守り、応答性は
  スライス由来の締切の早さとして表現される。原典は Stoica / Abdel-Wahab に
  よる1995年の論文
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/scheduler/sched-eevdf.rst)、
  Stoica & Abdel-Wahab (1995)
- **関連用語**: CFS、vruntime、nice値、ランキュー、タイムスライス

### errno

- **定義**: 直近のシステムコール等の失敗理由を示すエラー番号が入る、POSIX が
  規定する変数。カーネルは失敗を負のエラー番号で返し、libc のラッパーが
  それを errno への格納と戻り値 -1 に翻訳する。番号と意味の対応
  (2 = ENOENT など)は `man 3 errno` にまとまっている
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: `man 3 errno`、POSIX(IEEE Std 1003.1)
- **関連用語**: システムコール、終了ステータス

### exec(execve)

- **定義**: 呼び出したプロセスの中身(アドレス空間)を、指定した実行ファイルの
  プログラムに入れ替えるシステムコール。PID・fdテーブル・カレントディレクトリは
  exec をまたいで生き残る(CLOEXEC 印付きの fd を除く)。本文の標準表記は
  総称として「exec」を用い、システムコール名を指すときは「execve」と書く
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
  (依頼の3点セットとしての先出しは `01_intro/02_shell_and_commands.md`)
- **関連一次情報源**: `man 2 execve`、POSIX(IEEE Std 1003.1)
- **関連用語**: fork、wait、プロセス

### ext4(fourth extended filesystem)

- **定義**: Ubuntu をはじめ多くのディストリビューションが標準採用する
  Linux のジャーナリングファイルシステム。ジャーナリングは独立した
  jbd2 層が担い、物理ロギング(更新後ブロックの丸ごと複製)方式をとる。
  データの扱いは `data=` マウントオプションで journal / ordered(既定)/
  writeback から選べる
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
  (名前の先出しは `03_filesystem_storage/01_vfs_basics.md`)
- **関連一次情報源**: `man 5 ext4`、カーネルドキュメント
  (Documentation/admin-guide/ext4.rst、Documentation/filesystems/ext4/)
- **関連用語**: jbd2、ジャーナリング、XFS、ファイルシステム

### FHS(Filesystem Hierarchy Standard / ファイルシステム階層標準)

- **定義**: `/etc` `/var` `/usr` など主要ディレクトリの名前・役割・配置原理
  (静的/可変、共有可能/固有)を定めた標準。現行版はLinux Foundationが公開する
  FHS 3.0(2015年)。主要ディストリビューションはこれに概ね従う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: FHS 3.0(Linux Foundation)、`man 7 hier`
- **関連用語**: ルートディレクトリ、マウント

### FIFO(named pipe / 名前付きパイプ)

- **定義**: パイプと同じカーネル内バッファの通路を、ファイルシステム上の
  名前(特殊ファイル)として置いたもの(`mkfifo` で作成)。fd を fork で
  引き継げる血縁関係になくても、名前を待ち合わせ場所にして通信できる
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 fifo`、POSIX(IEEE Std 1003.1)
- **関連用語**: パイプ、IPC

### fork

- **定義**: 呼び出したプロセスの複製(子プロセス)を作るシステムコール。
  アドレス空間はコピーオンライトで複製され、fdテーブル・カレントディレクトリ・
  umask 等も子に複製される。PID は新規に振られる。Linux の glibc では
  内部的に `clone` システムコールとして実装されている
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
  (依頼の3点セットとしての先出しは `01_intro/02_shell_and_commands.md`)
- **関連一次情報源**: `man 2 fork`、`man 2 clone`、POSIX(IEEE Std 1003.1)
- **関連用語**: exec、wait、コピーオンライト、プロセス

### fsck(file system check)

- **定義**: ファイルシステムの全台帳(inode、ビットマップ、ディレクトリ)を
  突き合わせて矛盾を検出・修復するツール群の総称(ext4 系の実体は e2fsck)。
  所要時間はディスクの中身の量に比例する。ジャーナリングの普及により
  起動時の常用からは退いたが、ハードウェア故障やバグ由来の「ジャーナルの
  モデル外」の破損に対する最後の手段として存続している
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連一次情報源**: `man 8 fsck`、`man 8 e2fsck`
- **関連用語**: ジャーナリング、ファイルシステム、スーパーブロック

### futex(Fast Userspace muTEX)

- **定義**: 競合がない限りシステムコールなしのユーザー空間内の操作だけで
  済む同期プリミティブ。競合したときだけカーネルに入り待ち行列で眠る。
  pthread の mutex 等、ユーザー空間の同期機構の土台。本書では位置づけの
  理解にとどめ、深掘りはしない
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 2 futex`
- **関連用語**: セマフォ、スレッド、システムコール

### initramfs(initial RAM filesystem)

- **定義**: カーネルが本物のルートファイルシステムをマウントするまでの間だけ
  使われる、メモリ上の小さな仮のユーザーランド。ブートローダがカーネルと一緒に
  読み込み、ディスクドライバの読み込み・RAIDの組み立て・暗号化解除など
  「ルートをマウントするための準備」を担う。本物のルートに切り替わると破棄される
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 7 boot`、カーネルドキュメント(Documentation/filesystems/ramfs-rootfs-initramfs.rst)
- **関連用語**: ブートローダ、カーネル、マウント

### inode(アイノード)

- **定義**: ファイル1つにつき1個割り当てられる管理台帳。パーミッション・
  所有者(UID/GID)・サイズ・更新日時などの属性を記録する。ファイル名は
  inodeには含まれず、ディレクトリ側の「名前→inode番号」対応表が持つ。
  詳細な構造は `03_filesystem_storage/03_inode_block_management.md` で扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: `man 7 inode`
- **関連用語**: パーミッション、UID / GID

### IPC(Inter-Process Communication / プロセス間通信)

- **定義**: 隔離されたプロセスどうしがデータをやり取りする仕組みの総称。
  バイトストリーム(パイプ / FIFO / UNIXドメインソケット)、メッセージ
  (メッセージキュー)、共有メモリ、同期(セマフォ / futex)の4系統に
  分類できる。どの手段もカーネル経由で実現される(共有メモリは設定時のみ
  カーネルが関与し、以後の読み書きには介在しない)
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 pipe`、`man 7 shm_overview`、`man 7 unix`、
  POSIX(IEEE Std 1003.1)
- **関連用語**: パイプ、共有メモリ、UNIXドメインソケット、シグナル

### jbd2(Journaling Block Device 2)

- **定義**: ext4 のジャーナリングを担う、カーネル内の独立した層
  (fs/jbd2/)。システムコール1回分の原子性の単位(handle)を1つの
  トランザクションに相乗りさせ、既定5秒ごと・fsync 時・領域不足時に
  コミットする。日誌の実体は通常 inode 8 の隠しファイル(循環ログ)
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/filesystems/journalling.rst)
- **関連用語**: ext4、ジャーナリング、トランザクション、チェックポイント

### mmap

- **定義**: プロセスのアドレス空間に新しい領域(ファイルの割り付け、または
  アノニマスメモリ)を追加するシステムコール。実体は VMA を1個追加するだけで、
  物理フレームの割り当てはページフォールトまで遅延される。malloc の大きな
  割り当てや共有ライブラリの読み込みの実装にも使われる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: `man 2 mmap`、POSIX(IEEE Std 1003.1)
- **関連用語**: VMA、デマンドページング、ページフォールト

### MMU(Memory Management Unit / メモリ管理ユニット)

- **定義**: 仮想アドレスから物理アドレスへの翻訳(ページテーブルの表引き)を、
  すべてのメモリアクセスに対して自動で行う CPU 内のハードウェア。カーネルの
  仕事は表を書いておくことまでで、引くのは MMU。翻訳結果は TLB に
  キャッシュされる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連用語**: ページテーブル、TLB、仮想メモリ

### nice値(nice value)

- **定義**: fair クラスのタスクの重み(weight)を決めるユーザー向けの値。
  範囲は -20〜+19、既定 0 で、**小さいほど優遇**される(「他人に nice な
  ほど値が大きい」が名前の由来)。1段の差で重みが約 1.25 倍変わり、
  nice 0 が基準の weight 1024 にあたる。受け取る CPU 時間の総量は長期的に
  この重みに比例する
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: `man 2 nice`、`man 7 sched`、POSIX(IEEE Std 1003.1)
- **関連用語**: スケジューラ、EEVDF、vruntime

### OOM killer(Out Of Memory killer)

- **定義**: 物理メモリとスワップが尽き、回収も不能な最終局面で、カーネルが
  点数(`/proc/<PID>/oom_score`)最大のプロセスを強制終了してメモリを
  没収する仕組み。オーバーコミット(裏付けを超える予約を許す方針)の
  帰結としての後始末にあたる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: `man 5 proc`
- **関連用語**: オーバーコミット、スワップ、仮想メモリ

### PATH(環境変数PATH)

- **定義**: コマンド名だけが入力されたとき、シェルが実行ファイルを探す
  ディレクトリの探索リスト。コロン区切りで列挙され、先頭から順に探索して
  最初に見つかったものが実行される
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: 環境変数、シェル、外部コマンド

### PID(Process ID / プロセスID)

- **定義**: カーネルがプロセスを識別する番号。ユーザーから見える「プロセスID」の
  カーネル内部での実体はスレッドグループの番号(tgid)で、タスク(スレッド)
  固有の番号はスレッドID(TID)としてユーザーに見える。全プロセスは PID 1 を
  祖先とする1本のプロセスツリーをなす
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: `man 2 getpid`、POSIX(IEEE Std 1003.1)
- **関連用語**: プロセス、スレッド、task_struct、PID 1

### PID 1(init プロセス)

- **定義**: カーネルが起動の最終段階で立ち上げる、最初のユーザー空間プロセス
  (プロセス番号1番)。以後に生成されるすべてのプロセスの祖先で、システム稼働中は
  存在し続ける。伝統的な呼び名は「init」で、現代の主要ディストリビューションでは
  systemdが担う。本文の標準表記は「PID 1」とし、歴史的文脈では「init」も用いる
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 7 boot`、`man 1 init`
- **関連用語**: systemd、デーモン、カーネル

### OS(Operating System / オペレーティングシステム)

- **定義**: ハードウェア資源を複数のプログラムに配分する「資源管理」と、
  ハードウェアを扱いやすい概念(ファイル、プロセス等)に置き換える「抽象化」を
  担うソフトウェア。狭義にはカーネルを、広義にはカーネル+ユーザーランドを指す。
  本書で単に「OS」と書く場合は広義を指し、中核部分を指すときは「カーネル」と書く
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、ユーザーランド、ディストリビューション

### root(スーパーユーザー)

- **定義**: UID 0 を持つ特権ユーザー。伝統的なUNIXモデルではパーミッション検査を
  免除され、すべてのファイルにアクセスできる(現代のLinuxではこの権限は
  capabilityに分割されている。分野07で扱う)。本文の標準表記は「root」とし、
  「スーパーユーザー」「特権ユーザー」は同義として扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連用語**: UID / GID、パーミッション

### setuid / setgid / sticky ビット

- **定義**: rwx 9ビットの上位に置かれた3つの特殊ビット。setuidは「実行時に
  ファイル所有者の身分(実効UID)で動く」、setgidは実行ファイルでは同様に
  グループの身分、ディレクトリでは「新規ファイルが親のグループを継承」、
  stickyはディレクトリで「他人のファイルの削除を禁止」(例: `/tmp`)
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 1 chmod`、`man 7 inode`
- **関連用語**: パーミッション、root

### systemd

- **定義**: 現代の主要ディストリビューションがPID 1として採用する、init実装+
  サービス管理の仕組み一式。サービスや起動処理を「Unit」という単位で宣言的に
  管理し、依存関係を解決しながら並行起動する。詳細は分野06
  (`06_systemd_service_mgmt/`)で扱う
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 1 systemd`、`man 7 bootup`
- **関連用語**: PID 1、デーモン

### task_struct(PCB / プロセス制御ブロック)

- **定義**: Linuxカーネルがタスク(プロセス/スレッド)1つにつき1個持つ管理台帳の
  構造体(`include/linux/sched.h`)。pid・tgid・状態・親子関係を直接持ち、
  アドレス空間(mm)・fdテーブル(files)・身分(cred)等は独立した構造体への
  参照として持つ。OS一般の用語では PCB(Process Control Block)。本文の標準表記は
  Linuxの文脈では「task_struct」、OS一般論では「PCB」とする
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: Linuxカーネルソース(include/linux/sched.h)
- **関連用語**: プロセス、スレッド、PID

### TLB(Translation Lookaside Buffer / アドレス変換バッファ)

- **定義**: MMU が一度引いた翻訳結果(仮想ページ→物理フレーム)を溜める
  CPU 内の小さな高速キャッシュ。エントリ数は千数百程度の桁で、CR3 の
  差し替え(アドレス空間の交換)で原則無効になるため、コンテキストスイッチの
  間接費の主因となる。緩和策として、エントリにアドレス空間の札を付ける
  PCID がある
- **初出章**: `02_process_kernel/03_virtual_memory.md`
  (コストの文脈での先出しは `02_process_kernel/02_syscall_context_switch.md`)
- **関連用語**: MMU、ページテーブル、コンテキストスイッチ

### tmpfs

- **定義**: ファイルの実体をディスクではなくメモリ(ページキャッシュ)
  だけに持つファイルシステム。高速だが再起動で消える。`/run` や
  `/dev/shm`(POSIX 共有メモリの実体)に使われる。中身は物理メモリ
  (+スワップ)を消費する点に運用上の注意がある
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
  (POSIX 共有メモリの文脈での先出しは `02_process_kernel/05_signals_ipc.md`)
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/tmpfs.rst)
- **関連用語**: 疑似ファイルシステム、ページキャッシュ、共有メモリ

### UID / GID(User ID / Group ID)

- **定義**: カーネルがユーザー・グループを識別する番号。プロセスは身分証として
  UID/GIDを携え、ファイルのinodeには所有者UID・グループGIDが記録される。
  番号と名前の対応表はユーザーランドの `/etc/passwd`・`/etc/group` が持つ
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)
- **関連用語**: パーミッション、root、プロセス

### umask

- **定義**: 新規ファイル・ディレクトリ作成時に、プログラムが希望したモードから
  「削る」許可ビットを指定するプロセスごとの属性(実モード = 希望モード AND
  NOT umask)。シェルから子プロセスへ引き継がれる。与える許可ではなく
  削る許可の指定である点に注意
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: `man 2 umask`、POSIX(IEEE Std 1003.1)
- **関連用語**: パーミッション

### UNIXドメインソケット(UNIX domain socket)

- **定義**: 同一ホスト内のプロセス間通信に使うソケット。API はネットワーク
  ソケットと同一だが通信はカーネル内で完結し、待ち合わせにはファイル
  システム上のパス名を使う。fd そのものの受け渡し(SCM_RIGHTS)と、相手の
  身分のカーネル保証つき確認(SO_PEERCRED)ができる。内部機構の詳細は
  `04_linux_network_stack/01_socket_api.md` が主担当
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 unix`、POSIX(IEEE Std 1003.1)
- **関連用語**: IPC、ファイルディスクリプタ、パイプ

### vDSO(virtual Dynamic Shared Object)

- **定義**: カーネルが全プロセスのアドレス空間に自動で貼り付ける小さな共有
  ライブラリ。時刻取得(clock_gettime 等)のような高頻度かつ特権不要の
  システムコールを、カーネル空間への移行なしにユーザー空間内の関数呼び出し
  だけで済ませるための仕組み。`ldd` の出力に `linux-vdso.so.1` として現れる
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: `man 7 vdso`
- **関連用語**: システムコール、共有ライブラリ、アドレス空間

### VFS(Virtual File System / 仮想ファイルシステム)

- **定義**: ファイル系システムコールと個々のファイルシステム実装の間に
  立つカーネル内の抽象化層。共通のオブジェクトモデル(superblock /
  inode / dentry / file)と関数ポインタの操作表(契約)を定め、
  アプリケーションが実装の違いを知らずに済むようにする。
  「すべてはファイル」の実現機構
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/vfs.rst)
- **関連用語**: inode、dentry、スーパーブロック、オープンファイル記述

### vruntime(仮想ランタイム / virtual runtime)

- **定義**: タスクが受け取った CPU 時間を重みで換算した会計値
  (進み = 実行時間 × 1024/weight)。重いタスクほどゆっくり進むため、
  全タスクのこの値を揃えるように走らせると実時間の分配が重み比になる。
  CFS では選択の直接のキー、EEVDF では lag(貸し借りの残高)と仮想締切を
  算出する土台として使われる
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: カーネルソース(kernel/sched/fair.c)、`/proc/<PID>/sched`
- **関連用語**: CFS、EEVDF、nice値、スケジューラ

### VMA(vm_area_struct / 仮想メモリ領域)

- **定義**: 連続する仮想アドレス範囲1つの「予約」を表すカーネル内の構造体。
  範囲・許可(r/w/x)・中身の出どころ(ファイルバック/アノニマス)を記録する。
  mm_struct がメイプルツリー(Linux 6.1 以降。6.0 以前は赤黒木)で保持し、
  `/proc/<PID>/maps` で一覧できる。ページフォールトの正当性判定
  (SIGSEGV か否か)の根拠となる台帳
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: Linuxカーネルソース(include/linux/mm_types.h)、`man 5 proc`
- **関連用語**: アドレス空間、mmap、ページフォールト

### wait

- **定義**: 子プロセスの終了を待ち、終了ステータスを回収するシステムコール群
  (wait / waitpid / waitid 等の総称)。親が wait で回収するまで、終了済みの子は
  ゾンビとして task_struct だけが残る。本文の標準表記は総称として「wait」とする
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
  (依頼の3点セットとしての先出しは `01_intro/02_shell_and_commands.md`)
- **関連一次情報源**: `man 2 wait`、POSIX(IEEE Std 1003.1)
- **関連用語**: fork、終了ステータス、ゾンビプロセス

### XFS

- **定義**: 大容量ストレージとメタデータ操作の並列性に強い Linux の
  ジャーナリングファイルシステム(RHEL 系の標準)。日誌は常にメタデータ
  のみで、変更内容を細粒度で記録する論理ロギングと、メモリ上(CIL)で
  変更をまとめてから書く遅延ロギングを用いる。未書き込み(unwritten)
  エクステントにより古いデータの露出を防ぐ
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
  (名前の先出しは `03_filesystem_storage/01_vfs_basics.md`)
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/xfs.rst)
- **関連用語**: ext4、ジャーナリング、ファイルシステム

## あ行

### アドレス空間(address space)

- **定義**: プロセスごとにカーネルが与える専用の「メモリの眺め」。プログラムの
  コード・データ・スタックはここに載り、他のプロセスのメモリは見えも触れも
  できない(隔離)。実現機構である仮想メモリの詳細は
  `02_process_kernel/03_virtual_memory.md` で扱う
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連用語**: プロセス、コピーオンライト

### 依存関係(dependency)

- **定義**: あるパッケージが動作するために別のパッケージを必要とする関係。
  パッケージのメタデータにバージョン制約付きで宣言され、apt等の上層が
  グラフ探索によって解決する。主な発生源は共有ライブラリの必要性
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: Debian Policy Manual
- **関連用語**: パッケージ、共有ライブラリ、リポジトリ

### オーバーコミット(overcommit)

- **定義**: 物理メモリ+スワップの合計を超えるメモリの予約(VMA)を許す、
  Linux 既定の方針。実際にはほとんどのプログラムが予約を使い切らないため、
  予約時に厳格に断るとメモリが大幅に無駄になることが根拠。予約が本当に
  使われて破綻したときの後始末が OOM killer。方針は
  `/proc/sys/vm/overcommit_memory` で変更できる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/mm/overcommit-accounting.rst)
- **関連用語**: OOM killer、デマンドページング、VMA

### オープンファイル記述(open file description / struct file)

- **定義**: 「あるファイルを open した1回分」を表す層。読み書き位置・
  アクセスモード・参照カウントを持つ。fd はこれへの参照(番号)にすぎず、
  fork や dup では fd テーブル側だけが複製されるため、読み書き位置は
  複数の fd・親子プロセス間で共有されうる。本文の標準表記は Linux の
  文脈では「struct file」、POSIX 一般論では「オープンファイル記述」とする
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 2 open`(NOTES)
- **関連用語**: ファイルディスクリプタ、inode、VFS

### 親プロセス / 子プロセス(parent / child process)

- **定義**: fork で複製した側が親、複製された側が子。task_struct は双方向の
  参照(real_parent / children)で家系図を記録する。親は wait で子の終了
  ステータスを回収する責務を持ち、親を失った子(孤児)は PID 1(または
  subreaper)に養子縁組される
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: `man 2 fork`、`man 2 wait`、`man 2 prctl`(PR_SET_CHILD_SUBREAPER)
- **関連用語**: fork、wait、ゾンビプロセス、PID 1

## か行

### カーネル(kernel)

- **定義**: OSの中核として資源管理と抽象化を実際に行うプログラム。CPUの
  最高特権レベル(カーネルモード)で動作する。「Linux」は厳密にはカーネルの名前
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連一次情報源**: Linuxカーネルドキュメント(Documentation/ 以下)
- **関連用語**: OS、カーネル空間、システムコール

### カーネル空間 / ユーザー空間(kernel space / user space)

- **定義**: CPUの特権レベルによって隔てられた2つの実行環境。カーネル空間では
  カーネルが全資源にアクセスできる特権モードで動き、ユーザー空間では一般の
  プログラムが制限されたモードで動く。境界を越える正規の窓口がシステムコール
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、システムコール、ユーザーランド

### カーネルスタック(kernel stack)

- **定義**: タスク1つにつき1本、ユーザー空間のスタックとは別にカーネル内に
  用意される専用のスタック(x86-64 で 16 KiB)。システムコールや割り込みで
  カーネルに入ると必ずここへ足場を切り替える(ユーザーが操作できるスタックを
  カーネルは信用できないため)。カーネル入り時点のユーザー空間のレジスタ一式は
  この上の pt_regs 構造体に退避される
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/arch/x86/kernel-stacks.rst)
- **関連用語**: コンテキストスイッチ、システムコール、task_struct

### カーネルパニック(kernel panic)

- **定義**: カーネル自身が続行不能な異常を検出して停止する事態。ユーザー空間の
  プログラム単体のクラッシュとは異なり、マシン全体が停止する
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル

### 外部コマンド(external command)

- **定義**: シェルとは別の実行ファイルとして存在し、fork/execにより新しい
  プロセスとして起動されるコマンド(例: `ls`、`cat`)。シェル自身が処理する
  ビルトインコマンドと対をなす
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: ビルトインコマンド、PATH、シェル

### 仮想メモリ(virtual memory)

- **定義**: プロセスごとに物理メモリと無関係な**仮想アドレス**の空間を与え、
  アクセスのたびにページテーブルで実際の DRAM 上の番地(**物理アドレス**)へ
  翻訳する仕組み。プロセス間の隔離、配置の自由(物理の断片化の吸収)、
  遅延と節約の芸(デマンドページング、CoW、スワップ)のすべての土台。
  アドレス空間という「眺め」を実現している機構がこれにあたる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/mm/concepts.rst)
- **関連用語**: アドレス空間、ページテーブル、MMU、ページフォールト

### 環境変数(environment variable)

- **定義**: プロセスに付随し、子プロセスへ引き継がれる「名前=値」形式の
  設定情報(例: `PATH`、`HOME`)。引き継ぎの仕組みは
  `02_process_kernel/01_process_thread_basics.md` で扱う
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: PATH、シェル

### 疑似ファイルシステム(pseudo filesystem)

- **定義**: 裏にディスク等のデバイスを持たず、VFS の契約(操作の表)だけを
  満たすファイルシステムの総称(/proc、/sys、tmpfs 等)。read されるたびに
  カーネルが中身をその場で生成するものが多く、stat のサイズが 0 でも
  読める。`/proc/filesystems` で nodev と表示される
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: `man 5 proc`、カーネルドキュメント(Documentation/filesystems/)
- **関連用語**: VFS、tmpfs、マウント

### クライアント(client)

- **定義**: サーバーに対してサービスを依頼する側のコンピュータまたはプログラム
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: サーバー

### グロブ(glob / パス名展開)

- **定義**: `*.txt` のようなワイルドカードのパターンを、一致する実在の
  ファイル名の並びに置き換えるシェルの展開機能。展開はコマンド起動前に
  シェルが行い、コマンドは展開後の引数を受け取る。本文の標準表記は「グロブ」
  とし、「ワイルドカード展開」「パス名展開」は同義として扱う
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)Shell Command Language
- **関連用語**: シェル

### クラッシュ整合性(crash consistency)

- **定義**: 電源断やクラッシュがどの瞬間に起きても、ファイルシステムの
  台帳どうし(inode、ビットマップ、ディレクトリ等)が矛盾しない状態を
  保てるという性質、およびそれをどう保証するかという問題。1つの操作が
  複数ブロックの更新からなるのに、ディスクの原子的な書き込み単位は
  1ブロック程度でしかないことから生じる。解法がジャーナリング(WAL)や
  コピーオンライト方式(btrfs / ZFS)
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連用語**: ジャーナリング、fsck、トランザクション

### 共有ライブラリ(shared library)

- **定義**: 多数のプログラムが共通で使う機能(例: glibcのlibc.so.6)を1つの
  ファイル(.so)にまとめ、実行のたびに動的リンカ(ld.so)が結合して使う仕組み
  (動的リンク)。ディスク・メモリの節約と欠陥修正の一斉適用を可能にする一方、
  パッケージ間の依存関係の主因となる
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 8 ld.so`
- **関連用語**: 依存関係、パッケージ

### 共有メモリ(shared memory)

- **定義**: 複数のプロセスのページテーブルに同じ物理ページフレームを
  指す PTE を書き込み、メモリそのものを共有する IPC。データのコピーが
  発生しない最速の手段だが、カーネルが読み書きに介在しないため同時
  アクセスの同期(セマフォ等)を自前で負う。System V 世代(shmget、
  `ipcs -m` で観察)と POSIX 世代(shm_open、実体は `/dev/shm` の tmpfs)
  の2世代がある
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 shm_overview`、`man 7 sysvipc`
- **関連用語**: IPC、ページテーブル、mmap、セマフォ

### コピーオンライト(copy-on-write / CoW)

- **定義**: 「コピーしたことにしておき、実際の複製は書き込まれた部分だけ・
  書き込まれた時に行う」遅延コピー戦略。fork のアドレス空間複製で使われ、
  親子のページテーブルを同じ物理メモリに書き込み禁止で向けておき、書き込み時の
  例外を契機にそのページだけ複製する。本文の標準表記は「コピーオンライト」
  とし、2回目以降は「CoW」も用いる
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: `man 2 fork`
- **関連用語**: fork、アドレス空間

### コンテキストスイッチ(context switch)

- **定義**: CPU を使うタスク(task_struct)を交代させること。実体は
  アドレス空間の切り替え(switch_mm、CR3 差し替え)と実行状態の切り替え
  (switch_to、レジスタとカーネルスタックの差し替え)で、常にカーネル内で
  起きる。タスクが待ちで眠る「自発的」と、プリエンプションによる「非自発的」に
  分かれる。同一プロセスのスレッド間ではアドレス空間の切り替えが省略される。
  ユーザー/カーネル空間の行き来(モード切替)とは別の概念
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: Linuxカーネルソース(kernel/sched/core.c の context_switch)
- **関連用語**: モード切替、プリエンプション、task_struct、カーネルスタック

## さ行

### サーバー(server)

- **定義**: 他のコンピュータやプログラムからの依頼を受けてサービスを提供する
  役割(またはその役割を担うマシン)。ハードウェアの種類ではなく役割の名前
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: クライアント、デーモン

### システムコール(system call)

- **定義**: ユーザー空間のプログラムがカーネルに処理を依頼するための正規の窓口。
  ファイル読み書き・プロセス作成などハードウェアや保護資源が絡む操作はすべて
  これを経由する。POSIX(IEEE Std 1003.1)が規定するOSインタフェースの中心
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、man セクション2
- **関連用語**: カーネル空間 / ユーザー空間

### シグナル(signal)

- **定義**: カーネルからプロセスへの非同期の通知。中身は番号1つで、
  メッセージ本文は運ばない。発生源はハードウェア例外の翻訳(SIGSEGV等)・
  端末(SIGINT等)・kill による明示送信・カーネル内の出来事(SIGCHLD等)。
  プロセスはシグナルごとに処分(disposition: デフォルト動作/無視/
  ハンドラ)を選べるが、SIGKILL と SIGSTOP だけは変更もブロックも不可。
  標準シグナル(1〜31)は保留中に合流し(同番号は1回にまとまる)、
  リアルタイムシグナル(SIGRTMIN〜SIGRTMAX)は待ち行列に積まれる
- **初出章**: `02_process_kernel/05_signals_ipc.md`
  (SIGSEGV・SIGCHLD としての先出しは `02_process_kernel/03` / `01`)
- **関連一次情報源**: `man 7 signal`、POSIX(IEEE Std 1003.1)Signal Concepts
- **関連用語**: シグナルハンドラ、シグナルマスク、割り込み、プロセス

### シグナルハンドラ(signal handler)

- **定義**: シグナル配送時に実行させる関数として sigaction で登録する
  もの。カーネルがユーザースタック上に組み立てる「偽装された関数呼び出し」
  として、プログラム本体のどの2命令の間にでも割り込んで走る。この非同期性
  のため、中から呼べるのは非同期シグナル安全な関数に限られる
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 2 sigaction`、POSIX(IEEE Std 1003.1)
- **関連用語**: シグナル、非同期シグナル安全、カーネルスタック

### シグナルマスク(signal mask / ブロック)

- **定義**: スレッドごとに持つ「配送を保留させるシグナルの集合」
  (`man 2 sigprocmask` で操作)。ブロック中に生成されたシグナルは保留集合に
  留まり、ブロック解除時に配送される。SIGKILL / SIGSTOP はブロックできない。
  fork では複製され、exec もまたいで引き継がれる
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 2 sigprocmask`、POSIX(IEEE Std 1003.1)
- **関連用語**: シグナル、スレッド

### シンボリックリンク(symbolic link / symlink)

- **定義**: 「別のパス名」を中身として持つ特殊ファイル。パス解決の途中で
  出会うと、カーネルは書かれたパスに読み替えて解決を続ける。アクセス可否は
  リンク先のパーミッションで判定され、リンク自身の `lrwxrwxrwx` 表示は
  判定に使われない。リンク先が存在しないものは「壊れたリンク」と呼ぶ
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 7 symlink`
- **関連用語**: inode、パーミッション

### シェル(shell)

- **定義**: 人間が入力したコマンド文字列を解釈し、プログラムの起動をカーネルに
  依頼する対話用プログラム(例: bash)。カーネル(核)を包む「殻」が語源。
  仕組みの詳細は `01_intro/02_shell_and_commands.md` で扱う
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、ユーザーランド

### ジャーナリング(journaling)

- **定義**: 複数ブロックにまたがる更新を行う前に、その内容を日誌
  (ジャーナル)というディスク上の専用領域へ先に書き、コミットレコードで
  完結の印を付けてから実物の台帳に反映する方式。データベース由来の
  ログ先行書き込み(WAL: Write-Ahead Logging)の応用。クラッシュ後は
  「印のない日誌は破棄、印のある日誌は再生(冪等)」により、ジャーナル
  サイズに比例する時間で一貫した状態へ復帰できる。守るのは構造の整合性で
  あって直前データの耐久性ではない(耐久性は fsync の領分)
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/filesystems/journalling.rst)
- **関連用語**: クラッシュ整合性、トランザクション、チェックポイント、jbd2

### 終了ステータス(exit status)

- **定義**: プロセスが終了時に親プロセスへ残す0〜255の数値。POSIXの慣例で
  0が成功、0以外が失敗を表す。シェルでは `$?` で参照でき、`&&`/`||` による
  制御やサービス管理の成否判定の基礎となる
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)
- **関連用語**: シェル、プロセス

### スーパーブロック(superblock)

- **定義**: マウントされたファイルシステム1つ分の台帳となる VFS の
  オブジェクト。ブロックサイズ、ルートディレクトリの inode、空き容量の
  勘定、ダーティな inode の一覧などを持つ。mount はデバイスから
  スーパーブロックを組み立て、その根をディレクトリツリーに接ぎ木する操作
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/vfs.rst)
- **関連用語**: VFS、マウント、inode

### スケジューラ(scheduler)

- **定義**: 走行可能なタスクの中から次に CPU を使う者を選ぶカーネルの
  仕組み。交代の機構(コンテキストスイッチ)に対する「方針」側にあたる。
  Linux ではスケジューリングクラスの階層をなし、通常のタスクは fair クラス
  (Linux 6.6 以降は EEVDF)が受け持つ
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: `man 7 sched`、カーネルドキュメント(Documentation/scheduler/)
- **関連用語**: スケジューリングクラス、ランキュー、コンテキストスイッチ、EEVDF

### スケジューリングクラス / ポリシー(scheduling class / policy)

- **定義**: Linux のスケジューラを構成する絶対優先の階層
  (stop > deadline > realtime > fair > idle)。上位クラスに走行可能な
  タスクがいる限り下位に順番は回らない。ユーザーにはポリシー
  (SCHED_FIFO / SCHED_RR / SCHED_OTHER / SCHED_BATCH / SCHED_IDLE /
  SCHED_DEADLINE)として見える。POSIX が規定するのは FIFO / RR / OTHER の
  3つで、残りは Linux の拡張
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: `man 7 sched`、POSIX(IEEE Std 1003.1)
- **関連用語**: スケジューラ、nice値

### スレッド(thread)

- **定義**: プロセス内部の実行の流れの単位。同一プロセスのスレッドどうしは
  アドレス空間・fdテーブル・身分を共有し、実行位置とスタックだけを各自で持つ。
  Linuxカーネル内部ではプロセスと区別されず、どちらも「タスク」(task_struct
  1個)であり、clone のフラグによる資源共有の度合いが違うだけ
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: `man 7 pthreads`、`man 2 clone`、POSIX(IEEE Std 1003.1)
- **関連用語**: プロセス、task_struct、PID

### スワップ(swap)

- **定義**: 物理メモリが不足したとき、最近使われていないアノニマスページ
  (ヒープ、スタック等)の中身をディスク上のスワップ領域へ退避して
  ページフレームを回収する仕組み。退避したページに触れると、ページフォールトを
  契機に読み戻される(メジャーフォールト)。恒常的な出入り(vmstat の si/so)は
  物理メモリ不足・スラッシングの徴候
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/mm/concepts.rst)
- **関連用語**: ページフォールト、仮想メモリ、OOM killer

### セマフォ(semaphore)

- **定義**: 「同時に入れる数」を数えるカウンター型の同期プリミティブ。
  獲得で減り、解放で増え、0 のときの獲得は待たされる。カーネルが読み書きに
  介在しない共有メモリの交通整理役として古典的に使われる。System V 世代
  (semget)と POSIX 世代(sem_open / sem_init)の2世代がある
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 sem_overview`、POSIX(IEEE Std 1003.1)
- **関連用語**: 共有メモリ、futex、IPC

### ゾンビプロセス(zombie process)

- **定義**: 終了済みだが、親プロセスがまだ wait で終了ステータスを回収して
  いない状態のプロセス(`ps` の STAT では Z、表示は `<defunct>`)。実行資源は
  すべて解放済みで、PID と終了ステータスを含む最小限の記録(task_struct)だけが
  残る。少数なら正常な一生の最終段階であり、大量に溜まる場合のみ親の欠陥を疑う
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: `man 2 wait`、`man 1 ps`(PROCESS STATE CODES)
- **関連用語**: wait、親プロセス / 子プロセス、PID

## た行

### タイムスライス / スライス(time slice)

- **定義**: 交代までにタスクが連続して走れる時間の持ち分。EEVDF では
  仮想締切の算出に使われるタスクごとの申告値(既定は base_slice)で、
  短いほど「早く・細かく」、長いほど「遅く・まとまって」走る。受け取る
  総量の公平とは独立に、順番(応答性)だけを左右する。本文の標準表記は
  EEVDF の文脈では「スライス」、一般論では「タイムスライス」とする
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/scheduler/sched-eevdf.rst)
- **関連用語**: EEVDF、スケジューラ、プリエンプション

### 端末(terminal / ターミナル)

- **定義**: 文字の入出力を担う「画面と鍵盤」側の装置または
  それを再現するソフトウェア(ターミナルエミュレータ)。文字列を解釈する
  シェルとは別物で、端末が受け取った入力をシェルに渡す関係にある
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: シェル

### ダーティ(dirty)

- **定義**: ページキャッシュ上の内容がディスク上の内容より新しい
  (書き込まれたがまだ書き戻されていない)状態を示す印。write は
  ページキャッシュに書いてダーティ印を付けるだけで即座に戻り、
  実際のディスク反映(ライトバック)は後から非同期に行われる。
  ダーティなデータは電源断で失われる——fsync が存在する理由
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
  (概念の先出しは `03_filesystem_storage/01_vfs_basics.md`)
- **関連用語**: ページキャッシュ、ライトバック、ジャーナリング

### チェックポイント(checkpoint)

- **定義**: ジャーナリングにおいて、日誌へのコミットが済んだ更新を、
  実物の台帳(ディスク上の本来の位置)へ書き戻す処理。これが済んだ
  トランザクションの日誌領域は回収され、循環ログとして使い回される。
  「日誌に書く→実物に反映(チェックポイント)→日誌を回収」が
  ジャーナリングの基本サイクル
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連用語**: ジャーナリング、トランザクション、jbd2

### ディストリビューション(distribution)

- **定義**: Linuxカーネルに、選定されたユーザーランドのプログラム群・管理ツールを
  組み合わせて「すぐ使えるOS一式」として配布したもの(例: Ubuntu、Debian、RHEL)。
  ディストリビューションのバージョンとカーネルのバージョンは別々に進む
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、ユーザーランド

### デーモン(daemon)

- **定義**: 人間と対話せず、背後で動き続けて依頼を待つプログラム(例: sshd、
  nginx)。サーバーのサービス提供は実質的にデーモンが担う
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: サーバー、プロセス

### デマンドページング(demand paging)

- **定義**: メモリの割り当て要求(malloc、mmap 等)には予約(VMA)の記録
  だけで応じ、物理フレームの割り当てを「最初にそのページに触れた瞬間の
  ページフォールト」まで遅延する方式。確保されたが使われない領域が
  物理メモリを消費しない仕組みで、VmSize(予約)と VmRSS(実際)の差として
  観察できる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/mm/concepts.rst)
- **関連用語**: ページフォールト、VMA、mmap、オーバーコミット

### トランザクション(transaction)

- **定義**: 「全部成功するか、全く行われないか」(原子性)を保証したい
  一連の更新のまとまり。ファイルシステムの文脈では、1つの操作が更新する
  複数ブロック(あるいは jbd2 のように相乗りした多数の操作)を指し、
  日誌上のコミットレコード1ブロックの書き込み成否がトランザクション
  全体の成立/不成立を決める
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連用語**: ジャーナリング、チェックポイント、クラッシュ整合性

### 特権レベル(privilege level / ring)

- **定義**: CPU が実行中のコードに与える権限の段階。x86-64 では ring 0〜3 の
  4段階があり、Linux は ring 0(カーネルモード。全命令・全メモリにアクセス可)と
  ring 3(ユーザーモード。特権命令とカーネルメモリへのアクセスを CPU が拒否)の
  2つだけを使う。カーネル空間/ユーザー空間の区別を物理的に強制する仕組み。
  ring 3 から ring 0 へはカーネルが事前登録した入口(システムコール・割り込み・
  例外)からしか入れない
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
  (簡略な言及は `01_intro/01_server_os_kernel_overview.md`)
- **関連用語**: カーネル空間 / ユーザー空間、システムコール、モード切替

## な行

(まだ登録された用語はありません)

## は行

### パーミッション(permission / アクセス権)

- **定義**: ファイルごとに「所有者・グループ・その他」の3種の相手に対する
  「読み(r)・書き(w)・実行(x)」の可否を記録した9ビットの許可情報。
  inodeに記録され、検査は最初に一致した1段だけを見て、openの瞬間に一度だけ
  行われる。本文の標準表記は「パーミッション」とし、「アクセス権」は
  同義として扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)File Access Permissions
- **関連用語**: inode、UID / GID、root、umask

### パッケージ(package)

- **定義**: ソフトウェア1本分のファイル一式(実行ファイル・ライブラリ・設定・
  文書)を、配置先パス・メタデータ(名前、バージョン、依存関係)・保守スクリプト
  とともに1ファイルに固めた配布単位。Debian/Ubuntu系は `.deb` 形式
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 5 deb`、Debian Policy Manual
- **関連用語**: パッケージマネージャ、依存関係、リポジトリ

### パッケージマネージャ(package manager)

- **定義**: パッケージの導入・更新・削除を台帳で管理するソフトウェア。
  Debian/Ubuntuではローカルの配置と台帳を担うdpkgと、リポジトリからの取得・
  依存解決を担うaptの2層構造。本文の標準表記は「パッケージマネージャ」とし、
  「パッケージ管理システム」は同義として扱う
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 1 dpkg`、`man 8 apt`
- **関連用語**: パッケージ、リポジトリ、依存関係

### パス解決(path resolution / path walk)

- **定義**: パス名の文字列から目的の inode へ、根(または起点の
  ディレクトリ)から1成分ずつ表引きでたどり着く VFS の処理。ほぼすべての
  ファイル系システムコールの前段で走る。途中で各ディレクトリの x 権限を
  検査し、シンボリックリンクの読み替えとマウントの縫い目の乗り換えを行う。
  dcache により大半はディスク I/O なしで完了する
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
  (原理の先出しは `01_intro/03_filesystem_hierarchy_permissions.md`)
- **関連一次情報源**: `man 7 path_resolution`、
  カーネルドキュメント(Documentation/filesystems/path-lookup.rst)
- **関連用語**: dentry、inode、マウント、シンボリックリンク

### パイプ(pipe)

- **定義**: あるプロセスの標準出力を別のプロセスの標準入力に直結する、
  カーネル内のバッファを介したプロセス間のデータ通路(シェルでは `|`)。
  両側のプロセスは同時に実行され、バッファの空き/満杯に応じた流量制御を
  カーネルが行う
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連一次情報源**: `man 7 pipe`、`man 2 pipe`
- **関連用語**: 標準入出力、ファイルディスクリプタ

### 非同期シグナル安全(async-signal-safe)

- **定義**: シグナルハンドラの中から呼んでも安全であることを POSIX が
  保証する関数の性質(該当する関数の一覧は `man 7 signal-safety`)。
  ハンドラは本体のどの2命令の間にでも割り込むため、内部状態を持つ関数
  (printf、malloc 等)は保証外で、再入すると壊れうる。ハンドラの定石
  「フラグを立てるだけにする」の根拠
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 signal-safety`、POSIX(IEEE Std 1003.1)
- **関連用語**: シグナルハンドラ、シグナル

### 標準入出力(standard input / output / error)

- **定義**: すべてのプロセスが起動時に持つ3本の入出力経路。標準入力
  (stdin、fd 0)、標準出力(stdout、fd 1)、標準エラー出力(stderr、fd 2)。
  結果(stdout)とエラー(stderr)を別の宛先へ振り分けられるよう出口は2本ある
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)
- **関連用語**: ファイルディスクリプタ、リダイレクト、パイプ

### ビルトインコマンド(builtin command)

- **定義**: 外部プログラムを起動せず、シェル自身が処理するコマンド
  (例: `cd`、`export`)。シェル自身の状態(カレントディレクトリ、環境変数等)を
  変更する操作は、子プロセスでは実現できないためビルトインである必要がある。
  本文の標準表記は「ビルトインコマンド」とし、「内部コマンド」「組み込み
  コマンド」は同義として扱う
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: 外部コマンド、シェル

### ファームウェア(firmware)

- **定義**: マザーボード上のROMに格納され、電源投入直後に最初に実行される
  プログラム。ハードウェアを初期化し、ディスク上のブートローダを起動する。
  現代のサーバーの標準はUEFI(Unified Extensible Firmware Interface)で、
  旧来のBIOSを置き換えた
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: UEFI Specification(UEFI Forum)、`man 7 boot`
- **関連用語**: ブートローダ

### ファイルシステム(filesystem)

- **定義**: ストレージ上にファイルの中身・名前の一覧・空き領域の記録を
  どう並べるかという「データの並べ方の流儀」、およびその実装(ext4、XFS、
  tmpfs 等)。VFS の契約(操作の表)を満たして登録され、アプリケーション
  からは違いが見えない。ディスクを持たないものは疑似ファイルシステムと呼ぶ
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
  (語としての先出しは `01_intro/03_filesystem_hierarchy_permissions.md`)
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/)
- **関連用語**: VFS、マウント、inode、疑似ファイルシステム

### ファイルディスクリプタ(file descriptor / fd)

- **定義**: プロセスが開いている入出力経路(ファイル、端末、パイプ等)を
  カーネルがプロセスごとの番号札で管理したもの。0〜2は標準入出力の予約席。
  execをまたいで引き継がれる
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)
- **関連用語**: 標準入出力、リダイレクト、パイプ

### プリエンプション(preemption / 横取り)

- **定義**: 走り続けたいタスクから、カーネルが強制的に CPU を取り上げること
  (非自発的なコンテキストスイッチ)。タイマー割り込みを契機にスケジューラが
  「走りすぎ」を検査して再スケジュール要求(TIF_NEED_RESCHED)の旗を立て、
  カーネルからユーザー空間へ戻る間際などに交代が実行される。公平性の要であり、
  1つの暴走プログラムがマシンを固めることを防ぐ
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連用語**: コンテキストスイッチ、割り込み、task_struct

### プロセス(process)

- **定義**: 実行中のプログラムと、それにカーネルが割り当てた資源一式
  (アドレス空間、fdテーブル、身分、カレントディレクトリ等)。カーネルから見た
  資源配分と隔離の基本単位で、1個以上のスレッドを収める容れ物でもある。
  ディスク上の実行ファイル(プログラム)とは区別する
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
  (言及のみの先出しは `01_intro/01_server_os_kernel_overview.md`)
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 5 proc`
- **関連用語**: スレッド、task_struct、PID、アドレス空間

### ブートローダ(boot loader)

- **定義**: ファームウェアから制御を受け取り、カーネル本体(vmlinuz)と
  initramfsをメモリに読み込んでカーネルに制御を渡すプログラム(例: GRUB)。
  起動するカーネル版の選択やカーネル引数の指定もここで行う。本文の標準表記は
  「ブートローダ」(長音なし)とする
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 7 boot`
- **関連用語**: ファームウェア、initramfs、カーネル

### ページ / ページフレーム(page / page frame)

- **定義**: 仮想メモリの翻訳の単位となる固定長の区画(x86-64 の基本サイズは
  4 KiB)。仮想アドレス側の区画を「ページ」、物理メモリ側の区画を
  「ページフレーム」と呼び分ける。アドレスの下位12ビットはページ内オフセット
  として翻訳されずに素通りする
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: `getconf PAGESIZE`、カーネルドキュメント(Documentation/admin-guide/mm/concepts.rst)
- **関連用語**: ページテーブル、仮想メモリ

### ページキャッシュ(page cache)

- **定義**: 一度ディスクから読んだファイルの中身をページ単位でメモリに残し、
  再利用とプロセス間の共有(同じ共有ライブラリの実体が1組で済む等)に
  使い回すカーネルのキャッシュ。空きメモリの有効活用であり、必要になれば
  即座に回収される(free の buff/cache 列)。詳細は分野03で扱う
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連用語**: ページフォールト、スワップ、共有ライブラリ

### ページテーブル(page table)

- **定義**: 仮想ページ番号→物理フレーム番号+属性ビット(Present、R/W、U/S、
  NX、Accessed、Dirty)の対応を持つ翻訳表。x86-64 の標準は9ビット×4段
  (PGD→PUD→PMD→PTE)の多段構成で、疎なアドレス空間の使用部分だけ表を
  持てるようにしている。根(第1段)の物理アドレスを CR3 レジスタが指し、
  CR3 の差し替えがアドレス空間の交換にあたる
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/arch/x86/x86_64/mm.rst)
- **関連用語**: MMU、TLB、仮想メモリ、コンテキストスイッチ

### ページフォールト(page fault)

- **定義**: MMU の表引きが失敗(Present=0、権限違反等)したときに CPU が
  上げる例外。大半は異常ではなく、カーネルが仕掛けた正常な仕組み
  (デマンドページング、CoW、スワップ読み戻し)の発動であり、VMA に照らして
  正当と認められないものだけが SIGSEGV(Segmentation fault)になる。
  ディスク I/O を伴うものをメジャーフォールト、メモリ操作だけで済むものを
  マイナーフォールトと分類する
- **初出章**: `02_process_kernel/03_virtual_memory.md`
- **関連一次情報源**: カーネルソース(mm/memory.c、arch/x86/mm/fault.c)
- **関連用語**: 例外、VMA、デマンドページング、コピーオンライト

## ま行

### マウント(mount)

- **定義**: ディスク等の装置や疑似ファイルシステム(/proc、/sys)の中身を、
  単一ディレクトリツリーのあるディレクトリに「接ぎ木」して見せる操作。
  木は一本のまま裏側の実体だけが切り替わる。内部機構(VFS)は
  `03_filesystem_storage/01_vfs_basics.md` で扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: `man 8 mount`
- **関連用語**: ルートディレクトリ、FHS

### メッセージキュー(message queue)

- **定義**: 境界のあるメッセージ単位でデータを受け渡す IPC。バイト
  ストリームと違い「1依頼=1メッセージ」の形が保存される。System V 世代
  (msgget、`ipcs -q` で観察)と POSIX 世代(mq_open、実体は `/dev/mqueue`。
  優先度つき)の2世代がある
- **初出章**: `02_process_kernel/05_signals_ipc.md`
- **関連一次情報源**: `man 7 mq_overview`、`man 7 sysvipc`、
  POSIX(IEEE Std 1003.1)
- **関連用語**: IPC、パイプ、UNIXドメインソケット

### モード切替(mode switch)

- **定義**: 同一タスクのまま、CPU の実行モードがユーザーモード(ring 3)と
  カーネルモード(ring 0)の間で切り替わること。システムコールの往復で
  必ず1回起きる。タスクそのものが交代するコンテキストスイッチとは別物で、
  コストも1〜2桁軽い
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連用語**: システムコール、特権レベル、コンテキストスイッチ

## や・ら・わ行

### ユーザーランド(userland)

- **定義**: カーネルの外側(ユーザー空間)で動くプログラム群の総称。シェル、
  コマンド、ライブラリ(glibc等)、デーモンなどを含む。カーネルと合わせて
  広義のOSを構成する
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、カーネル空間 / ユーザー空間、ディストリビューション

### ライトバック(writeback / 書き戻し)

- **定義**: ページキャッシュ上のダーティなページを、カーネルのスレッドが
  後から非同期にディスクへ書き戻す処理。write システムコールが即座に
  戻れるのはこの遅延のおかげで、同じページへの連打の集約・連続書き込みの
  まとめ書きという効率も得る。代償として書き戻し前の電源断でデータが
  失われるため、確定が必要な地点では fsync で強制する
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
  (概念の先出しは `03_filesystem_storage/01_vfs_basics.md`)
- **関連一次情報源**: `man 2 fsync`、カーネルドキュメント
  (Documentation/admin-guide/mm/concepts.rst)
- **関連用語**: ダーティ、ページキャッシュ、ジャーナリング

### ランキュー(run queue / rq)

- **定義**: CPU(論理コア)1つにつき1本用意される、走行可能なタスクの
  待合室(struct rq)。クラスごとのサブキュー(fair 用の cfs_rq 等)を束ね、
  fair クラスでは赤黒木で管理される。R(TASK_RUNNING)状態の実体は
  「いずれかのランキューに載っている(または走行中)」こと。CPU 間の
  偏りはロードバランシングで是正される
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: カーネルソース(kernel/sched/sched.h)
- **関連用語**: スケジューラ、task_struct、ロードバランシング

### ルートディレクトリ(root directory / 「/」)

- **定義**: 単一ディレクトリツリーの根となるディレクトリ。POSIXはファイルの
  世界を「ルートディレクトリを起点とする単一の階層」としてモデル化しており、
  すべてのパス解決はここから始まる。rootユーザーとは別概念なので注意
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)
- **関連用語**: FHS、マウント、root

### リポジトリ(repository)

- **定義**: パッケージ本体と、その署名付き目録・索引を集めて配布するサーバー上の
  置き場。aptは公開鍵→目録→索引→パッケージというハッシュと署名の連鎖で
  内容を検証する。`apt update` は目録と索引を手元に同期する操作。
  Gitのリポジトリとは別概念なので文脈で区別する
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 8 apt`、`man 5 sources.list`
- **関連用語**: パッケージ、パッケージマネージャ、依存関係

### リダイレクト(redirect)

- **定義**: コマンドの標準入出力の接続先を、端末からファイル等へ付け替える
  シェルの機能(`>`、`<`、`2>` など)。実体はファイルディスクリプタテーブルの
  付け替えで、fork後・exec前に子プロセス側で行われる。記号の解釈は
  シェルが行い、コマンド自身には渡らない
- **初出章**: `01_intro/02_shell_and_commands.md`
- **関連用語**: 標準入出力、ファイルディスクリプタ、シェル

### 例外(exception)

- **定義**: 実行中の命令自身が引き金となってカーネルに制御を移す出来事
  (ゼロ除算、不正なメモリアクセス、コピーオンライトの書き込み禁止違反など)。
  外部から届く割り込みと並ぶ、ユーザー空間からカーネルへ入る正規の門の1つ。
  飛び先はカーネルが事前に CPU へ登録済みで、ユーザー側は選べない
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
  (CoW の文脈での先出しは `02_process_kernel/01_process_thread_basics.md`)
- **関連用語**: 割り込み、システムコール、特権レベル、コピーオンライト

### ロードアベレージ(load average)

- **定義**: 「走行可能(R)+割り込み不能な待ち(D)」のタスク数の
  指数移動平均(1分・5分・15分)。D 状態を含むため、CPU 需要だけでなく
  ディスク・NFS の詰まりでも上昇する点が CPU 使用率との大きな違い。
  絶対値ではなくコア数との比で読む。`/proc/loadavg`、`uptime` 等で見える
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連一次情報源**: `man 5 proc`
- **関連用語**: ランキュー、スケジューラ

### ロードバランシング(load balancing)

- **定義**: CPU ごとに分かれたランキュー間の偏りを、タスクの移送
  (migration)で均すスケジューラの機能。移送はキャッシュ・TLB の足跡
  (cache hot)を失うコストを伴うため、SMT → LLC 共有コア群 → NUMA ノード →
  マシン全体というスケジューリングドメインの階層ごとに、近いほど頻繁に・
  遠いほど慎重に行われる
- **初出章**: `02_process_kernel/04_scheduler.md`
- **関連用語**: ランキュー、スケジューラ、TLB

### 割り込み(interrupt)

- **定義**: ハードウェアがカーネルに出来事(ディスクI/O完了、パケット到着、
  タイマー等)を通知する仕組み。システムコールと並ぶ、カーネルが動くきっかけの
  一つ。詳細は `02_process_kernel/02_syscall_context_switch.md` で扱う
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、システムコール
