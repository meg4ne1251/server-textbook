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

### cgroup(control group / cgroups)

- **定義**: プロセスをグループに束ねて1本の木(階層)に並べ、グループ単位で
  CPU 時間・メモリ量・I/O 帯域・プロセス数などの「取り分」をコントローラで
  制限・分配するカーネル機構。インタフェースは疑似ファイルシステム cgroupfs
  (`/sys/fs/cgroup`)で、グループ作成は mkdir、所属変更・設定はファイルへの
  write。本書は木が全体で1本の **cgroup v2**(unified hierarchy、Linux 4.5 で
  正式化)のみを基準とする。相対配分は weight・絶対上限は max の記法に統一
  され、プロセスは原則として木の葉にだけ置く。ネームスペースと並ぶコンテナの土台
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
  (名前の先出しは `04_linux_network_stack/03_network_namespaces.md`)
- **関連一次情報源**: カーネルドキュメント
  (Documentation/admin-guide/cgroup-v2.rst)、`man 7 cgroups`
- **関連用語**: コントローラ、ネームスペース、コンテナ、systemd

### close-to-open整合性(close-to-open consistency)

- **定義**: NFS が提供するキャッシュ整合性の約束。「クライアント A が
  close した後にクライアント B が open すれば、B は A の書き込みを
  すべて見る」。close 時のダーティページのフラッシュと、open 時の
  サーバーへの属性検証(キャッシュが古ければ破棄)の組で実現される。
  open〜close の間の同時アクセスについては何も保証しない
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: `man 5 nfs`(DATA AND METADATA COHERENCE)
- **関連用語**: NFS、ページキャッシュ、ダーティ

### CoDel(Controlled Delay)

- **定義**: 行列の長さではなくパケットの滞留時間(enqueue から
  dequeue までの時間)を測り、最小滞留時間が target(5 ms)を
  interval(100 ms)にわたって下回らなかったときだけ、行列の先頭から
  パケットを捨て始めるキュー管理アルゴリズム。瞬間のバーストは容認し、
  恒常的な滞留(bufferbloat)だけを罰することで、損失を送信元への
  減速の合図として最速で届ける
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: RFC 8289、`man 8 tc-codel`
- **関連用語**: fq_codel、バッファブロート、qdisc、TCP

### dentry(directory entry / ディレクトリエントリ)

- **定義**: パス名の1成分、すなわち「この名前はこの inode を指す」という
  対応1件をメモリ上に持つ VFS のオブジェクト。集合体は dcache
  (dentry cache)と呼ばれ、パス解決のディスク I/O を省く。存在しない
  名前も「負の dentry」としてキャッシュされ、ENOENT の応答を高速化する
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/vfs.rst)
- **関連用語**: VFS、inode、パス解決

### device mapper(dm / デバイスマッパー)

- **定義**: 仮想的なブロックデバイスを作り、区間ごとの「この範囲への
  アクセスはあのデバイスのあの位置へこの方式で転送せよ」という
  マッピングテーブルで I/O を翻訳するカーネルの汎用枠組み。転送方式は
  ターゲット(linear / striped / raid / snapshot / thin / crypt 等)として
  差し替え可能。LVM・LVM RAID・ディスク暗号化(LUKS)の共通の土台
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/admin-guide/device-mapper/)、`man 8 dmsetup`
- **関連用語**: LVM、ブロックデバイス、RAID

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

### epoll

- **定義**: 多数の fd を1回の眠りでまとめて待つための Linux 固有の
  多重化機構。監視対象の登録(epoll_ctl)と発生の受け取り(epoll_wait)を
  分離し、イベント発生時のコールバックで ready リストに積む設計により、
  コストが監視数ではなく発生数に比例する。select / poll(POSIX)の
  「呼ぶたびに全 fd を検査する」設計の限界(C10K 問題)への回答
- **初出章**: `04_linux_network_stack/01_socket_api.md`
  (存在の言及の先出しは `02_process_kernel/05_signals_ipc.md`)
- **関連一次情報源**: `man 7 epoll`
- **関連用語**: ソケット、ノンブロッキングI/O、ファイルディスクリプタ

### EPT / NPT(Extended Page Table / Nested Page Table)

- **定義**: 「ゲスト物理アドレス→ホスト物理アドレス」の翻訳を行う、
  ハードウェア仮想化支援の第二のページテーブル(Intel が EPT、AMD が
  NPT)。MMU はゲストのページテーブルと EPT を続けて歩く(二次元
  ウォーク)ため、ゲストは自分のページテーブルを trap なしで自由に
  書き換えられる(シャドウページテーブル方式の解消)。代償として
  TLB ミス時のウォークは最悪20回超のメモリ参照になる
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: Intel SDM(VMX の章)、
  カーネルソース(arch/x86/kvm/mmu/)
- **関連用語**: ページテーブル、TLB、KVM、仮想マシン

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

### fq_codel(Fair Queueing Controlled Delay)

- **定義**: フローごとの行列(既定 1024 本。4つ組等のハッシュで
  振り分け)を DRR で公平に巡回し、各行列に CoDel を適用する qdisc。
  バルク転送の遅延・損失はそのフロー自身に降りかかり、小さな対話
  フローは他人の行列と無関係にすぐ順番が来る。公平と低遅延を同時に
  実現する汎用の既定として、systemd が `net.core.default_qdisc` を
  これに設定している
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: RFC 8290、`man 8 tc-fq_codel`
- **関連用語**: CoDel、公平キューイング、qdisc、バッファブロート

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

### HTB(Hierarchical Token Bucket)

- **定義**: トークンバケットをクラスの木に組み上げたクラスフル qdisc。
  クラスごとに rate(混雑時も使える保証帯域)と ceil(親に余りが
  あるとき借りられる天井)を持ち、「保証+貸し借り」で全体帯域を
  分配する——nice / weight による CPU 時間の重み付き分配の帯域版。
  filter に一致しないパケットは `default` 指定がないとどのクラスにも
  入らず素通しになる点が運用上の急所
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: `man 8 tc-htb`
- **関連用語**: トークンバケット、qdisc、シェーピング / ポリシング

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

### iptables

- **定義**: Linux 2.4 以降長く標準だったパケットフィルタリング / NAT の
  ルール体系とツール。プロトコルごとの分立(ip6tables / arptables /
  ebtables)、ルールの線形評価、照合条件の追加にカーネル変更を要する
  構造などの限界から、nftables に世代交代した。現在の Ubuntu / Debian の
  `iptables` コマンドは nftables バックエンドへの互換層(iptables-nft)
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
- **関連一次情報源**: `man 8 iptables`、netfilter プロジェクト(netfilter.org)
- **関連用語**: nftables、netfilter、ファイアウォール

### iSCSI(Internet Small Computer System Interface)

- **定義**: ディスクとの会話の標準語である SCSI コマンドを TCP に載せて
  ネットワークへ流し、遠隔のブロックデバイスをローカルディスクと同じ顔
  (/dev/sdX)で使えるようにするプロトコル。依頼側をイニシエータ、
  貸す側をターゲットと呼ぶ。サーバーはブロックを貸すだけで中身の
  ファイルシステムを関知しないため、台帳の管理と整合性の責任は
  クライアント側にある(SAN の代表方式)
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: RFC 7143、T10 規格群(SAM / SBC)
- **関連用語**: イニシエータ / ターゲット、LUN、ブロックデバイス、NAS / SAN

### jbd2(Journaling Block Device 2)

- **定義**: ext4 のジャーナリングを担う、カーネル内の独立した層
  (fs/jbd2/)。システムコール1回分の原子性の単位(handle)を1つの
  トランザクションに相乗りさせ、既定5秒ごと・fsync 時・領域不足時に
  コミットする。日誌の実体は通常 inode 8 の隠しファイル(循環ログ)
- **初出章**: `03_filesystem_storage/02_ext4_xfs_journaling.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/filesystems/journalling.rst)
- **関連用語**: ext4、ジャーナリング、トランザクション、チェックポイント

### KVM(Kernel-based Virtual Machine)

- **定義**: Linux カーネル自身をハイパーバイザにするカーネルモジュール
  (kvm.ko + kvm_intel.ko / kvm_amd.ko。Linux 2.6.20、2007年)。
  CPU のハードウェア仮想化支援(VT-x / AMD-V)の運転と、EPT による
  メモリ変換だけを担い、デバイスの演技はユーザーランド(QEMU)に
  委ねる。インタフェースは `/dev/kvm` への ioctl の3層(システム /
  VM / vCPU)。vCPU はただのスレッド、ゲスト RAM はただのアノニマス
  メモリ(VMA)として、既存のスケジューラ・メモリ管理がそのまま適用
  される
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/virt/kvm/api.rst)
- **関連用語**: QEMU、ハイパーバイザ、仮想マシン、EPT / NPT、VM exit

### LUN(Logical Unit Number / 論理ユニット)

- **定義**: SCSI / iSCSI のターゲットが貸し出す論理ディスク1つ分の単位
  (およびその番号)。イニシエータがログインすると、LUN がクライアント側に
  新しいブロックデバイスとして現れる。裏の実体(バックストア)には
  ブロックデバイスやファイルを充てられる。単一ホスト用ファイルシステムの
  載った LUN を複数ホストから同時に mount してはならない
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: RFC 7143、`man 8 targetcli`
- **関連用語**: iSCSI、イニシエータ / ターゲット、ブロックデバイス

### LVM(Logical Volume Manager / 論理ボリュームマネージャ)

- **定義**: ブロックデバイスを PV(物理ボリューム)として登録して
  PE(物理エクステント。既定 4 MiB)に等分し、VG(ボリュームグループ)
  というプールに束ね、そこから LV(論理ボリューム)という仮想ブロック
  デバイスを払い出す体系。拡張・増設・引っ越し(pvmove)・スナップ
  ショットが、使用中のままの対応表(LE→PE)の書き換えに還元される。
  実体はユーザーランドのツール群と PV 上のメタデータで、毎回の I/O の
  翻訳は device mapper が行う。LVM の「エクステント」は ext4 の
  エクステントとは別概念(本書では「PE / LE」と表記して区別する)
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: `man 8 lvm`、`man 7 lvmraid`
- **関連用語**: device mapper、ブロックデバイス、パーティション、スナップショット

### md(Multiple Devices ドライバ)

- **定義**: Linux のソフトウェア RAID を実装するカーネルドライバ。複数の
  ブロックデバイスを束ねた配列を `/dev/md0` 等の1つのブロックデバイスと
  して見せる。管理コマンドは mdadm、状態は `/proc/mdstat` で観察できる。
  LVM RAID(dm-raid ターゲット)も内部では md の各レベルの実装
  (パーソナリティ)を再利用している
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/md.rst)、
  `man 8 mdadm`
- **関連用語**: RAID、ブロックデバイス、device mapper

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

### NAPI(New API)

- **定義**: ネットワーク受信の負荷適応機構。暇なときは割り込みで即応し、
  忙しくなると割り込みを止めて softirq の中でリングバッファから
  まとめてパケットを刈り取る(ポーリング)。パケット1個ごとの割り込みで
  CPU が食い尽くされる「割り込みの嵐」を防ぐ。刈り尽くすと割り込みを
  再度有効にして戻る
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/networking/napi.rst)
- **関連用語**: softirq、割り込み、sk_buff

### NAS / SAN(Network Attached Storage / Storage Area Network)

- **定義**: ネットワークストレージの2大分類。NAS はファイルの層で切って
  共有する構成(代表は NFS。台帳はサーバー側にあり、複数クライアントの
  共有をサーバーが調停する)、SAN はブロックの層で切って貸し出す構成
  (代表は iSCSI / Fibre Channel。クライアントにはブロックデバイスが
  見え、台帳の管理はクライアント側)。どの層でネットワークを挟むかが、
  共有の可否と整合性の責任の在り処を決める
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連用語**: NFS、iSCSI、VFS、ブロックデバイス

### NAT(Network Address Translation / ネットワークアドレス変換)

- **定義**: パケットの宛先や送信元のアドレス・ポートを、カーネル通過の
  途中で書き換える機構。宛先の書き換え(DNAT)はルーティング判断前の
  prerouting、送信元の書き換え(SNAT / masquerade)は判断後の
  postrouting で行う。Linux の実装はコネクション追跡の上に載っており、
  ルールの評価はフローの最初の1パケットだけ、以後と逆方向は台帳
  (conntrack エントリ)に従って自動で書き換えられる。アドレス設計上の
  意味・使い方は network-guide が主
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
- **関連一次情報源**: netfilter プロジェクト(netfilter.org)、`man 8 nft`
- **関連用語**: netfilter、コネクション追跡、nftables

### netfilter

- **定義**: Linux ネットワークスタックの要所5箇所(prerouting / input /
  forward / output / postrouting)に置かれたフック(検問所)の枠組み。
  カーネル内の各機能が nf_hook_ops でフックに処理を優先度順に登録し、
  パケットごとに verdict(ACCEPT / DROP 等)を返す。パケット
  フィルタリング・NAT・コネクション追跡の共通の土台で、ルール処理
  エンジン(nftables 等)はこの上に載る
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
- **関連一次情報源**: netfilter プロジェクト(netfilter.org)、
  Linuxカーネルソース(include/linux/netfilter.h)
- **関連用語**: nftables、コネクション追跡、NAT、sk_buff

### NFS(Network File System)

- **定義**: ファイル操作の依頼(LOOKUP / GETATTR / READ / WRITE 等)を
  RPC でサーバーへ送る分散ファイルシステム。ファイルの指定にはパス名で
  なく不透明なファイルハンドルを使う。v3(RFC 1813)まではサーバーが
  クライアントの状態を覚えないステートレス設計、v4(RFC 7530 / 8881)は
  OPEN / CLOSE とリースを持つステートフル設計。キャッシュの整合性は
  close-to-open 整合性として提供される(NAS の代表方式)
- **初出章**: `03_filesystem_storage/05_network_storage.md`
  (D 状態の文脈での先出しは `02_process_kernel/04_scheduler.md`)
- **関連一次情報源**: RFC 1813、RFC 7530、RFC 8881、`man 5 nfs`、
  `man 5 exports`
- **関連用語**: RPC、ファイルハンドル、close-to-open整合性、VFS、NAS / SAN

### nftables

- **定義**: netfilter のフックに載る現行のルール処理エンジン
  (Linux 3.13 以降)。カーネル側は少数の汎用命令(式)を評価する
  仮想機械だけを持ち、ルールの意味はユーザー空間の `nft` が命令列に
  翻訳して送り込む。テーブル・チェーンはすべてユーザー定義(素の状態は
  空)。inet ファミリによる IPv4/IPv6 の統一、set / map による要素数に
  よらない照合、トランザクションによる原子的な更新が iptables からの
  主な改善点
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
- **関連一次情報源**: `man 8 nft`、netfilter プロジェクト(netfilter.org)
- **関連用語**: netfilter、iptables、コネクション追跡、ファイアウォール

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

### PIDネームスペース(PID namespace)

- **定義**: プロセス番号の空間を分けるネームスペース。他の種類と異なり
  入れ子の階層(最大32段)をなし、プロセスは根から自分の属す段までの
  各段での番号を struct pid の中に同時に持つ(親のネームスペースからは
  親の段の番号で見える)。各ネームスペースの最初のプロセスはその中の
  PID 1 として振る舞い、孤児の引き取り・ハンドラ未登録シグナルの遮断・
  自身の終了で全員 SIGKILL という init の責務と特権をネームスペース内で
  持つ。unshare では呼び出した本人は移らず、次に fork した子から新しい
  空間が始まる
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
- **関連一次情報源**: `man 7 pid_namespaces`、`man 2 unshare`
- **関連用語**: PID、PID 1、ネームスペース、コンテナ

### OS(Operating System / オペレーティングシステム)

- **定義**: ハードウェア資源を複数のプログラムに配分する「資源管理」と、
  ハードウェアを扱いやすい概念(ファイル、プロセス等)に置き換える「抽象化」を
  担うソフトウェア。狭義にはカーネルを、広義にはカーネル+ユーザーランドを指す。
  本書で単に「OS」と書く場合は広義を指し、中核部分を指すときは「カーネル」と書く
- **初出章**: `01_intro/01_server_os_kernel_overview.md`
- **関連用語**: カーネル、ユーザーランド、ディストリビューション

### qdisc(queueing discipline / キューイング規律)

- **定義**: IP 層とドライバの間に挟まる、NIC ごとの送信待ち行列の
  規律。カーネル本体との契約は enqueue(預かれ)と dequeue(1つ
  よこせ)だけで、その間に並べ替える・遅らせる・捨てるかは実装の
  自由——CPU スケジューラと同じ「機構と方針の分離」のパケット版。
  内部にクラスの木を持つクラスフル(htb 等)と持たないクラスレス
  (fq_codel、tbf、netem 等)に大別され、方針の設定は tc コマンドで
  行う。詰まらない仮想デバイス(veth、lo)の既定は行列を持たない
  noqueue
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
  (送信の道のりでの先出しは `04_linux_network_stack/01_socket_api.md`)
- **関連一次情報源**: `man 8 tc`、Linuxカーネルソース(net/sched/)
- **関連用語**: tc、fq_codel、HTB、公平キューイング、softirq

### QEMU

- **定義**: 仮想マシンのデバイスモデル(仮想ディスク・NIC 等の演技)を
  担うユーザーランドのプロセス。単体でも全命令をソフトウェアで解釈する
  エミュレータ(TCG)として動くが、KVM と組むと CPU 実行をカーネルに
  委ね、KVM_RUN の戻り(VM exit)で要求されたデバイスの反応だけを
  演じて再入する。ゲスト RAM は QEMU 自身の mmap 領域で、vCPU は
  QEMU のスレッド。仮想ディスクのフォーマット qcow2 と `cache=`
  オプション(フラッシュの翻訳方針)もこの層の管轄
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: QEMU ドキュメント(qemu.org)、
  カーネルドキュメント(Documentation/virt/kvm/api.rst)
- **関連用語**: KVM、仮想マシン、virtio、TAPデバイス

### RAID(Redundant Array of Independent Disks)

- **定義**: 複数のディスクを束ねて1つのブロックデバイスに見せ、冗長性
  (故障への耐性)と性能(並列化)を得る技術。束ね方はレベルとして
  類型化される: RAID 0(ストライピング。冗長性なし)、RAID 1
  (ミラーリング)、RAID 5(分散パリティ。1台の故障に耐える)、
  RAID 6(二重パリティ。2台に耐える)、RAID 10(ミラー+ストライプ)。
  守るのは装置の故障に対する可用性のみで、誤操作・論理破壊への備え
  (バックアップ)の代替にはならない
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: Patterson, Gibson, Katz (1988)、
  カーネルドキュメント(Documentation/admin-guide/md.rst)
- **関連用語**: md、パリティ、ミラーリング、ストライピング

### root(スーパーユーザー)

- **定義**: UID 0 を持つ特権ユーザー。伝統的なUNIXモデルではパーミッション検査を
  免除され、すべてのファイルにアクセスできる(現代のLinuxではこの権限は
  capabilityに分割されている。分野07で扱う)。本文の標準表記は「root」とし、
  「スーパーユーザー」「特権ユーザー」は同義として扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連用語**: UID / GID、パーミッション

### RPC(Remote Procedure Call / 遠隔手続き呼び出し)

- **定義**: 「手続き番号と引数を決まった形式に詰めて送ると、相手がその
  手続きを実行して結果を返す」という、関数呼び出しの形をした通信の
  枠組み。NFS の土台は ONC RPC(SUNRPC とも。データ表現は XDR)で、
  応答がなければ再送する。NFSv3 以前は rpcbind 等の補助プロトコルを
  必要としたが、NFSv4 は TCP ポート 2049 番に一本化された
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: RFC 5531(ONC RPC)、RFC 4506(XDR)
- **関連用語**: NFS、システムコール(対比: ローカルの依頼窓口)

### setuid / setgid / sticky ビット

- **定義**: rwx 9ビットの上位に置かれた3つの特殊ビット。setuidは「実行時に
  ファイル所有者の身分(実効UID)で動く」、setgidは実行ファイルでは同様に
  グループの身分、ディレクトリでは「新規ファイルが親のグループを継承」、
  stickyはディレクトリで「他人のファイルの削除を禁止」(例: `/tmp`)
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 1 chmod`、`man 7 inode`
- **関連用語**: パーミッション、root

### sk_buff(socket buffer / skb)

- **定義**: カーネル内でパケット1個を一生運ぶ構造体
  (include/linux/skbuff.h)。ヘッダ分の余白(headroom)を先に確保して
  データを置き、層を通るたびにポインタ(data)だけを動かしてヘッダの
  付け外しを表現することで、スタック通過中のデータコピーを避ける。
  コピーは原則、送信時のユーザー空間→skb と受信時の skb→ユーザー空間の
  2回だけ
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: Linuxカーネルソース(include/linux/skbuff.h)、
  カーネルドキュメント(Documentation/networking/)
- **関連用語**: ソケット、NAPI

### softirq(ソフト割り込み)

- **定義**: 割り込みハンドラ(前半。最小限で即終了)から後回しにされた
  仕事を、割り込みからの出口や専属カーネルスレッド(ksoftirqd)で
  処理する仕組み。「割り込みほど緊急ではないが早めにやるべき仕事」の
  置き場で、ネットワーク受信(NET_RX)・送信(NET_TX)、タイマーなどが
  代表。CPU ごとの稼働量は `/proc/softirqs` で観察できる
- **初出章**: `04_linux_network_stack/01_socket_api.md`
  (「割り込みの後半」としての先出しは `02_process_kernel/02_syscall_context_switch.md`)
- **関連一次情報源**: Linuxカーネルソース(kernel/softirq.c)
- **関連用語**: 割り込み、NAPI、カーネルスタック

### systemd

- **定義**: 現代の主要ディストリビューションがPID 1として採用する、init実装+
  サービス管理の仕組み一式。サービスや起動処理を「Unit」という単位で宣言的に
  管理し、依存関係を解決しながら並行起動する。詳細は分野06
  (`06_systemd_service_mgmt/`)で扱う
- **初出章**: `01_intro/04_package_and_system_layout.md`
- **関連一次情報源**: `man 1 systemd`、`man 7 bootup`
- **関連用語**: PID 1、デーモン

### TAPデバイス(TAP device)

- **定義**: 「片足がカーネル、片足がプロセス」の仮想ネットワーク
  デバイス。カーネル側からは普通のネットワークデバイスに見え、
  プロセス側からは fd に見える。プロセスが fd へ write したフレームは
  カーネル側に受信として現れ、カーネル側からの送信は fd から read
  できる。QEMU / vhost_net が仮想マシンの virtqueue との橋渡しに使い、
  veth と同様にブリッジへ挿せる。L3(IP パケット)版は TUN デバイス
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/networking/tuntap.rst)
- **関連用語**: ブリッジ、veth、virtio、QEMU

### task_struct(PCB / プロセス制御ブロック)

- **定義**: Linuxカーネルがタスク(プロセス/スレッド)1つにつき1個持つ管理台帳の
  構造体(`include/linux/sched.h`)。pid・tgid・状態・親子関係を直接持ち、
  アドレス空間(mm)・fdテーブル(files)・身分(cred)等は独立した構造体への
  参照として持つ。OS一般の用語では PCB(Process Control Block)。本文の標準表記は
  Linuxの文脈では「task_struct」、OS一般論では「PCB」とする
- **初出章**: `02_process_kernel/01_process_thread_basics.md`
- **関連一次情報源**: Linuxカーネルソース(include/linux/sched.h)
- **関連用語**: プロセス、スレッド、PID

### tc(traffic control)

- **定義**: qdisc・クラス・filter を設定するユーザーランドのコマンド
  (iproute2 に含まれる)。カーネル側の機構(net/sched/)に対する
  方針の記述役で、`tc qdisc add / replace / del`、`tc -s qdisc show`
  (統計: backlog / drops / overlimits)が基本操作。実験用の netem
  (遅延・損失の注入)もこの体系の qdisc の1つ
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: `man 8 tc`、`man 8 tc-netem`
- **関連用語**: qdisc、HTB、fq_codel

### TCP(Transmission Control Protocol)

- **定義**: ストリーム型(SOCK_STREAM)ソケットの標準プロトコル。
  バイト列を欠け・重複・順序の入れ替わりなく届けることを、確認応答
  (ACK)と再送、受信ウィンドウによる流量制御、輻輳制御で実現する。
  接続は3ウェイハンドシェイクで確立され、4つ組(両端の IP と
  ポート)で識別される。write の回数(メッセージ境界)は保存しない。
  本書はカーネル実装(ソケット・キュー・状態)の観点のみを扱い、
  プロトコル自体の詳細(輻輳制御等)は範囲外とする
- **初出章**: `04_linux_network_stack/01_socket_api.md`
  (名前の先出しは `03_filesystem_storage/05_network_storage.md`)
- **関連一次情報源**: RFC 9293、`man 7 tcp`
- **関連用語**: ソケット、UDP、ポート番号、バックログ

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

### UDP(User Datagram Protocol)

- **定義**: データグラム型(SOCK_DGRAM)ソケットの標準プロトコル。
  接続の概念を持たず、1通ずつ独立した手紙(データグラム)を宛先指定で
  送る(sendto / recvfrom)。メッセージの境界は保存されるが、到達も
  順序も保証されない。保証が要らない・自前で持つ用途(DNS、NTP、
  QUIC 等)で使われる
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: RFC 768、`man 7 udp`
- **関連用語**: TCP、ソケット、ポート番号

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

### userネームスペース(user namespace)

- **定義**: UID/GID の番号空間を分け、外の番号との対応表
  (`/proc/<PID>/uid_map` / `gid_map`)を持つネームスペース(Linux 3.8 で
  完成)。capability の判定はこのネームスペースごとに行われ、作成者は
  自分の支配範囲に限りフルの capability を持つため、一般ユーザーでも
  他種のネームスペースを root なしで作れるようになる(rootless コンテナの
  土台)。他のすべてのネームスペースは、どれか1つの user ネームスペースに
  所有される
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
- **関連一次情報源**: `man 7 user_namespaces`
- **関連用語**: UID / GID、root、ネームスペース、コンテナ

### vDSO(virtual Dynamic Shared Object)

- **定義**: カーネルが全プロセスのアドレス空間に自動で貼り付ける小さな共有
  ライブラリ。時刻取得(clock_gettime 等)のような高頻度かつ特権不要の
  システムコールを、カーネル空間への移行なしにユーザー空間内の関数呼び出し
  だけで済ませるための仕組み。`ldd` の出力に `linux-vdso.so.1` として現れる
- **初出章**: `02_process_kernel/02_syscall_context_switch.md`
- **関連一次情報源**: `man 7 vdso`
- **関連用語**: システムコール、共有ライブラリ、アドレス空間

### veth(virtual Ethernet pair / veth ペア)

- **定義**: 必ず2枚1組で作られる仮想ネットワークデバイス。片方への
  送信がそのまま相方の受信になる(実装は skb を相方の受信キューへ積んで
  NET_RX softirq を上げるだけで、物理的な配線・信号は存在しない)。
  ペアの片割れを別のネットワークネームスペースへ移すことで、境界を
  またぐ仮想の直結ケーブルとして使う。ブリッジと組み合わせるのが
  コンテナネットワークの標準構成
- **初出章**: `04_linux_network_stack/03_network_namespaces.md`
- **関連一次情報源**: `man 4 veth`、Linuxカーネルソース(drivers/net/veth.c)
- **関連用語**: ネットワークネームスペース、ブリッジ、sk_buff

### VFS(Virtual File System / 仮想ファイルシステム)

- **定義**: ファイル系システムコールと個々のファイルシステム実装の間に
  立つカーネル内の抽象化層。共通のオブジェクトモデル(superblock /
  inode / dentry / file)と関数ポインタの操作表(契約)を定め、
  アプリケーションが実装の違いを知らずに済むようにする。
  「すべてはファイル」の実現機構
- **初出章**: `03_filesystem_storage/01_vfs_basics.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/vfs.rst)
- **関連用語**: inode、dentry、スーパーブロック、オープンファイル記述

### vhost

- **定義**: virtio デバイスの処理(virtqueue の読み書き)を QEMU を
  経由せずカーネル内のワーカーで行う最適化(代表は virtio-net 用の
  vhost_net)。ゲストからの kick は ioeventfd(VM exit を eventfd への
  通知に変換し、vCPU は即ゲストへ戻る)、ゲストへの割り込みは
  irqfd(eventfd への write が仮想割り込み注入になる)で結線され、
  データ経路からユーザーランドが消える
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: カーネルソース(drivers/vhost/)、
  カーネルドキュメント(Documentation/virt/kvm/api.rst の
  ioeventfd / irqfd)
- **関連用語**: virtio、KVM、TAPデバイス、VM exit

### virtio

- **定義**: 準仮想化デバイスの標準仕様(OASIS 標準)。実在ハードの
  そっくりさんではなく、最初から仮想であることを前提に設計された
  架空デバイス(virtio-blk / virtio-net 等)をゲストに見せ、専用
  ドライバと virtqueue(共有メモリ上のディスクリプタテーブル+
  avail / used リング)で通信する。依頼はリングに積むだけで VM exit
  せず、「積んだよ」の合図(kick)だけが exit になり、しかも抑制
  できる——1回の exit でまとめて処理する設計により、完全
  エミュレーションの「レジスタ操作のたびに exit」を解消する
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: OASIS Virtual I/O Device (VIRTIO) Version 1.x、
  カーネルソース(drivers/virtio/)
- **関連用語**: 準仮想化、VM exit、vhost、QEMU

### VM exit / VM entry

- **定義**: ハードウェア仮想化支援(VT-x / AMD-V)における、ゲスト
  世界(non-root モード)とホスト世界(root モード)の間の遷移。
  ハイパーバイザが VMCS に登録した事象(特定の特権命令、I/O、EPT
  フォールト等)が起きると CPU が自動でゲストを中断してホストへ制御を
  返し(VM exit)、処理後に VMLAUNCH / VMRESUME で戻る(VM entry)。
  遷移のたびに VMCS でゲスト/ホスト状態が入れ替わる、コンテキスト
  スイッチのハードウェア版。素のモード切替より重く、VM exit の削減が
  仮想化性能の中心課題となる
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: Intel SDM(VMX の章)、
  カーネルドキュメント(Documentation/virt/kvm/api.rst)
- **関連用語**: モード切替、コンテキストスイッチ、KVM、virtio

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

### イニシエータ / ターゲット(initiator / target)

- **定義**: iSCSI(および SCSI 一般)の2つの役割。イニシエータは
  コマンドを発行して依頼する側(クライアント。Linux では open-iscsi)、
  ターゲットはブロックを貸して依頼に応える側(サーバー。Linux では
  カーネル内実装の LIO)。双方は IQN(iqn.yyyy-mm.逆順ドメイン:識別子
  形式)という名前で識別され、ログインで対応づけられる
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: RFC 7143、カーネルドキュメント(Documentation/target/)
- **関連用語**: iSCSI、LUN、クライアント、サーバー

### エクステント(extent)

- **定義**: 「論理ブロック番号 L から始まる長さ N の範囲は、物理ブロック
  番号 P から連続して置いてある」という (L, N, P) の3つ組で、連続した範囲を
  1レコードで表すマッピングの単位(ext4 / XFS の標準方式)。ext4 では
  inode 内に4件まで直接入り、超えるとエクステントツリー(B木)に成長する。
  「未書き込み(unwritten)」の印を付けると、割り当て済みだが読めばゼロが
  返る状態を表せる(fallocate による事前確保、古いデータの露出防止)
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
  (unwritten の言及の先出しは `03_filesystem_storage/02_ext4_xfs_journaling.md`)
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/ext4/)、
  `man 2 fallocate`
- **関連用語**: inode、間接ブロック、スパースファイル、遅延割り当て

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

### 仮想マシン(virtual machine / VM)

- **定義**: ゲストごとに仮想のハードウェア一式(CPU・メモリ・ディスク・
  NIC)を見せ、その上でゲストに自前のカーネルを丸ごと動かさせる隔離の
  形態。ハードウェアの層で切るため、別 OS・別カーネルを動かせ、隔離の
  境界がホストカーネルの外にもう1枚できる(コンテナとの対比)。
  KVM/QEMU の実装では、ホストから見ればただのプロセス(vCPU =
  スレッド、ゲスト RAM = mmap 領域)として動く
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
  (コンテナとの対比の先出しは
  `05_virtualization_containers/01_cgroups_namespaces.md`)
- **関連一次情報源**: カーネルドキュメント(Documentation/virt/kvm/api.rst)
- **関連用語**: ハイパーバイザ、KVM、QEMU、コンテナ

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

### 間接ブロック(indirect block)

- **定義**: ext2 / ext3 のマッピング方式で使われる「ブロック番号だけが
  並んだ目次専用ブロック」。inode 内の直接参照12個で足りない部分を、
  間接→二重間接→三重間接と目次の階層を深くして指す。ブロック1つにつき
  番号1つを記録するため連続領域を短く表現できない弱点があり、ext4 では
  エクステントに置き換えられた(互換のため読める)
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
- **関連用語**: エクステント、inode、ブロックグループ

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

### 公平キューイング(fair queueing)

- **定義**: フロー(おおむね4つ組)ごとに行列を分け、行列の間を
  ラウンドロビンで巡回して送信機会を配る方式。1巡あたり quantum
  バイト(ほぼ MTU 1個分)ずつ取り出す DRR(Deficit Round Robin)に
  より、パケット数ではなくバイト量で公平を測る。大食いのフローの
  遅延・損失はそのフロー自身に閉じ、明示的な優先度設定なしに対話
  フローが守られる。CPU スケジューラの重み付き公平分配と同族で、
  EEVDF の原論文もこの系譜に属する
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: RFC 8290、Stoica & Abdel-Wahab (1995)
- **関連用語**: fq_codel、qdisc、EEVDF

### コネクション追跡(connection tracking / conntrack)

- **定義**: netfilter のフック(prerouting と output)で全パケットを
  観察し、フロー(4つ組が中心)ごとの状態をハッシュ表の台帳に記録する
  仕組み。パケットは NEW / ESTABLISHED / RELATED / INVALID に分類され、
  ステートフルファイアウォール(「確立済みは通す」)と NAT の自動
  逆変換の土台となる。エントリは方向ごとの2つのタプル(original /
  reply)を持ち、上限は `net.netfilter.nf_conntrack_max`。ESTABLISHED は
  「両方向の往来を観測済み」の意味で、TCP の接続状態とは別物
  (UDP のフローにも付く)
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/networking/nf_conntrack-sysctl.rst)、`man 8 conntrack`
- **関連用語**: netfilter、NAT、nftables、TCP

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

### コンテナ(container)

- **定義**: ネームスペース(見え方の隔離)と cgroup(資源の取り分)を
  中心に、ルートファイルシステムの差し替え(pivot_root)や capability の
  削減などを、ユーザーランドのランタイムが決まった組み合わせで束ねたもの。
  カーネルに「コンテナ」という単一の概念・構造体は存在せず、中身はホストと
  カーネルを共有するただのプロセス群である。ゲストごとに自前のカーネルを
  動かす仮想マシンとは「どの層で切るか」が異なる
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
- **関連一次情報源**: `man 7 namespaces`、カーネルドキュメント
  (Documentation/admin-guide/cgroup-v2.rst)
- **関連用語**: ネームスペース、cgroup、プロセス、カーネル

### コントローラ(controller)

- **定義**: cgroup の木の各グループに資源の制限・分配を課す、資源種別ごとの
  モジュール(cpu / memory / io / pids / cpuset 等)。制限の実行は
  カーネルの既存機構(スケジューラ、メモリ回収と OOM killer 等)が
  グループ単位の会計に基づいて行う。v2 では各グループの
  `cgroup.subtree_control` への書き込みにより、木の上から子グループへ
  使う分だけ有効化して配る
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
- **関連一次情報源**: カーネルドキュメント
  (Documentation/admin-guide/cgroup-v2.rst)
- **関連用語**: cgroup、スケジューラ、OOM killer

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

### シンプロビジョニング(thin provisioning)

- **定義**: プールの上に「書かれたブロックにだけ実領域を割り当てる」
  論理ボリューム(thin LV)を作る方式(スパースファイルのブロック
  デバイス版)。スナップショットはブロックを共有する対応表の複製だけで
  作れるため軽い。見かけの合計サイズがプールの実容量を超えることを許す
  ため、プール使い切り時は全 thin LV の書き込みが破綻する——
  オーバーコミットと同型の問題を持つ
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: `man 7 lvmthin`
- **関連用語**: LVM、スナップショット、オーバーコミット、スパースファイル

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

### シェーピング / ポリシング(shaping / policing)

- **定義**: 帯域制限の2つの流儀。シェーピング(整形)は超過分を
  行列で**待たせて**送信を平滑化する方式で、行列を必要とするため
  送信(egress)側でしかできない。ポリシング(取り締まり)は超過分を
  待たせず**捨てる**方式で、受信(ingress)側でも使え、TCP の損失を
  合図とした間接的な減速を促す。受信済みのパケットは回線帯域を消費
  済みであるため、受信側の制御は本質的にこの間接手段に限られる
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: `man 8 tc-tbf`、`man 8 tc-police`
- **関連用語**: トークンバケット、qdisc、HTB

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

### 準仮想化(paravirtualization)

- **定義**: ゲスト側が「相手は仮想環境である」と知っている前提で、
  仮想化に都合のよいインタフェースを使う方式の総称。歴史的には
  x86 の仮想化不能命令を回避するためのゲストカーネル改造(初期の
  Xen)を指したが、ハードウェア支援(VT-x)後の現代では、VM exit を
  最小化する準仮想化デバイス(virtio)や準仮想化クロック(kvm-clock)
  として残る。対義は実在ハードを忠実に演じる完全エミュレーション
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: OASIS Virtual I/O Device (VIRTIO) Version 1.x
- **関連用語**: virtio、VM exit、ハイパーバイザ

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

### ストライピング(striping)

- **定義**: データをチャンク(一定幅。md の既定 512 KiB)で輪切りにし、
  複数のディスクへ交互に置く方式(RAID 0 の中身)。読み書きが全台に
  並列化され容量も全台分使えるが、冗長性はなく、1台の故障で全体が
  失われる。横一列分(全台のチャンク1周)をストライプと呼ぶ
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連用語**: RAID、パリティ、ミラーリング

### スナップショット(snapshot)

- **定義**: ある瞬間の論理ボリュームの内容を凍結した、もう1つのブロック
  デバイス。全量コピーではなくコピーオンライトで実現される。伝統的方式
  (dm-snapshot)は元 LV への書き込み時に旧ブロックを退避領域へ写す
  (溢れると無効化される)。thin 方式はブロックを共有する対応表の複製で、
  退避コピーが不要。バックアップの「静止点」作りなどに使われる
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: `man 8 lvcreate`、カーネルドキュメント
  (Documentation/admin-guide/device-mapper/snapshot.rst)
- **関連用語**: LVM、コピーオンライト、シンプロビジョニング

### スパースファイル(sparse file)

- **定義**: 書かれたことのない範囲(穴)を持つファイル。穴にはブロックが
  割り当てられず、マッピングに対応レコードが存在しない。読むとカーネルが
  ゼロを返すだけで、ディスクを消費しない。`ls -l` のサイズ(論理)と
  `du`(実割り当て)が乖離する正体で、仮想マシンのディスクイメージ等に
  使われる
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
- **関連一次情報源**: `man 2 lseek`(SEEK_HOLE / SEEK_DATA)
- **関連用語**: エクステント、inode

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

### ソケット(socket)

- **定義**: 通信の端点をファイルディスクリプタとして見せる抽象——
  「すべてはファイル」のネットワークへの延長。アドレスファミリ
  (AF_INET / AF_INET6 / AF_UNIX)と型(SOCK_STREAM / SOCK_DGRAM)で
  性格が決まり、read / write の契約に加えて宛先・接続・流量・失敗を
  扱う専用のシステムコール群(bind / listen / accept / connect 等)を
  持つ。fd の裏は struct file → struct socket(VFS 側の顔)→
  struct sock(プロトコル側の実体)の層構造
- **初出章**: `04_linux_network_stack/01_socket_api.md`
  (UNIXドメインソケットとしての先出しは `02_process_kernel/05_signals_ipc.md`)
- **関連一次情報源**: POSIX(IEEE Std 1003.1)、`man 2 socket`、`man 7 socket`
- **関連用語**: ファイルディスクリプタ、TCP、UDP、UNIXドメインソケット

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

### 遅延割り当て(delayed allocation)

- **定義**: write の時点ではブロックを割り当てず予約だけ行い、実際の
  割り当てをライトバック(書き戻し)の瞬間まで遅らせる ext4 / XFS の方式。
  書き戻し時点ではファイルの全体像が見えるため、連続領域をまとめて確保でき
  断片化を抑える。副作用として rename 置き換えパターンでのデータ喪失の窓を
  広げる面があり、ext4 は auto_da_alloc で緩和している
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
- **関連一次情報源**: `man 5 ext4`
- **関連用語**: ライトバック、ダーティ、エクステント

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

### デグレード(degraded / 縮退)

- **定義**: RAID 配列のメンバーが脱落し、冗長性を失った(または減らした)
  まま動き続けている状態(`/proc/mdstat` では `[U_]` のような欠けとして
  見える)。I/O は継続できるため気づきにくいが、「次の1台の故障で全損」
  という無防備な状態であり、監視による検知と速やかな交換・再構築
  (recovery / resync)が前提となる
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/admin-guide/md.rst)
- **関連用語**: RAID、md、パリティ

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

### トークンバケット(token bucket)

- **定義**: 帯域制限の標準原理。バケツにトークンが一定レート(rate)で
  補充され、深さ(burst)を超えた分はあふれる。パケットの送信には
  バイト数分のトークンが必要で、足りなければ貯まるまで待つ。長期の
  平均速度は rate を超えられない一方、静穏期に貯めた分だけ瞬間の
  バーストは許される——「平均は厳格に、瞬間は寛容に」。クラスレスな
  実装が tbf、階層化したものが HTB
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: `man 8 tc-tbf`
- **関連用語**: HTB、シェーピング / ポリシング、qdisc

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

### ネームスペース(namespace)

- **定義**: カーネルが管理するグローバルな資源(ネットワークスタック、
  マウントの一覧、PID の空間、ホスト名など)を「見え方の集合」に包み、
  プロセスのグループごとに独立した眺めを与える仕組みの総称。所属は
  task_struct → nsproxy でたどられ、fork した子は親の眺めを引き継ぐ。
  cgroups と並ぶコンテナの土台で、全体像は分野05
  (`05_virtualization_containers/01_cgroups_namespaces.md`)で扱う
- **初出章**: `04_linux_network_stack/03_network_namespaces.md`
  (ネットワーク以外の種類は分野05が主)
- **関連一次情報源**: `man 7 namespaces`
- **関連用語**: ネットワークネームスペース、task_struct、プロセス

### ネットワークネームスペース(network namespace / netns)

- **定義**: ネットワークスタック一式——デバイス一覧(lo 含む)、
  IP アドレス、ルーティング表、netfilter のルールセット、conntrack の
  台帳、ソケットとポート番号の空間、`net.*` の sysctl——を1単位として
  複製するネームスペース。実体はカーネル内の `struct net` で、ホスト
  本来のスタックも init_net という1インスタンスにすぎない。部分共有は
  なく、1つのデバイスは常にどれか1つのネームスペースに属する。
  ソケットは作成時のネームスペースに固定される
- **初出章**: `04_linux_network_stack/03_network_namespaces.md`
- **関連一次情報源**: `man 7 network_namespaces`、`man 8 ip-netns`、
  Linuxカーネルソース(include/net/net_namespace.h)
- **関連用語**: ネームスペース、veth、ブリッジ、netfilter、コネクション追跡

### ノンブロッキングI/O(non-blocking I/O)

- **定義**: 読み書きの準備ができていないとき、眠る代わりに即座に
  エラー(EAGAIN / EWOULDBLOCK)を返すモード(O_NONBLOCK)。
  「どの fd が準備できたかをまとめて待つ」多重化(select / poll /
  epoll)と組み合わせ、1つのプロセスで多数の接続を扱う様式の土台と
  なる。本文の標準表記は「ノンブロッキング」とする
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: `man 2 fcntl`(O_NONBLOCK)、POSIX(IEEE Std 1003.1)
- **関連用語**: epoll、ソケット、ファイルディスクリプタ

## は行

### ハードリンク(hard link)

- **定義**: 既存の inode 番号を指すディレクトリエントリをもう1行追加する
  操作(`ln`)、またはその結果としての名前。複数の名前が同じ inode を
  **対等に**指し、inode 側のリンクカウントが参照している名前の数を記録する。
  実体はリンクカウント 0 かつ誰にも open されていない時点で回収される。
  inode 番号が通用する範囲の制約からファイルシステムをまたげず(EXDEV)、
  ツリーの循環を防ぐためディレクトリには作れない(`.` と `..` は例外)
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
  (存在の言及は `01_intro/03_filesystem_hierarchy_permissions.md`)
- **関連一次情報源**: `man 2 link`、POSIX(IEEE Std 1003.1)
- **関連用語**: inode、シンボリックリンク、dentry

### ハイパーバイザ(hypervisor / VMM)

- **定義**: 仮想マシンを実行するソフトウェア。無害な命令はゲストに
  直接実行させ、危険な命令・事象だけを横取り(trap)して、そのゲスト
  専用の仮想ハードウェアへの操作として演じ直す。VMM(Virtual Machine
  Monitor)とも呼ぶ。古典分類ではハードウェア直上の type 1 と OS 上の
  type 2 に分かれるが、KVM は「Linux カーネル自身をハイパーバイザに
  する」設計でこの分類の中間に位置する。本文の標準表記は
  「ハイパーバイザ」(長音なし)
- **初出章**: `05_virtualization_containers/02_kvm_qemu.md`
- **関連一次情報源**: Popek & Goldberg (1974)、Intel SDM(VMX の章)
- **関連用語**: 仮想マシン、KVM、QEMU、VM exit

### パーティション(partition)

- **定義**: 1台のディスクの先頭に区画の一覧表(現代の標準は GPT)を書き、
  範囲1つ1つを独立したブロックデバイス(/dev/sda1 等)として見せる
  仕組み。実体は開始位置をずらすだけの最も素朴な翻訳で、「ブロック
  デバイスの上にブロックデバイスを作る」積み重ねの最初の例
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
  (語としての先出しは `01_intro/03_filesystem_hierarchy_permissions.md`)
- **関連一次情報源**: UEFI Specification(GPT の規定)、`man 8 fdisk`
- **関連用語**: ブロックデバイス、LVM、マウント

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

### バックログ(backlog / accept キュー)

- **定義**: listen 中のソケットで、3ウェイハンドシェイク完了済みの
  接続がアプリケーションの accept を待つ行列(およびその上限を指定する
  listen の第2引数。天井は `net.core.somaxconn`)。ハンドシェイク
  進行中の接続は別の行列(SYN キュー)で待つ。accept が遅れて
  あふれると、成立したはずの接続が捨てられる。`ss -tln` の LISTEN 行では
  Recv-Q が現在の待ち数、Send-Q が上限として見える
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: `man 2 listen`、`man 7 tcp`
- **関連用語**: ソケット、TCP

### バッファブロート(bufferbloat)

- **定義**: 深すぎる送信バッファが引き起こす恒常的な遅延の膨張。
  TCP は損失を「回線が一杯」の合図として速度を調整するため、
  バッファが深いと損失がなかなか起きず、行列は満杯近くで安定し、
  スループットは増えないまま全パケットが満杯の行列を通過するように
  なる(例: txqueuelen 1000 × 1500 バイトは 10 Mbps 回線で 1.2 秒の
  遅延)。2010年代に Gettys らが名付けて問題提起し、CoDel / fq_codel
  という解が生まれた
- **初出章**: `04_linux_network_stack/04_qdisc_traffic_control.md`
- **関連一次情報源**: Gettys & Nichols, "Bufferbloat: Dark Buffers in
  the Internet"(CACM 2012)、RFC 8289
- **関連用語**: CoDel、fq_codel、qdisc、TCP

### パリティ(parity)

- **定義**: RAID 5/6 が使う誤り訂正用の冗長データ。RAID 5 の P は
  ストライプ内データの XOR で、「失われた1台は残り全部の XOR に等しい」
  という性質により n−1 台分の容量で1台の故障に耐える。RAID 6 は
  リード・ソロモン符号による第2のパリティ Q を加えて2台に耐える。
  部分更新時には旧データ・旧パリティの読み戻しが必要になる
  (read-modify-write ペナルティ)
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: Patterson, Gibson, Katz (1988)、
  カーネルドキュメント(Documentation/admin-guide/md.rst)
- **関連用語**: RAID、md、ストライピング

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

### ファイアウォール(firewall)

- **定義**: 通過するパケットを規則に基づいて許可・拒否(・書き換え)する
  仕組みの総称。Linux ではカーネル内の netfilter + nftables が実装の
  本体で、ufw / firewalld などの管理ツールは最終的に nftables の
  ルールを書く前端にあたる。DROP は差出人に何も通知せず捨てるため、
  「refused ではなくタイムアウト」がファイアウォールを疑う合図になる。
  方針設計・運用は分野07(`07_operations_security/04`)で扱う
- **初出章**: `04_linux_network_stack/02_netfilter_nftables.md`
  (言及の先出しは `04_linux_network_stack/01_socket_api.md`)
- **関連一次情報源**: `man 8 nft`、netfilter プロジェクト(netfilter.org)
- **関連用語**: netfilter、nftables、NAT

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

### ファイルハンドル(file handle)

- **定義**: NFS でファイルを指定するための、クライアントには不透明な
  識別子。クライアントは LOOKUP(ディレクトリのハンドル, 名前)で
  取得し、以後の READ / WRITE はハンドルだけで行う。サーバー側の実装は
  概ね「ファイルシステム ID + inode 番号 + 世代番号」で、名前と inode の
  分離という UNIX の設計がプロトコルの語彙に現れたもの
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: RFC 1813、RFC 7530
- **関連用語**: NFS、inode、パス解決

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

### ブリッジ(bridge / Linux bridge)

- **定義**: カーネル内のソフトウェア実装による L2 スイッチ。ブリッジ
  デバイス(br0 等)を作り、veth や物理 NIC を「ポート」として挿すと、
  MAC アドレスの学習(FDB)に基づいてポート間でフレームを転送する。
  多数のネームスペースを束ねる集線装置として、コンテナネットワークの
  標準部品(docker0 等)。L2 スイッチングの動作原理そのものは
  network-guide が主
- **初出章**: `04_linux_network_stack/03_network_namespaces.md`
- **関連一次情報源**: `man 8 ip-link`(bridge タイプ)、
  Linuxカーネルソース(net/bridge/)
- **関連用語**: veth、ネットワークネームスペース、NAT

### ブロックグループ(block group)

- **定義**: ext4 がデバイス全体を等分する固定サイズの区画(4 KiB ブロック
  では 32768 ブロック = 128 MiB)。各グループがブロック/inode ビットマップと
  inode テーブルを持ち、「ファイルの inode と中身のデータを同じグループに
  置く」という局所性の方針の土台となる。flex_bg は複数グループ分の台帳を
  先頭グループに寄せる発展。XFS の対応物はアロケーショングループ(AG)で、
  こちらの主目的は割り当ての並列性
- **初出章**: `03_filesystem_storage/03_inode_block_management.md`
- **関連一次情報源**: カーネルドキュメント(Documentation/filesystems/ext4/)
- **関連用語**: inode、スーパーブロック、エクステント

### ブロックデバイス(block device)

- **定義**: 「固定長の区画(セクタ)が番号順に並び、どの番号にも順不同で
  読み書きできる装置」というカーネルの抽象(`ls -l` の先頭が `b` の
  特殊ファイル)。実ディスクも、パーティション・md 配列・LVM の LV の
  ような仮想デバイスも同じ顔で見え、ブロックデバイスの上にブロック
  デバイスを積み重ねられる。mount が要求するのはこの顔だけで、裏の
  構成をファイルシステムは関知しない。対になる抽象は番地を持たない
  キャラクタデバイス(先頭 `c`)
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連一次情報源**: `man 4 sd`、カーネルドキュメント(Documentation/block/)
- **関連用語**: パーティション、device mapper、md、マウント

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

### ポート番号(port number)

- **定義**: 1台のマシンの中の通信の窓口を区別する16ビットの番号
  (0〜65535)。IP アドレスがマシンを指し、ポート番号がその中の
  サービスを指す。サーバーはよく知られた番号(22、80 等)に bind して
  待ち、1024 未満への bind は特権(CAP_NET_BIND_SERVICE)を要する。
  クライアント側はカーネルが空き番号(エフェメラルポート)を自動で
  割り当てる
- **初出章**: `04_linux_network_stack/01_socket_api.md`
- **関連一次情報源**: `man 7 ip`、`man 5 services`
- **関連用語**: ソケット、TCP、UDP

## ま行

### マウント(mount)

- **定義**: ディスク等の装置や疑似ファイルシステム(/proc、/sys)の中身を、
  単一ディレクトリツリーのあるディレクトリに「接ぎ木」して見せる操作。
  木は一本のまま裏側の実体だけが切り替わる。内部機構(VFS)は
  `03_filesystem_storage/01_vfs_basics.md` で扱う
- **初出章**: `01_intro/03_filesystem_hierarchy_permissions.md`
- **関連一次情報源**: `man 8 mount`
- **関連用語**: ルートディレクトリ、FHS

### マウントネームスペース(mount namespace)

- **定義**: マウントの一覧(ディレクトリツリーの見え方)を分ける
  ネームスペース(Linux 2.4.19。ネームスペースの最古参)。作成時点の
  マウントテーブルの複製から始まり、以後の mount / umount は原則として
  その中だけの出来事になる。マウント1件ごとの伝播方針
  (shared / private / slave)により、ネームスペース間でマウントイベントを
  転送することもできる。コンテナのルートファイルシステム差し替え
  (pivot_root)の土台
- **初出章**: `05_virtualization_containers/01_cgroups_namespaces.md`
  (委譲の予告は `03_filesystem_storage/01_vfs_basics.md`)
- **関連一次情報源**: `man 7 mount_namespaces`、カーネルドキュメント
  (Documentation/filesystems/sharedsubtree.rst)
- **関連用語**: マウント、ネームスペース、パス解決、コンテナ

### マルチパス(multipath / dm-multipath)

- **定義**: 同じ LUN への複数の経路(NIC・スイッチの2系統化などで
  生じる)を1つの仮想ブロックデバイスに束ねる device mapper の
  ターゲット。個体識別子(WWID)で「別名で見えている同じディスク」を
  認識し、I/O を経路へ振り分け、経路故障時は multipathd が検知して
  残りへ切り替える。linear が位置を、raid が台数を吸収したのに続き、
  経路を吸収する翻訳層
- **初出章**: `03_filesystem_storage/05_network_storage.md`
- **関連一次情報源**: `man 8 multipathd`、`man 5 multipath.conf`
- **関連用語**: device mapper、iSCSI、LUN、ブロックデバイス

### ミラーリング(mirroring)

- **定義**: 同じ内容を複数のディスクへ複製して書く方式(RAID 1 の中身)。
  1台が壊れても残りで続行でき、読み出しは複数台に分散できる。容量効率は
  1/2。複製は忠実であるため、誤操作や論理破壊もそのまま全台へ写る
  (バックアップの代替にはならない)
- **初出章**: `03_filesystem_storage/04_lvm_raid.md`
- **関連用語**: RAID、ストライピング、パリティ

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
