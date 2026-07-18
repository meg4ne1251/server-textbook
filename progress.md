# 進捗管理ファイル

このファイルは、サーバー/Linuxシステム基礎教科書プロジェクトの進捗を記録し、
セッション(会話)をまたいで作業を引き継ぐためのものです。

**新しいセッションを始めるときは、まずこのファイル全体を読んでから、
「次のステップ」に記載された箇所から着手してください。**

---

## 現在の状態

- 完了ステップ: Step 0〜12(分野01・分野02完了、分野03は2章まで完了)
- 次のステップ: Step 13 `03_filesystem_storage/03_inode_block_management.md`
  (inode、ブロック管理)から着手する

---

## ステップ完了ログ

<!--
1ステップ完了するごとに、以下のフォーマットでこの下に追記していく(上書きしない)。

## Step N: <ファイルパス> (完了日: YYYY-MM-DD)

- 完了内容:
- 決定事項:
- 未解決・要検証事項:
- 次のステップ: Step N+1 <次のファイルパス>
-->

## Step 0: `shared/glossary.md`, `shared/style_guide.md` (完了日: 2026-07-13)

- 完了内容: 両ファイルのひな形を確定。glossary.md は五十音順/A〜Zセクション構成の
  空テンプレート、style_guide.md は章構成テンプレート・表記ルール(一次情報源の
  参照方法、英語用語、数値・単位、図解、network-guide 相互参照)を定義。
- 決定事項: style_guide.md の一次情報源の文例を CLAUDE.md「前提バージョンの固定」
  (Linux 7.x 基準、EEVDF標準)に合わせて更新。旧実装は「6.6以前のCFS」のように
  版を添えて対比の文脈で扱うルールを 2.1.1 として明文化。
- 未解決・要検証事項: 基準カーネルの具体版(7.x のどれか)は Step 1 の
  `00_overview/01_roadmap.md` で確定する。EEVDF のカーネルドキュメントの正確な
  ファイルパス(sched-eevdf.rst)は基準版で要確認。基準ディストリビューションも
  Step 1 で決定する。
- 次のステップ: Step 1 `00_overview/01_roadmap.md`

## Step 1: `00_overview/01_roadmap.md` (完了日: 2026-07-13)

- 完了内容: 教科書全体のロードマップを作成。10分野の一覧表、分野間の依存関係を
  示す前提知識マップ(Mermaid)、各分野の到達目標、学習パス3種(通読/運用急ぎ/
  コンテナ目的)、network-guide との役割分担を記述。
- 決定事項: (1) 基準カーネルは Linux 7.0(7.x系)、基準ディストロは
  Ubuntu Server 26.04 LTS と確定。(2) ロードマップでは用語を「予告」するに
  とどめ、glossary.md への正式登録は各用語を本文で初めて定義する章
  (主に 01_intro 以降)で行う。(3) 分野04は network-guide との橋渡しで、
  プロトコル詳細は network-guide 側が主。
- 未解決・要検証事項: Linux 7.0 が執筆時点の実在の最新安定版かは要検証
  (本文にも要検証注記済み)。Ubuntu 26.04 LTS の同梱カーネル版も要確認。
  EEVDF のカーネルドキュメントパス(sched-eevdf.rst)は Step 9 執筆時に確認する。
- 次のステップ: Step 2 `01_intro/01_server_os_kernel_overview.md`

## Step 2: `01_intro/01_server_os_kernel_overview.md` (完了日: 2026-07-13)

- 完了内容: 最初の本文章を執筆。サーバー=役割、OSの2大機能(資源管理・抽象化)、
  カーネル/ユーザーランドの区別、カーネル空間/ユーザー空間とシステムコール、
  層構造の見取り図(ASCII)、`ls` 実行の縦の一往復(Mermaid sequenceDiagram)、
  実行例(uname / os-release / ps)、誤解ベースのトラブルシューティング。
- 決定事項: (1) glossary へ初登録: OS、カーネル、カーネル空間/ユーザー空間、
  カーネルパニック、クライアント、サーバー、システムコール、シェル、
  ディストリビューション、デーモン、ユーザーランド、割り込み。
  (2) 本書の背骨となる一文「カーネルは依頼(システムコール)と通知(割り込み)に
  反応して資源を差配する番人」をこの章で導入。以後の章はこの比喩に立ち返る。
  (3) 「プロセス」「ソケット」等は言及のみで正式定義は各分野の章に委ねる。
- 未解決・要検証事項: 実行例中のカーネル版表記 `7.0.0-19-generic` と
  vmlinuz サイズは Ubuntu 26.04 実機での要検証。x86-64 の特権レベルを
  「ring 0〜3」と簡略に述べた箇所は Step 7(syscall章)で正確に展開する。
- 次のステップ: Step 3 `01_intro/02_shell_and_commands.md`

## Step 3: `01_intro/02_shell_and_commands.md` (完了日: 2026-07-13)

- 完了内容: シェルの本質を「読む→解釈(展開)→起動(fork/exec)→待つ(wait)」の
  ループとして説明。展開・PATH探索・リダイレクト解釈は「コマンド起動前にシェルが
  行う」原則を軸に、fdテーブル、fork後exec前のリダイレクト処理、パイプの
  カーネル内バッファと流量制御、終了ステータスまでを扱った。
- 決定事項: (1) glossary へ登録: PATH、外部コマンド、環境変数、グロブ、
  終了ステータス、端末、パイプ、標準入出力、ビルトインコマンド、
  ファイルディスクリプタ、リダイレクト。標準表記は「ビルトインコマンド」
  「グロブ」に統一。(2) fork/exec/wait はこの章では「3点セットの依頼」として
  導入にとどめ、正式な内部動作は Step 6(02_process_kernel/01)に委ねる。
  (3) style_guide.md 実例ログにコンソール表記(```console、$ プロンプト、
  ← 注釈)を明文化。
- 未解決・要検証事項: パイプバッファの既定容量を「64 KiB(man 7 pipe)」と
  記載——Linux 7.x 基準版での既定値・F_SETPIPE_SZ まわりは Step 10(IPC章)で
  再確認する。bash 既定 PATH の実際の値は Ubuntu 26.04 実機で要検証。
  端末(TTY/PTY)の内部機構は本章では扱わず、必要になった時点で扱う章を決める。
- 次のステップ: Step 4 `01_intro/03_filesystem_hierarchy_permissions.md`

## Step 4: `01_intro/03_filesystem_hierarchy_permissions.md` (完了日: 2026-07-13)

- 完了内容: 単一ツリー+マウント、FHS 3.0の主要ディレクトリと2軸の設計原理
  (静的/可変・共有可能/固有)、UID/GIDと3×3パーミッションモデル、inode先取り、
  検査アルゴリズム(最初に一致した1段のみ・open時に一度だけ)、ディレクトリの
  rwxの意味、umask、特殊ビット(setuid/setgid/sticky)、シンボリックリンクを執筆。
- 決定事項: (1) glossary へ登録: FHS、inode、root、setuid/setgid/stickyビット、
  UID/GID、umask、シンボリックリンク、パーミッション、マウント、ルートディレクトリ。
  標準表記は「パーミッション」「root」に統一。(2) inodeはここで先取り定義し、
  正式な構造は Step 13(03_filesystem_storage/03)に委ねる。ハードリンクも同章へ。
  (3) rootの全能性はcapabilityに分割されている旨のみ言及し、詳細は分野07に委ねる。
  (4)「すべてはファイル」の思想をこの章で明示的に導入(以後の章で再利用する)。
- 未解決・要検証事項: Ubuntu 26.04 の umask 既定値を 022 と記載——pam_umask や
  /etc/login.defs 由来の実際の既定は実機で要検証。stat 実行例の inode 番号等の
  出力形式も実機で要確認。POSIX ACL は存在への言及のみで、扱う章は未定
  (分野07の候補)。
- 次のステップ: Step 5 `01_intro/04_package_and_system_layout.md`

## Step 5: `01_intro/04_package_and_system_layout.md` (完了日: 2026-07-13)

- 完了内容: 入門分野の最終章。パッケージ=ファイル一式+メタデータ+保守スクリプト
  (man 5 deb / Debian Policy)、dpkg(台帳・配置)とapt(取得・依存解決)の2層、
  共有ライブラリと依存関係の由来、署名の連鎖(InRelease→索引→.deb)、起動の
  バトンリレー(UEFI→GRUB→カーネル+initramfs→PID 1→デーモン)と各段階の
  FHS上の住所・管轄分野の対応表を執筆。
- 決定事項: (1) glossary へ登録: initramfs、PID 1(init)、systemd、依存関係、
  共有ライブラリ、パッケージ、パッケージマネージャ、ファームウェア、ブートローダ、
  リポジトリ。標準表記は「パッケージマネージャ」「ブートローダ(長音なし)」
  「PID 1」に統一。(2) 前章の「fdは入場済みの通行証」の比喩を「更新しても実行中の
  プロセス/カーネルには効かない」の説明に再利用(以後も使える型)。(3) 動的リンク
  (ELF/ld.so)の詳細はStep 6以降のexec説明に委ね、本章は概略のみ。(4) UEFI/GRUB
  自体の深掘りは本書では概観のみと明記(管轄分野の対応表に記載)。
- 未解決・要検証事項: apt policy 実行例の bash バージョン表記(5.2.37-1ubuntu1)は
  Ubuntu 26.04 実機で要検証。aptの依存解決器の実装詳細(apt 3.x系のsolver)は
  深入りせず「ヒューリスティクスで解く」とのみ記述——分野07(Step 28)で更新管理を
  扱う際に必要なら再確認。initramfsのカーネルドキュメントパス
  (ramfs-rootfs-initramfs.rst)は基準版での存在を要確認。
- 次のステップ: Step 6 `02_process_kernel/01_process_thread_basics.md`

## Step 6: `02_process_kernel/01_process_thread_basics.md` (完了日: 2026-07-14)

- 完了内容: 分野02の最初の章。プログラム/プロセスの区別、プロセス=資源一式+
  隔離の単位、task_struct(PCB)の骨格(資源は参照で持つ設計)、状態遷移
  (R/S/D/T/Z、stateDiagram)、fork/exec分離の設計理由(posix_spawn対比)、
  CoW、execで生き残るもの/リセットされるもの(CLOEXEC含む)、wait/ゾンビ/
  孤児のreparenting(subreaper言及)、Linuxのタスク観とpid/tgidを執筆。
- 決定事項: (1) glossaryへ登録: exec、fork、wait、PID、task_struct、
  アドレス空間、親プロセス/子プロセス、コピーオンライト、スレッド、
  ゾンビプロセス、プロセス。標準表記: 総称は「exec」「wait」、システムコール名は
  「execve」。Linux文脈は「task_struct」/OS一般論は「PCB」。「コピーオンライト」
  (2回目以降CoW可)。(2) fdテーブルは複製されるが「開いたファイルの状態」
  (読み書き位置)は親子共有——二層構造の詳細はStep 11(VFS)に委ねた。
  (3) 排他制御・競合状態はスレッドの構造理解にとどめ本書では深追いしない方針。
  (4) 環境変数の引き継ぎの正体(fork複製+execのenvp引数)をこの章で回収済み。
- 未解決・要検証事項: task_structのメンバ名(__state、real_parent等)は
  Linux 7.0のsched.hで要確認(6.x系の名称に基づく)。straceのclone出力表記
  (flags=SIGCHLD)とjournaldのスレッド数の実行例はUbuntu 26.04実機で要検証。
  glibcのfork→clone実装の詳細(clone3使用の有無)は断定を避けた。
  D状態の詳細は分野03、シグナルはStep 10、スケジューラ状態遷移はStep 9で展開。
- 次のステップ: Step 7 `02_process_kernel/02_syscall_context_switch.md`

## Step 7: `02_process_kernel/02_syscall_context_switch.md` (完了日: 2026-07-14)

- 完了内容: モード切替とコンテキストスイッチの区別を章の軸に据えた。特権レベル
  (ring 0/3)の正確な展開(Step 2の伏線回収)、カーネルへの3つの門(syscall/
  割り込み/例外、いずれも飛び先は登録済み)、x86-64の呼び出し規約(rax+rdi…、
  POSIX=インタフェース/ABI=機構の役割分担)、errnoの二層構造、vDSO、
  自発的/非自発的スイッチとタイマー割り込み・TIF_NEED_RESCHED、ブロッキング
  readの一部始終(sequenceDiagram)、entry_SYSCALL_64の道中、カーネルスタックと
  pt_regs、context_switch()=switch_mm+switch_to、コストの直接費/間接費、
  KPTI(発展)を執筆。
- 決定事項: (1) glossaryへ登録: ABI、errno、vDSO、カーネルスタック、
  コンテキストスイッチ、特権レベル(ring)、プリエンプション、モード切替、例外。
  (2) 「コンテキストスイッチは常にカーネル内で起きる」「境界を越えたら足場も
  履き替える(カーネルスタック)」を以後の章でも使う定型として導入。
  (3) 割り込み処理の後半(softirq等)は本章では扱わず、分野04(ネットワーク
  スタック)で必要になった時点で導入する方針。(4) 前章のCoWを「例外」の
  実例として接続し、例外をglossaryに正式登録した。
- 未解決・要検証事項: CONFIG_HZは「サーバー向け既定で秒250回程度」と記述——
  Ubuntu 26.04の実際の値は実機で要検証(tickless/NO_HZの話は意図的に省略、
  Step 9で必要なら扱う)。モード切替「数十〜数百ns」/コンテキストスイッチ
  「µs桁」は桁の感覚として記述、厳密値は断定回避済み。
  Documentation/arch/x86/entry_64.rst と kernel-stacks.rst のパスは
  Linux 7.0で要確認(6.x系のパスに基づく)。straceの集計値・vDSOのldd出力は
  Ubuntu 26.04実機で要検証。TLB/PCID/CR3の詳細はStep 8、スケジューラの
  選択アルゴリズム(EEVDF)はStep 9で展開する。
- 次のステップ: Step 8 `02_process_kernel/03_virtual_memory.md`

## Step 8: `02_process_kernel/03_virtual_memory.md` (完了日: 2026-07-14)

- 完了内容: 「翻訳表を1枚挟む」を軸に、仮想メモリなしの3問題(隔離・配置・容量)
  →ページ/PTE属性ビット→多段ページテーブル(4段、CR3=根)→MMU/TLB/PCID
  (前章の間接費・KPTIの伏線回収)→ページフォールト=正常な仕組み(デマンド
  ページング、CoW回収、遅延読み込み、スワップ)、VMAとページテーブルの二層構造、
  フォールトハンドラの分岐フロー(graph TD)、SIGSEGVの正体、回収とOOM killer、
  Huge Page(発展)を執筆。
- 決定事項: (1) glossaryへ登録: mmap、MMU、OOM killer、TLB、VMA、
  オーバーコミット、仮想メモリ(仮想/物理アドレス含む)、スワップ、
  デマンドページング、ページ/ページフレーム、ページキャッシュ、ページテーブル、
  ページフォールト。標準表記:「メジャーフォールト/マイナーフォールト」
  「ファイルバック/アノニマス」「メイプルツリー(Linux 6.1以降)」。
  (2) 二層構造の定型「VMA=予約の台帳(あるべき姿)/ページテーブル=実態
  (いまの姿)」を導入、SIGSEGV判定はVMA側と明確化。(3) buddy/slab等の物理
  メモリアロケータは深追いせず不掲載(必要なら分野03以降)。ページキャッシュの
  詳細は分野03(VFS)、ASLRの深掘りは分野07、SIGSEGV等シグナル機構はStep 10に
  委ねた。(4) x86-64は4段(48bit)を基本とし、5段(57bit、Linux 4.14以降)は
  言及のみ。
- 未解決・要検証事項: Documentation/admin-guide/mm/concepts.rst と
  Documentation/arch/x86/x86_64/mm.rst のパスはLinux 7.0で要確認(6.x系に
  基づく)。TLBエントリ数「千数百程度の桁」は概数——具体CPUの値は断定回避。
  /proc/self/maps・free・vmstat等の実行例の出力形式はUbuntu 26.04実機で要検証。
  maple tree導入版(6.1)とTHP/HugeTLBのドキュメントパスも基準版で再確認。
  スラッシング対策・swappiness等の運用チューニングは分野07/09で扱うか未定。
- 次のステップ: Step 9 `02_process_kernel/04_scheduler.md`

## Step 9: `02_process_kernel/04_scheduler.md` (完了日: 2026-07-14)

- 完了内容: 「機構(context_switch)と方針(pick)の分離」を章の軸に、素朴案
  (FIFO/輪番/優先度)の破綻→重み付き公平分配、スケジューリングクラスの階層
  (stop>deadline>realtime>fair>idle、RTスロットリング)、nice→weight
  (1段≒1.25倍)、流体モデル、CFS(vruntime会計、限界=レイテンシ表現の欠如)、
  EEVDF(lag/資格/仮想締切、量と順番の分離、1995年原論文)、ランキューと
  R状態の実体(Step 6回収)、tick→pickフロー(Step 7のTIF_NEED_RESCHED回収)、
  wakeup preemption、NO_HZ_IDLE(Step 7のCONFIG_HZ宿題回収)、SMPロード
  バランシングとcache hot(Step 8のTLB接続)、発展でSCHED_DEADLINEと
  スライス申告を執筆。
- 決定事項: (1) glossaryへ登録: CFS、EEVDF、nice値、vruntime、スケジューラ、
  スケジューリングクラス/ポリシー、タイムスライス/スライス、ランキュー、
  ロードアベレージ、ロードバランシング。標準表記: EEVDF文脈は「スライス」/
  一般論は「タイムスライス」、「資格(eligibility)」「仮想締切
  (virtual deadline)」「lag(貸し借りの残高)」。(2) 「量(公平)はlagと
  資格が守り、順番(応答性)はスライスが決める」を本章の定型として導入。
  (3) 公平キューイング系譜としてqdisc(分野04)への橋を予告。group
  schedulingは分野05、PSIは分野09に委ねた。(4) HRTICK・NUMAバランシングの
  詳細・O(1)スケジューラの内部は深追いせず不掲載。
- 未解決・要検証事項: base_slice既定値を0.75ms程度と記載——Linux 7.0の
  /sys/kernel/debug/sched/base_slice_ns 実値は要検証。sched-eevdf.rstの
  存在・パスも基準版で要確認(6.x系に基づく)。sched_setattrの
  sched_runtimeによるスライス申告は「Linux 6.12以降・要検証」と注記済み。
  /proc/PID/schedのフィールド名(se.deadline、se.slice)とps CLS列の出力、
  chrt -mの出力形式はUbuntu 26.04実機で要検証。RTスロットリング既定95%は
  cgroup v2環境での挙動変化があれば分野05で再確認。
- 次のステップ: Step 10 `02_process_kernel/05_signals_ipc.md`

## Step 10: `02_process_kernel/05_signals_ipc.md` (完了日: 2026-07-17)

- 完了内容: 分野02の最終章。「隔離した以上、連絡もカーネル経由」を軸に、
  通知(シグナル)とデータ(IPC)の2分類→シグナル=割り込みのプロセス版、
  処分(デフォルト/無視/ハンドラ)、SIGKILL/SIGSTOP不可侵の理由、
  生成・保留・配送の一生と標準シグナルの合流、非同期シグナル安全、
  配送=カーネル出口(TIF_SIGPENDING、Step 7の関所と対)、S/D状態の正体
  (Step 6回収)、ハンドラ=偽装呼び出し+sigreturn、IPC4系統の見取り図、
  パイプ64KiB(Step 3宿題回収)とSIGPIPE、共有メモリ=同一フレームを指す
  PTE(Step 8接続)、UNIXドメインソケットとSCM_RIGHTS、SysV/POSIX 2世代、
  発展でsignalfd/eventfd/pidfdを執筆。
- 決定事項: (1) glossaryへ登録: FIFO、futex、IPC、UNIXドメインソケット、
  共有メモリ、シグナル、シグナルハンドラ、シグナルマスク、セマフォ、
  非同期シグナル安全、メッセージキュー。標準表記:「処分(disposition)」
  「生成/保留/配送」「合流」(標準シグナルのまとめられ)、
  「非同期シグナル安全」。(2) S=シグナルで起こせる眠り/D=起こせない眠り、
  を状態の正式な定義として確定(TASK_KILLABLEは中間として言及)。
  (3) 同期機構(セマフォ/futex)は位置づけの理解のみで深掘りしない方針を
  維持。ジョブ制御(セッション/プロセスグループ)は深追いせず分野06へ、
  ソケット内部は分野04(01_socket_api)へ、signalfd/epoll連携も分野04へ委譲。
- 未解決・要検証事項: SIGRTMIN実効値(glibcが内部使用のため通常34)と
  kill -l の出力形式はUbuntu 26.04実機で要検証。パイプ既定容量64KiB・
  pipe-max-size既定1MiB・PIPE_BUF=4KiBはman 7 pipe(6.x系)に基づく——
  Linux 7.0での変更有無は要確認。/dev/shmとipcsの実行例出力は実機で要検証。
  pidfd系(pidfd_open 5.3以降)の版表記も基準版manで再確認。シグナルフレーム
  の詳細(SA_RESTORER、踏み台の実装がglibc側にある点)は簡略化して記述した。
- 次のステップ: Step 11 `03_filesystem_storage/01_vfs_basics.md`

## Step 11: `03_filesystem_storage/01_vfs_basics.md` (完了日: 2026-07-17)

- 完了内容: 分野03の最初の章。「共通の注文票(契約)」を軸に、ファイル
  システム=データの並べ方の流儀→VFS=インタフェースと実装の分離、
  4大オブジェクト(superblock/inode/dentry/file)と関数ポインタの操作表、
  疑似ファイルシステム(=「すべてはファイル」の実現機構、Step 4回収)、
  fd→struct file→inodeの三層(Step 6の読み書き位置共有の宿題回収)、
  unlinkと参照カウント(rmは名前を消すだけ)、dentry/dcacheとパス解決
  (負のdentry、シンボリックリンク読み替え・マウント乗り換え=Step 4の
  接ぎ木の機構的回収)、openの一部始終(検査はopen時に一度だけの機構)、
  read/writeとページキャッシュ(Step 8宿題回収、writeの遅延書き戻しと
  fsync、mmapとの統一)、superblockとmountの内部を執筆。
- 決定事項: (1) glossaryへ登録: dentry、tmpfs、VFS、オープンファイル記述、
  疑似ファイルシステム、スーパーブロック、パス解決、ファイルシステム。
  標準表記: Linux文脈は「struct file」/POSIX一般論は「オープンファイル
  記述」(task_struct/PCBと同じ使い分けパターン)。(2) VFS inodeは
  「メモリ上の共通形式」とし、ディスク上のinode構造・ブロック管理・
  ハードリンクはStep 13に委ねた。(3) ライトバック/ダーティの正式な
  glossary登録と整合性の議論(ジャーナリング)はStep 12に委ね、本章は
  「fsyncの存在理由」までで止めた。(4) マウントネームスペースは分野05、
  ソケット等のfile化の詳細は分野04に委譲。address_space_operationsの
  名称は本文に出さず「汎用実装がページキャッシュを引く」の粒度に抑えた。
- 未解決・要検証事項: vfs.rst / path-lookup.rst / tmpfs.rst のパスは
  Linux 7.0で要確認(6.x系に基づく)。/proc/sys/fs/dentry-state の
  フィールド構成(負のdentry数の列位置)はman 5 procで要再確認。
  実行例の time cat の数値・free/findmnt出力はUbuntu 26.04実機で要検証。
  ulimit -n の既定値(1024と記載)も実機で要確認。RCU-walk/ref-walkは
  存在の言及のみに抑えた(深掘りするなら発展として別途)。
- 次のステップ: Step 12 `03_filesystem_storage/02_ext4_xfs_journaling.md`

## Step 12: `03_filesystem_storage/02_ext4_xfs_journaling.md` (完了日: 2026-07-19)

- 完了内容: 前章の宿題「書き戻し途中の電源断でFSは壊れないのか」を回収。
  1操作=複数ブロック更新とディスクの原子性の限界→クラッシュ整合性問題、
  fsck全数調査の限界→ジャーナリング(WAL)、「印(コミットレコード)の
  ない日誌はなかったことにする」を背骨に、順序保証(フラッシュ/FUA)、
  data=3モードとorderedの根拠(古いデータの露出)、整合性vs耐久性
  (fsyncとの役割分担)、jbd2内部(inode 8、handle/トランザクションの
  相乗り、コミット7手順、revoke、リカバリ3パス)、XFS(論理ロギング、
  遅延ロギングCIL/AIL、intentロギング)、fsck存続の理由、CoW方式への
  言及を執筆。
- 決定事項: (1) glossaryへ登録: ext4、fsck、jbd2、XFS、クラッシュ整合性、
  ジャーナリング、ダーティ、チェックポイント、トランザクション、
  ライトバック(Step 11から持ち越したダーティ/ライトバックの正式登録を
  ここで実施)。標準表記:「ジャーナル(日誌)」「コミットレコード」
  「チェックポイント」「再生(replay)」「相乗り」(handleのバッチング)。
  (2) 定型を2つ導入:「印のない日誌は、なかったことにする」
  「ジャーナリングが守るのは『矛盾しないこと』であって『消えないこと』
  ではない」。(3) 未書き込み(unwritten)エクステントは言及のみ、
  詳細はStep 13(エクステント)で扱う。btrfs/ZFSのCoW方式は対比の言及
  のみで本書の範囲外と明記。(4) ディスク上の台帳の詳細な配置
  (ビットマップ、ブロックグループ等)はStep 13に委ねた。
- 未解決・要検証事項: ジャーナル既定サイズ「数十MiB〜1GiB程度で自動決定」
  は現行e2fsprogsで要確認(実行例のJournal size: 1024Mも実機で要検証)。
  /proc/fs/jbd2/<dev>-8/infoの出力フィールドはLinux 7.0で要確認(6.x系に
  基づく)。XFSのnobarrier削除をLinux 4.19と記載——正確な版は要確認。
  遅延ロギングの版(2.6.39既定化、3.3以降唯一)も要再確認。
  journalling.rst / admin-guide/ext4.rst / admin-guide/xfs.rst のパスは
  Linux 7.0で要確認。auto_da_allocの現行の既定有効を`man 5 ext4`で要確認。
  ext4のerrors=既定(Ubuntuルートはremount-ro)は実機のtune2fs -lで要検証。
  QEMUのcache=unsafeの言及は分野05で正式に扱う際に再確認。
- 次のステップ: Step 13 `03_filesystem_storage/03_inode_block_management.md`
