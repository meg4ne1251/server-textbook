# 進捗管理ファイル

このファイルは、サーバー/Linuxシステム基礎教科書プロジェクトの進捗を記録し、
セッション(会話)をまたいで作業を引き継ぐためのものです。

**新しいセッションを始めるときは、まずこのファイル全体を読んでから、
「次のステップ」に記載された箇所から着手してください。**

---

## 現在の状態

- 完了ステップ: Step 0〜20(分野01〜04完了、分野05は1/3章)
- 次のステップ: Step 21 `05_virtualization_containers/02_kvm_qemu.md`
  (KVM/QEMUによるハードウェア仮想化)から着手する

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

## Step 13: `03_filesystem_storage/03_inode_block_management.md` (完了日: 2026-07-19)

- 完了内容: 章の軸を「マッピング(inodeはどう中身を指すか)」と「割り当て
  (どこに書くかを決める)」の2問に設定。ブロックグループ(128MiB、局所性、
  flex_bg)、間接ブロック(ext2/3)→エクステント(L,N,P・inode内4件・B木)、
  unwritten(Step 12宿題回収、fallocate)、スパースファイル、mballoc・
  遅延割り当て(前章のrename 0バイト問題の背景を回収)、ディスク上inode
  (256バイト、i_block 60バイトの可変用途、fast symlink)、inode総数の
  mkfs時固定とdf -i、ディレクトリエントリ・htree・削除の跡地併合、
  ハードリンク(Step 4/11宿題回収、EXDEV・ディレクトリ不可の理由)、
  孤児リスト、XFS対比(AG・B+木2本・動的inode)を執筆。
- 決定事項: (1) glossaryへ登録: エクステント、間接ブロック、スパース
  ファイル、遅延割り当て、ハードリンク、ブロックグループ。(2) 定型
  「遅らせると、まとめられて、賢く決められる」を導入(デマンドページング・
  ライトバック・遅延ロギング・遅延割り当てを貫く反復原理として明示)。
  (3) 「rmは名前を消すだけ」をディスクレベルで完結(rec_len併合で名前すら
  残る)。(4) buddy/mballocの内部詳細・e4defragの手順・inline_data機能の
  詳細は深追いせず不掲載。ビットマップは一般語のためglossary登録せず。
- 未解決・要検証事項: inode既定サイズ256バイト・bytes-per-inode既定
  16KiB(mke2fs.conf)は現行e2fsprogsで要確認。flex_bg既定16グループ、
  エクステント1件上限32768ブロック、htree実用上最大2段(3段対応
  largedirの有無)はLinux 7.0のext4文書で要確認。ext4の孤児リストは
  従来のスーパーブロック起点リストとorphan_file機能(新しめ)のどちらを
  基準にするか要確認(本文は従来方式ベースの簡略記述)。filefrag -v・
  stat・df -iの出力形式はUbuntu 26.04実機で要検証。XFSの動的inode
  割り当て・B+木2本(bnobt/cntbt)の名称は本文に出さず概念のみとした。
- 次のステップ: Step 14 `03_filesystem_storage/04_lvm_raid.md`

## Step 14: `03_filesystem_storage/04_lvm_raid.md` (完了日: 2026-07-19)

- 完了内容: 章の軸を「ブロックの世界にも翻訳表を挟む」(Step 8 の仮想メモリの
  型の再演)に設定。ブロックデバイスの抽象と積み重ね(パーティション→
  device mapper)、LVM 3層(PV/VG/LV、PE 4MiB、操作=翻訳表の書き換え、
  方針はユーザーランド/機構はカーネル)、RAID レベルと XOR パリティの原理、
  md と dm-raid の実装共有、dmsetup table の実物、LVM メタデータ(テキスト・
  全PV複製)、スナップショット2方式(退避CoW / thin)、RMW ペナルティ、
  縮退・再構築・URE・スクラブ、write hole(bitmap / journal / PPL)を執筆。
- 決定事項: (1) glossaryへ登録: device mapper、LVM、md、RAID、
  シンプロビジョニング、ストライピング、スナップショット、デグレード、
  パーティション、パリティ、ブロックデバイス、ミラーリング。
  (2) LVM の「エクステント」と ext4 のエクステントの用語衝突は、LVM 文脈を
  「PE / LE」と表記して回避すると明文化。(3) 定型「層ごとの整合性は、
  層ごとに自分で守る」を導入(write hole と ext4 ジャーナリングの守備範囲の
  区別)。Step 5 の「initramfs が RAID を組み立てる」の伏線をトラブル
  シューティングで回収。(4) bio・I/O スケジューラ等のブロック層内部は
  本章では扱わず、必要になった時点で導入する方針(深追い回避)。
  ハードウェアRAID の内部・btrfs/ZFS の統合型(FS+RAID)は言及のみ。
- 未解決・要検証事項: PE 既定 4MiB・md チャンク既定 512KiB は現行の
  lvm2 / mdadm で要確認。write-intent bitmap の「大きな配列では既定で有効」の
  閾値(mdadm の既定)は要確認。--write-journal / PPL の導入カーネル版
  (4.4 / 4.12 前後)は本文で版を出さずに回避——正確な版は要確認。
  speed_limit_min/max の既定値は本文に数値を出していない。dmsetup table・
  lvs -o seg_pe_ranges・/proc/mdstat の出力形式は Ubuntu 26.04 実機で要検証。
  スナップショット(旧方式)のチャンクサイズ既定は断定を避けた。
  Documentation/admin-guide/device-mapper/snapshot.rst のパスは Linux 7.0 で
  要確認。ESP が素のパーティションである必要の記述は UEFI 仕様ベースの
  一般論として記載。
- 次のステップ: Step 15 `03_filesystem_storage/05_network_storage.md`

## Step 15: `03_filesystem_storage/05_network_storage.md` (完了日: 2026-07-19)

- 完了内容: 分野03の最終章。章の軸を「どの層でネットワークを挟むかが、
  共有できるものと整合性の責任の在り処を決める」に設定。NFS(ファイルの層/
  NAS)と iSCSI(ブロックの層/SAN)の対比表、RPC とファイルハンドル
  (名前と inode の分離の再演)、v3 ステートレス→v4 リースの設計転換、
  close-to-open 整合性と属性キャッシュ、UNSTABLE WRITE/COMMIT と verifier、
  hard/soft と D 状態(分野02のロードアベレージ伏線回収)、silly rename
  (.nfsXXXX)、SCSI→iSCSI(イニシエータ/ターゲット/IQN/LUN)、
  単一ホスト用 FS の LUN 二重マウント破損の理由(調停者の不在)、LIO、
  dm-multipath(前章の「翻訳表」の型で経路を吸収)を執筆。
- 決定事項: (1) glossaryへ登録: close-to-open整合性、iSCSI、LUN、
  NAS / SAN、NFS、RPC、イニシエータ / ターゲット、ファイルハンドル、
  マルチパス。(2) TCP/IP 自体の詳細は network-guide、ソケット内部は
  分野04(Step 16)に委ねると章内で明示(橋渡しの予告)。(3) クラスタ
  ファイルシステム(GFS2/OCFS2)と分散ロックは言及のみで分野08に委譲。
  fstab の `_netdev` と起動順序の話は分野06に委譲。(4) FC / NVMe-oF は
  「ブロックの層で切る」仲間としての言及のみ。pNFS も言及のみ。
- 未解決・要検証事項: 属性キャッシュ既定(acregmin=3s〜acregmax=60s、
  acdirmin=30s)は man 5 nfs(6.x系)に基づく——現行 nfs-utils で要確認。
  NFS の TASK_KILLABLE 化(2.6.25、intr/nointr の無効化)の記述は本文では
  版を出さず「現代のカーネルでは」とした。iscsiadm / lsblk / findmnt /
  exportfs の出力形式は Ubuntu 26.04 実機で要検証。Ubuntu 26.04 の
  nfs-kernel-server 既定エクスポートオプション(sync/root_squash)と
  マウント既定(vers=4.2 か)も実機で要確認。silly rename が NFSv4 でも
  実装上使われ続けている点は簡略化して記述(v4 はデリゲーションとの
  関係が複雑なため深追い回避)。
- 次のステップ: Step 16 `04_linux_network_stack/01_socket_api.md`

## Step 16: `04_linux_network_stack/01_socket_api.md` (完了日: 2026-07-19)

- 完了内容: 分野04の最初の章。「read/write の契約は守ったまま、宛先・接続・
  流量・失敗というファイルになかった4つの事情を専用の道具が扱う」を軸に、
  ソケット=「すべてはファイル」のネットワーク延長、ファミリ×型(ストリーム
  の境界非保存=パイプの再演)、bind とポート番号(特権ポート・エフェメラル)、
  5システムコールと3ウェイハンドシェイク(カーネルが処理、accept は完成品の
  取り出し、4つ組識別)、read/write の待ち(S状態・流量制御・SIGPIPE 回収)、
  多重化(C10K、select/poll→epoll、signalfd 合流=Step 10 回収)、
  struct socket/sock 二層、sk_buff(headroom、コピー2回)、送信の道のり
  (ルーティング表=network-guide 橋渡し、qdisc=Step 19 予告)、受信の道のり
  (hardirq 前半→softirq/NAPI=Step 7 の割り込み後半の伏線回収)、
  accept/SYN キュー、epoll 内部(ready リスト、O(発生数))を執筆。
- 決定事項: (1) glossaryへ登録: epoll、NAPI、sk_buff、softirq、TCP、UDP、
  ソケット、ノンブロッキングI/O、バックログ(accept キュー)、ポート番号。
  (2) network-guide 相互参照の形式を確定し style_guide 2.5 を更新
  (実例: `../../network-guide/01_fundamentals/02_routing_table_basics.md`。
  役割分担: network-guide=L2/L3・ルーティング主、本書=カーネル実装主)。
  (3) TCP/UDP のプロトコル詳細(輻輳制御・再送)は本書の範囲外と glossary
  にも明記。オフロード(GSO/GRO)は発展の言及のみ。(4) netfilter の検問所は
  受信経路で1文予告(Step 17)、qdisc は送信経路で予告(Step 19)。
  SYN cookie / ファイアウォールの詳細は分野07 に委譲。複数キュー NIC
  (RSS 等)の分散は分野09 と絡めて扱う想定で本文は言及のみ。
- 未解決・要検証事項: somaxconn 既定 4096(Linux 5.4 以降)は実行例の
  ss 出力(Send-Q=4096)として記載——Linux 7.0 / Ubuntu 26.04 実機で
  要検証。エフェメラルポート範囲 32768〜60999、TIME_WAIT 60秒
  (Linux 固定値)も基準版で要確認。strace -e trace=%network の curl 出力
  (EINPROGRESS)と /proc/softirqs は実機で要検証。napi.rst のパスは
  6.x系に基づく——Linux 7.0 で要確認。sk_buff のコピー「原則2回」は
  sendfile/splice 等のゼロコピー経路を除いた記述(ゼロコピーは深追いせず
  不掲載)。C10K の初出(1999年頃、Dan Kegel)は年のみ記載。
- 次のステップ: Step 17 `04_linux_network_stack/02_netfilter_nftables.md`

## Step 17: `04_linux_network_stack/02_netfilter_nftables.md` (完了日: 2026-07-19)

- 完了内容: 章の軸を「共通の道の要所に固定の検問所(フック)を置き、
  機能は登録する」に設定。5フックとルーティング判断の位置関係(DNAT が
  prerouting である必然)、verdict(DROP=黙殺)、優先度の列(conntrack
  -200→dstnat -100→filter 0→srcnat 100)、conntrack の4状態と2タプル
  (NAT 自動逆変換の正体)、iptables の4限界→nftables の仮想機械設計
  (式の列、set/map、inet 統一、原子的更新)、SSH 接続1本の通し稽古、
  実行例(counter の偏り=顔パスの証拠、conntrack -L)、前章の
  「refused ではなくタイムアウト」の伏線回収を執筆。
- 決定事項: (1) glossaryへ登録: iptables、NAT、netfilter、nftables、
  コネクション追跡、ファイアウォール。標準表記:「検問所(フック)」
  「verdict(評決)」「台帳」(conntrack)、conntrack 状態は
  NEW / ESTABLISHED / RELATED / INVALID の英大文字。(2) 定型
  「ルールの精査は最初の1通だけ、2通目からは台帳で顔パス」を導入
  (フィルタと NAT の両方を貫く)。(3) NAT のプロトコル的意味・
  アドレス設計は network-guide 主、本書はカーネル機構主と明記。
  ufw / firewalld・SYN cookie・堅牢化の方針設計は分野07に委譲。
  ingress / egress フック(netdev ファミリ)は発展の言及のみで
  Step 19(tc/qdisc)との接点を予告。(4) x_tables 内部・NFQUEUE の
  詳細・nftables の flowtable(ハードウェアオフロード)は深追いせず
  不掲載。
- 未解決・要検証事項: conntrack タイムアウト既定(TCP established
  5日、UDP 30秒〜数分)は nf_conntrack-sysctl.rst(6.x系)に基づく——
  Linux 7.0 での既定値は要確認。優先度の数値(-200/-100/0/100)は
  include/uapi/linux/netfilter_ipv4.h(6.x系)に基づく。nft の
  counter 出力・conntrack -L の行形式は Ubuntu 26.04 実機で要検証。
  Ubuntu 26.04 の iptables が iptables-nft である点も実機の
  iptables -V で要確認。「フックが空なら実質ゼロコスト」は
  static_key 実装に基づく簡略記述(詳細は本文に出さず)。
  reject(ICMP エラーの種別選択)の詳細は分野07で必要なら展開。
- 次のステップ: Step 18 `04_linux_network_stack/03_network_namespaces.md`

## Step 18: `04_linux_network_stack/03_network_namespaces.md` (完了日: 2026-07-19)

- 完了内容: 章の軸を「仮想メモリの型の再演——スタック一式の複製を作り、
  プロセスごとに眺めを差し替える」に設定。複製されるもの一覧を前2章の
  登場人物に対応づけ(部分共有なしの設計理由=検問所・台帳・ルーティングの
  連携の整合性)、veth ペア(パイプの再演)、ブリッジ(カーネル内 L2
  スイッチ)、ホストをルータとする外部接続(=コンテナ標準構成の正体)、
  struct net / init_net / nsproxy、ソケットの所属は作成時固定、生成
  (clone/unshare/setns)と寿命(fd・/proc/PID/ns/net・bind mount、
  ip netns add の実装)、veth 内部(送信=相方の受信、NET_RX)、
  通し稽古(ns 内から masquerade 経由で外へ、複数 ns の検問所を順に
  通る)、実行例(新品 ns の空っぽ確認、8080 の2重 listen、inode 識別)、
  VXLAN への橋渡し(network-guide 参照)を執筆。
- 決定事項: (1) glossaryへ登録: veth、ネームスペース、ネットワーク
  ネームスペース、ブリッジ。(2) ネームスペース一般(7種の列挙、user ns、
  cgroup ns 等)は深追いせず分野05に委譲——本章は net ns のみ。
  (3) 調査の大原則「観察コマンドは見たいスタックの中で実行する」を定型と
  して導入。(4) network-guide 相互参照2件: L2 スイッチング原理は
  `../../network-guide/02_vlan_vxlan_evpn/01_vlan_basics.md`、VXLAN は
  同 `03_vxlan_fundamentals.md`(ホスト内=ns で分割/ホスト跨ぎ=VXLAN で
  延伸、ブリッジに挿せる同列のデバイスという整理)。(5) 抽象 UNIX
  ドメインソケットが net ns 管轄である点は発展の言及のみ。Docker 固有の
  実装(docker0 等)は「正体の例」として名前のみ、詳細は分野05。
- 未解決・要検証事項: ネットワークネームスペースの導入版「2.6.24
  (2008年)から段階的に」・マウント ns の「2.4.19」は man 7
  namespaces(6.x系)に基づく——記述の粒度は要再確認。新品 ns の lo が
  DOWN で始まる挙動・ip netns exec の /etc/netns/<name>/resolv.conf
  重ねマウント・/run/netns の bind mount の実体は Ubuntu 26.04 実機で
  要検証。veth の実装記述(相方の受信キューへ積み NET_RX を上げる)は
  簡略化——XDP 対応後の veth_xmit の詳細経路は本文に出していない。
  実行例の ss 出力・net:[inode] の値は実機で要検証。ns 破棄時の物理 NIC
  の init_net への帰還は要実機確認(veth は消える)。
- 次のステップ: Step 19 `04_linux_network_stack/04_qdisc_traffic_control.md`

## Step 19: `04_linux_network_stack/04_qdisc_traffic_control.md` (完了日: 2026-07-19)

- 完了内容: 分野04の最終章。章の軸を「行列は唯一の采配の場所」と
  「機構(enqueue/dequeue)と方針(qdisc)の分離=分野02の再演」に設定。
  CPU との差分2点(横取り不可・破棄は正当な合図)、クラスレス/クラスフル+
  filter の木、トークンバケット(rate/burst)とシェーピング/ポリシング、
  HTB(rate/ceil の保証+貸し借り)、公平キューイング(DRR、EEVDF 原論文の
  系譜回収=Step 9)、bufferbloat と CoDel(滞留時間判定・先頭破棄)、
  fq_codel が既定たる理由、dev_queue_xmit(送信者の CPU 時間で dequeue、
  NET_TX の正体回収=Step 16)、mq、noqueue(veth=前章回収)、BQL、
  ingress/clsact と netfilter ingress の境界(Step 17 回収)、実行例
  (既定確認、netem 注射、tbf 統計)を執筆。
- 決定事項: (1) glossaryへ登録: CoDel、fq_codel、HTB、qdisc、tc、
  公平キューイング、シェーピング / ポリシング、トークンバケット、
  バッファブロート。標準表記:「トークンバケット」「バッファブロート」
  「シェーピング(整形)/ポリシング(取り締まり)」。(2) 定型を2つ導入:
  「行列があるところにしか、采配の余地はない」「捨てることは失敗ではなく、
  送信元への合図」。(3) 輻輳制御・ペーシングの詳細は範囲外を維持
  (fq/BBR は発展の言及のみ)。eBPF は tc の取り付け点として言及のみ
  (深掘りする章は未定)。ifb は迂回手段としての言及にとどめた。
  (4) コンテナの帯域制限=veth への qdisc 付与の自動化、として分野05へ
  橋渡し。分野04の4章の総括をまとめに記載。
- 未解決・要検証事項: net.core.default_qdisc=fq_codel(systemd 由来)と
  tc qdisc show の出力形式(mq 配下の fq_codel、noqueue)は Ubuntu 26.04
  実機で要検証。fq_codel 既定値(limit 10240p / flows 1024 /
  quantum 1514 / memory_limit 32Mb)と dev_tx_weight 既定 64、
  txqueuelen 既定 1000 は 6.x 系に基づく——Linux 7.0 で要確認。
  BQL 導入版 3.3・clsact の評価順(tc ingress が netfilter ingress より
  先)は簡略記述。tbf の GSO 塊の扱い(内部でのセグメント化の有無)は
  断定を避け「会計が粗くなる」の表現にとどめた。netem/tbf 実行例の
  数値は例示であり実機で要検証。
- 次のステップ: Step 20 `05_virtualization_containers/01_cgroups_namespaces.md`

## Step 20: `05_virtualization_containers/01_cgroups_namespaces.md` (完了日: 2026-07-20)

- 完了内容: 分野05の最初の章。章の軸を「カーネルに『コンテナ』という概念はない
  ——見えてしまう干渉を遮るネームスペースと、奪ってしまう干渉を遮る cgroups の
  編成に付けた名前」に設定。ネームスペース総論(8種の表と導入版、共通設計=
  nsproxy/fork継承/clone・unshare・setns/procの名札/参照カウント、Step 18 の
  一般化)、PID ns(入れ子・struct pid の多層番号・ns 内 PID 1 の責務と特権・
  unshare は本人を移さない)、mount ns(伝播方針、pivot_root 予告)、
  UTS/IPC/time、user ns(uid_map、capability はネームスペースごと、rootless の
  土台)、cgroup v1→v2(統一階層の動機=コントローラ間連携)、v2 の規則
  (葉にプロセス、subtree_control、weight/max 二本柱)、css_set/css、
  cpu コントローラ=EEVDF のグループスケジューリング(Step 9 伏線回収、
  cpu.weight=nice の一般化)、memory(記帳・max/high/low・グループ限定 OOM=
  Step 8 回収、ページキャッシュ込み)、pids/io(ライトバック帰属=v2 の動機)、
  コンテナ組み立て5手順(veth+qdisc=Step 18/19 回収)、実行例(systemd-run
  MemoryMax で 137、unshare -r -f -p --mount-proc)を執筆。
- 決定事項: (1) glossaryへ登録: cgroup、コンテナ、コントローラ、
  PIDネームスペース、userネームスペース、マウントネームスペース。
  (2) cgroups は v2 のみを基準とし、v1 は「古い資料の読み替え」として
  トラブルシューティングでのみ扱うと確定。標準表記:「コントローラ」
  「記帳(charge)」「取り分」(weight=混雑時の比率/max=絶対の天井の
  二本柱を、HTB の rate/ceil・nice と並ぶ反復の型として明示)。
  (3) capability・seccomp の詳細は分野07、pivot_root の手順とランタイムの
  実装は Step 22、systemd のスライス構造は分野06、PSI は分野09 に委譲。
  (4) mount 伝播(shared/private/slave)は位置づけの理解にとどめ深追いしない。
  仮想マシンとの対比(切る層の違い)は Step 21 の導入への橋とした。
- 未解決・要検証事項: Ubuntu 26.04 の非特権 user ns の AppArmor 制限
  (apparmor_restrict_unprivileged_userns 相当)の既定は要検証(本文にも
  注記済み)。cpu.max 既定 period 100000µs・cpu.weight 範囲 1〜10000 既定
  100・PID ns 最大32段は 6.x 系文書に基づく——Linux 7.0 で要確認。
  /proc/self/cgroup の scope 名、systemd-run --user --scope での
  MemoryMax 挙動(user 権限で委譲されているか)、unshare 実行例の出力は
  Ubuntu 26.04 実機で要検証。time ns の対象クロック(MONOTONIC/BOOTTIME
  のみ)は man 7 time_namespaces に基づく。io コントローラの書き戻し帰属の
  実装詳細(wb_blkcg 等)は本文に出さず概念のみとした。
- 次のステップ: Step 21 `05_virtualization_containers/02_kvm_qemu.md`
