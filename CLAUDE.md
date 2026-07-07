# サーバー/Linuxシステム基礎教科書プロジェクト

## プロジェクトの目的

これはMeganeがサーバー/インフラエンジニアとしてエンタープライズレベルの専門性を身につけるための、
個人学習用技術教科書を作成するプロジェクトです。
単なる資格試験対策やコマンド集ではなく、**カーネルのソースコード・POSIX仕様・
公式ドキュメントレベルまで踏み込んだ、技術書として通用するレベルの内容**を到達点として目指します。
ただし読者は入門段階から入るため、本書は「本当の基礎」から始めて段階的に難易度を上げる
構成をとる(詳細は「想定読者」「難易度の設計方針」を参照)。仕様書・カーネルソース
レベルの深掘りは各分野を理解し切るための最深層として位置づけ、いきなりそこから入らない。

このプロジェクトは `network-guide` (ネットワーク技術教科書) と対をなす、
完全に独立した分野の教科書です。ネットワーク教科書の第3部(Linuxネットワークスタック内部)は
両者の橋渡し章として、相互参照を意識して執筆します。

## 想定読者

- Linuxの基本的なコマンド(`ls` / `cd` / `cp` / `mv` / `rm` / `git` など)を
  一通り打てる程度。ファイル操作やディレクトリ移動はできるが、サーバーの構築・
  運用の経験はほとんどなく、OSやカーネルが「なぜそう動くのか」も知らない
- 「プロセス」「ファイルシステム」「systemd」といった用語は聞いたことがある
  程度で、体系的には理解していない。断片的な知識もほぼない前提で読み始められる
- この一冊を通して、まず「サーバーとは何か・Linuxがどう動くか」という入門から
  入り、最終的にサーバーの**基礎・運用・応用レベル**の知識を体系的に身につける
  ことを目指す。将来的にはエンタープライズ〜大規模インフラも視野に入れる

つまり「基本的なコマンドは打てるが、そこから先の体系的な一冊が欲しい」初学者を
想定する。**個人の実務経歴(具体的な組織・案件の話)は教科書の本文には含めない。**
一般的な読者が自分ごととして読める内容にする。

## 難易度の設計方針

本書は「本当の入門」から入り、章を追うごとに難易度を段階的に上げる。

- **入り口は前提知識ゼロで読める平易さを保つ**: 各分野・各章の冒頭は、基本コマンド
  しか知らない読者でも追える説明から始める。いきなりカーネル内部や仕様書レベルに
  飛び込まない
- **専門用語は初出時に必ずかみ砕く**: 用語を当然のものとして使わず、初出時に日常的な
  言葉で一度説明してから使う(定義は`shared/glossary.md`にも追記する)
- **難易度を飛ばさない**: 前の章で説明していない概念を前提にしない。読者が一段ずつ
  上れるよう、章・節の順序で難易度が連続するように配置する
- **到達目標はサーバーの基礎・運用・応用レベルの体系的理解**: カーネルソース・
  POSIX仕様レベルの深掘りは、理解を裏打ちする「発展」として扱い、それが理解の
  前提条件にならないようにする(発展部分は飛ばしても本筋が追えるようにする)

## 教科書の範囲(章立て大分類)

1. **プロセス・カーネル基礎** — プロセス/スレッド、システムコール、仮想メモリ、スケジューラ
2. **ファイルシステム・ストレージ** — VFS、ジャーナリング、inode、LVM/RAID、ネットワークストレージ
3. **Linuxネットワークスタック内部** — ソケットAPI、netfilter/nftables、ネットワークネームスペース、tc/qdisc
   (`network-guide` との橋渡し分野)
4. **仮想化・コンテナ基盤** — cgroups/namespaces、KVM/QEMU、コンテナランタイム
5. **systemd・サービス管理** — Unit依存関係解決、cgroup連携、journald
6. **高可用性・クラスタリング** — クラスタリングの基本概念、Pacemaker/Corosync、ロードバランシング/レプリケーション
7. **可観測性** — syslog、メトリクス/ログ/トレース、Prometheusのpull型設計思想

各分類は独立したディレクトリを持ち、その中で「触ったことはあるが原理は知らないレベル」→
「理論(カーネル文書・仕様書ベース)」→「応用」→「実装例」の順に章を分割する。
**いきなり応用理論に入らず、各分野の最初の章は必ず「そもそも何のためにある仕組みか」
「基本的な構造」から丁寧に立ち上げること。**

## ディレクトリ構造

```
server_textbook/
├── CLAUDE.md
├── 00_overview/
│   └── 01_roadmap.md                      # 教科書全体のロードマップ・前提知識マップ
├── 01_process_kernel/
│   ├── 01_process_thread_basics.md        # プロセス/スレッドの基本、PCB、fork/exec
│   ├── 02_syscall_context_switch.md       # システムコールとコンテキストスイッチ
│   ├── 03_virtual_memory.md               # 仮想メモリ、ページテーブル、TLB
│   ├── 04_scheduler.md                    # CFS等スケジューラの原理
│   └── 05_signals_ipc.md                  # シグナル、IPC概要
├── 02_filesystem_storage/
│   ├── 01_vfs_basics.md                   # VFS抽象化層
│   ├── 02_ext4_xfs_journaling.md          # ジャーナリングファイルシステム
│   ├── 03_inode_block_management.md       # inode、ブロック管理
│   ├── 04_lvm_raid.md                     # LVM、RAID
│   └── 05_network_storage.md              # NFS/iSCSI基礎
├── 03_linux_network_stack/
│   ├── 01_socket_api.md                   # ソケットAPI、システムコールからパケットまで
│   ├── 02_netfilter_nftables.md           # netfilter/nftablesのフック機構
│   ├── 03_network_namespaces.md           # ネットワークネームスペース
│   └── 04_qdisc_traffic_control.md        # tc/qdiscのパケットスケジューリング
├── 04_virtualization_containers/
│   ├── 01_cgroups_namespaces.md           # cgroups/namespacesの理論(コンテナの土台)
│   ├── 02_kvm_qemu.md                     # KVM/QEMUによるハードウェア仮想化
│   └── 03_container_runtime.md            # コンテナランタイムの仕組み(containerd等)
├── 05_systemd_service_mgmt/
│   ├── 01_init_systemd_overview.md        # initからsystemdへ
│   ├── 02_unit_dependency.md              # Unit定義と依存関係解決
│   └── 03_cgroup_integration_journald.md  # cgroup連携とjournald
├── 06_ha_clustering/
│   ├── 01_cluster_fundamentals.md         # クラスタリングの基本概念、split-brain
│   ├── 02_pacemaker_corosync.md           # Pacemaker/Corosyncの合意形成
│   └── 03_load_balancing_replication.md   # ロードバランシングとレプリケーション
├── 07_observability/
│   ├── 01_syslog_logging.md               # syslogとログ設計
│   ├── 02_metrics_logs_traces.md          # メトリクス/ログ/トレースの三本柱
│   └── 03_prometheus_pull_model.md        # Prometheusのpull型設計思想
├── shared/
│   ├── glossary.md                        # 全章共通の用語集(初出時にここへ追記)
│   └── style_guide.md                     # このCLAUDE.mdの執筆ルールの実例集
└── progress.md                             # 進捗管理ファイル(セッションをまたぐ引き継ぎ用)
```

## 実装ステップ

以下の順番でステップを1つずつ実行する。**1ステップ = 1ファイル**を基本単位とし、
1ステップ完了ごとに`progress.md`を更新する(詳細は次節「進捗管理ファイルの運用」を参照)。

| Step | 対象ファイル | 内容 |
|---|---|---|
| 0 | `shared/glossary.md`, `shared/style_guide.md` | 空のひな形を作成(以後随時追記) |
| 1 | `00_overview/01_roadmap.md` | 教科書全体のロードマップ・前提知識マップ |
| 2 | `01_process_kernel/01_process_thread_basics.md` | プロセス/スレッドの基本、PCB、fork/exec |
| 3 | `01_process_kernel/02_syscall_context_switch.md` | システムコールとコンテキストスイッチ |
| 4 | `01_process_kernel/03_virtual_memory.md` | 仮想メモリ、ページテーブル、TLB |
| 5 | `01_process_kernel/04_scheduler.md` | CFS等スケジューラの原理 |
| 6 | `01_process_kernel/05_signals_ipc.md` | シグナル、IPC概要 |
| 7 | `02_filesystem_storage/01_vfs_basics.md` | VFS抽象化層 |
| 8 | `02_filesystem_storage/02_ext4_xfs_journaling.md` | ジャーナリングファイルシステム |
| 9 | `02_filesystem_storage/03_inode_block_management.md` | inode、ブロック管理 |
| 10 | `02_filesystem_storage/04_lvm_raid.md` | LVM、RAID |
| 11 | `02_filesystem_storage/05_network_storage.md` | NFS/iSCSI基礎 |
| 12 | `03_linux_network_stack/01_socket_api.md` | ソケットAPI、システムコールからパケットまで |
| 13 | `03_linux_network_stack/02_netfilter_nftables.md` | netfilter/nftablesのフック機構 |
| 14 | `03_linux_network_stack/03_network_namespaces.md` | ネットワークネームスペース |
| 15 | `03_linux_network_stack/04_qdisc_traffic_control.md` | tc/qdiscのパケットスケジューリング |
| 16 | `04_virtualization_containers/01_cgroups_namespaces.md` | cgroups/namespacesの理論 |
| 17 | `04_virtualization_containers/02_kvm_qemu.md` | KVM/QEMUによるハードウェア仮想化 |
| 18 | `04_virtualization_containers/03_container_runtime.md` | コンテナランタイムの仕組み |
| 19 | `05_systemd_service_mgmt/01_init_systemd_overview.md` | initからsystemdへ |
| 20 | `05_systemd_service_mgmt/02_unit_dependency.md` | Unit定義と依存関係解決 |
| 21 | `05_systemd_service_mgmt/03_cgroup_integration_journald.md` | cgroup連携とjournald |
| 22 | `06_ha_clustering/01_cluster_fundamentals.md` | クラスタリングの基本概念、split-brain |
| 23 | `06_ha_clustering/02_pacemaker_corosync.md` | Pacemaker/Corosyncの合意形成 |
| 24 | `06_ha_clustering/03_load_balancing_replication.md` | ロードバランシングとレプリケーション |
| 25 | `07_observability/01_syslog_logging.md` | syslogとログ設計 |
| 26 | `07_observability/02_metrics_logs_traces.md` | メトリクス/ログ/トレースの三本柱 |
| 27 | `07_observability/03_prometheus_pull_model.md` | Prometheusのpull型設計思想 |

## 進捗管理ファイルの運用

セッション(会話)をまたいで作業を続けるため、`progress.md`を唯一の引き継ぎ情報源とする。

### セッション開始時のルール

- **必ず`progress.md`を最初に読み込んでから作業を再開する**
- あわせて`shared/glossary.md`にも目を通し、既存の用語定義と齟齬が出ないようにする
- `progress.md`に記載された「次のステップ」を確認し、そこから着手する
  (指示がない限り、途中のステップに勝手に飛ばない)

### 1ステップ完了時に`progress.md`へ追記する内容

```markdown
## Step N: <ファイルパス> (完了日: YYYY-MM-DD)

- 完了内容: このステップで何を書いたか、2〜3行で要約
- 決定事項: 章立て内での用語選択や構成上の判断で、以後の章にも影響するもの
- 未解決・要検証事項: 断定を避けた箇所、後で確認すべき点
- 次のステップ: Step N+1 <次のファイルパス> から着手する
```

- 追記は**上書きではなく追記**。過去のステップの記録は消さず、ログとして蓄積する
- ファイルが長くなりすぎた場合は、直近5ステップ分を残して古い記録を
  `shared/progress_archive.md`に移動してもよい(そのルールもここに追記する)

## 各章の執筆テンプレート

各章(.mdファイル)は以下の構成に統一する:

1. **概要** — この章で何を学ぶか、前提知識は何か(2〜3行)
2. **導入** — そもそも何のためにある仕組みか(初学者卒業直後でも追える説明)
3. **理論** — カーネルドキュメント・POSIX仕様・公式仕様書ベースの動作原理。
   「なぜこう設計されているか」を説明する
4. **内部動作の詳細** — データ構造、状態遷移、アルゴリズムなど。ここが本編の中心
5. **(補助) 実行例** — 動作原理の理解を助ける場合にのみ、必要最小限で載せる(任意項目)
6. **トラブルシューティング** — 挙動の観点から見た典型的な詰まりポイント
   (strace/ftrace/procファイル等での見え方など)
7. **演習・確認問題** — 理解度を確認する問題
8. **まとめ** — 章の要点を3〜5行

## 執筆スタイルのルール

- **各分野の最初の章は必ず基礎から**: 「そもそも何のための仕組みか」を説明せずに
  いきなり応用理論に入らない
- **主目的は「仕組みの理解」であり「操作方法」ではない**: このコンポーネント/機構が
  システム全体の中でどういう役割を持ち、どういう仕組みで動いているかを説明することが
  本編の中心。具体的なコマンド手順の網羅は基本的に載せない
- **実行例は仕組みの理解を助ける場合だけ、補助的に載せる**: 例えば「スケジューラの
  概念を説明した直後に、`/proc`以下の実際の値がどう見えるかを示すと理解が早い」
  というような、理論の裏付けとして機能する場合のみ
- **一次情報源を明記する**: 「Linuxカーネルのドキュメント(Documentation/以下)では〜」
  「POSIX(IEEE Std 1003.1)では〜と規定されている」のように出典を明確にする
  (このプロジェクトでは`network-guide`の「RFC番号を明記する」ルールに相当する)
- **個人の実務経歴や具体的な運用の逸話は書かない**: 教科書本文は一般読者にも
  通用する記述にする(実行例で特定ディストリビューションを使うのは可、体験談・組織名は不可)
- **前の章への参照を活用する**: 「プロセスの章で説明したページテーブルと
  組み合わせると〜」のように章をまたいだ関連づけを行う
- **`network-guide`との橋渡しを意識する**: 特に第3部(Linuxネットワークスタック内部)は、
  `network-guide`側のVXLAN/EVPN章などと相互参照できる箇所があれば明示する
- **用語は初出時にshared/glossary.mdに追記し、以後はそこでの定義に統一する**
- **図解が必要な箇所は、ASCIIアートまたはMermaid記法で表現する**
- **1章あたりの分量は無理に伸ばさない**: 内容が薄まるくらいなら、章を分割する
- **1章内の深度レンジを意識する**: 「導入」は初学者卒業直後が追える平易さ、
  「内部動作の詳細」は技術書レベル、と章内で深度を段階的に上げる。
  1章で扱う深さの上限を欲張らず、カーネルソースの1行1行を追うような深追いが
  必要になったら、その部分は別章に切り出すか「発展」として分離する
  (「1章あたりの分量は無理に伸ばさない」の具体化)

## 品質チェック基準

各章を書き終えたら、以下を確認する:

- [ ] 「触れるが原理は知らない」読者が導入部分でつまずかないか
- [ ] カーネルドキュメント・POSIX仕様等の一次情報源に基づいているか
      (記憶からの断片的な知識で埋めていないか)
- [ ] 前後の章と用語・表記が一貫しているか
- [ ] 「導入→理論→内部動作→実行例→トラブルシュート」の流れが崩れていないか
- [ ] 個人の実務経歴や特定案件の話が紛れ込んでいないか

## Git運用ルール

このプロジェクトは独立したGitHubリポジトリ(`meg4ne1251/server-textbook`, public)で管理する。
(`network-guide`とは対をなす姉妹プロジェクトだが、リポジトリは分離している)

- **ファイルを編集・作成するたびに、その都度GitHubへcommit & pushする**
  (複数ファイルの変更をまとめて溜め込まない。1ステップ完了 = 1コミットを基本とする)
- `progress.md`への追記も含めて、1ステップ分の変更(本文ファイル + progress.md + 必要なら
  glossary.md)を1つのコミットにまとめてpushしてよい
- pushは`origin main`に対して行う(force pushはしない)

## 前提バージョンの固定

本書は特定バージョンを基準に記述し、版差のある挙動を曖昧にしない。

- **カーネル**: Linux 7.x 系を基準とする(具体的な基準版は
  `00_overview/01_roadmap.md` に明記する)
- **ディストリビューション**: 実行例は基準ディストロ1つを定め、章冒頭で
  必要に応じて前提を書く
- **版差のある仕組みは必ず版を添える**: 例としてスケジューラは、CFS が
  Linux 6.6 で EEVDF に置き換わっている。本書は 7.x 基準なので、公平分配
  スケジューラのデフォルトは EEVDF として説明し、CFS は歴史的経緯・対比の
  文脈で扱う。旧実装を説明する場合は「6.6以前のCFS」のように版を添える
- 各章で前提とするカーネル/ツールのバージョンが基準と異なる場合は、
  章の「概要」に明記する

## 進め方の原則

- 章は一度に大量生成せず、「実装ステップ」の表に沿って1ステップ(1ファイル)ずつ進める
- セッション開始時は必ず`progress.md`と`shared/glossary.md`を読み込んでから着手する
  (「進捗管理ファイルの運用」参照)
- 1ステップ完了ごとに`progress.md`へ追記してからセッションを終える
- **1ステップ完了ごとに、変更をGitHubへcommit & pushする**(「Git運用ルール」参照)
- 技術的に自信がない箇所は、断定せず「要検証」の注記を残し、`progress.md`にも記録する
- 実装ステップの順番(00_overview → 01_process_kernel → 02〜07)が基本だが、
  各分野は独立して読めるようにも書く
