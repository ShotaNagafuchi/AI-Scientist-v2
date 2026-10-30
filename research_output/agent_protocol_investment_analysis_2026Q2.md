# エージェントプロトコル層の投資・知的分析レポート

*調査日: 2026-04-10*
*調査範囲: MCP / A2A / ANP プロトコル + エージェントインフラ投資環境 + 歴史的プロトコル戦争パターン*

---

## Executive Summary

**結論: プロトコル戦争はすでに終わった。勝者は「MCP + A2A の二層共存」。**

- MCP (Anthropic) = エージェント→ツール接続層。97Mインストール、全主要AI企業対応済
- A2A (Google) = エージェント→エージェント通信層。150+組織参加、v1.0リリース済
- 両方とも Linux Foundation (AAIF) 配下で共同ガバナンス
- ANP (中国コミュニティ発) = 分散型発見層。学術的に面白いが投資対象外

**投資機会はプロトコル自体ではなく「その上のインフラ層」にある。** 歴史的にプロトコル層からの直接収益はゼロ（HTTP, TCP/IP, OAuth, K8s すべて同様）。価値はツーリング、プラットフォーム、マネージドサービスに蓄積する。

**最大の穴: エージェント・アイデンティティ / 監査可能性。** 全カテゴリ中最も資金不足（$37M）なのに規制圧力は最大（EU AI Act 2026年8月施行）。

---

## Part 1: プロトコル層の現状

### 1.1 MCP (Model Context Protocol)

| 項目 | 詳細 |
|------|------|
| **発表** | Anthropic, 2024年11月 |
| **月間DL数** | 97M (2026年3月時点, 16ヶ月で48倍成長) |
| **仕様** | JSON-RPC 2.0, stdio + Streamable HTTP, ツール/リソース/プロンプト/サンプリング |
| **ガバナンス** | AAIF (Linux Foundation) に寄贈。Platinum: AWS, Anthropic, Block, Bloomberg, Cloudflare, Google, Microsoft, OpenAI |
| **公開サーバ数** | 10,000+ |
| **クライアント** | Claude Desktop, ChatGPT, Cursor, Gemini, Copilot, VS Code, JetBrains |
| **本番採用企業** | Block, Bloomberg, Amazon, Fortune 500の80%+(エージェント全般) |

**技術的に何をカバーするか:**
- エージェントがツール/データベース/APIに接続するための標準インターフェース
- ツールの発見・呼び出し・結果受け取り
- リソース(ファイル, URI)のサブスクリプション
- サーバ→クライアントのサンプリング要求(LLM推論の逆要求)

**技術的に何をカバーしないか (公式に認めている):**
- エージェント間通信 (A2Aの領域)
- 認証・認可 (OAuth連携はあるが一級市民ではない)
- サーバ発見プロトコル
- サプライチェーンセキュリティ (署名・検証なし)
- 監視・監査の標準
- マルチエージェントオーケストレーション (Anthropicが "companion spec" をドラフト中)

**セキュリティの現実:**
- ツールポイズニング攻撃の成功率: 72.8% (MCPTox ベンチマーク, o1-mini対象)
- Rug pull攻撃: ユーザー承認後にツール説明を書き換え可能
- 実際の事例: Supabase Cursor エージェントがサポートチケット経由のプロンプトインジェクションで侵害 (2025年中頃)
- 仕様は "SHOULD" で安全策を推奨するのみ、"MUST" ではない

### 1.2 A2A (Agent2Agent Protocol)

| 項目 | 詳細 |
|------|------|
| **発表** | Google, 2025年4月 |
| **現バージョン** | v1.0 (2026年4月リリース) |
| **参加組織** | 150+ |
| **仕様** | JSON-RPC 2.0 over HTTPS + gRPC, Agent Card, Task lifecycle, Artifact, Push通知 |
| **ガバナンス** | LF AI & Data → AAIF (Linux Foundation) |
| **GitHub Stars** | 22K |

**核心的な技術要素:**
- **Agent Card**: `.well-known/agent.json` にエージェントが能力・認証方式・エンドポイントを公開。v1.0で署名付きカード対応
- **Task**: ステートフルな作業単位。submitted → working → input-required → completed/failed/canceled
- **Artifact**: タスクの出力物。v1.0でPart構造を統一(破壊的変更)
- **通信モード**: 同期(JSON-RPC POST), ストリーミング(SSE/gRPC), プッシュ通知(Webhook)

**MCP vs A2A の正確な違い:**

| 次元 | MCP | A2A |
|------|-----|-----|
| コア抽象 | エージェント→ツール (クライアント-サーバ) | エージェント→エージェント (P2P) |
| 相互作用 | 垂直: 1エージェントがツールに手を伸ばす | 水平: 自律エージェント間のタスク委任 |
| 状態モデル | ステートレスなツール呼び出し | ステートフルなTaskライフサイクル |
| 非同期 | 限定的 | ネイティブ (Webhook, ポーリング, ストリーミング) |
| 発見 | なし (レジストリ依存) | Agent Card (`/.well-known/agent.json`) |

**本当に本番で動いている例:**
- Tyson Foods + Gordon Food Service: サプライチェーン連携 (Google ADK + A2A + Vertex AI + Cloud Run)
- Microsoft Azure AI Foundry + Copilot Studio: 2026年4月GA、全対象顧客に提供
- Google Cloud: Agent Engine, Cloud Run, GKEでネイティブサポート

**注意: 150+組織の内訳**
- コンサル企業が大半 (Accenture, BCG, Deloitte, KPMG, PwC, TCS等) → A2Aインフラを運用しているのではなく、クライアント案件で使っている
- 実際にプラットフォームレベルで本番稼働: Google, Microsoft, 食品サプライチェーンパイロット

### 1.3 ACP → A2A 吸収の経緯

- 2025年3月: IBM が ACP (Agent Communication Protocol) を BeeAI プラットフォーム用に発表
- 2025年3月末: IBM が BeeAI + ACP を Linux Foundation に寄贈
- 2025年4月: Google が A2A を 50+ パートナーとともに発表
- **2025年8月29日: ACP が A2A に正式マージ**
- Kate Blair (IBM) が A2A Technical Steering Committee に参加
- IBMの判断: 勢力図を読み、エゴより実利を取った。ガバナンス上の発言権を確保

### 1.4 ANP (Agent Network Protocol)

| 項目 | 詳細 |
|------|------|
| **出自** | 中国・蘇州のオープンソースコミュニティ (葉周が主導) |
| **技術** | W3C DID (`did:wba`) ベースの分散型エージェント発見 |
| **協力者** | 中国移動, 中国電信, 中国聯通の研究者 |
| **成熟度** | 仕様段階、本番実装なし |

**MCP/A2A との位置づけ:**
- MCP/A2A は「管理されたエンタープライズ環境」向け。中央集権的信頼 (HTTPS証明書, OAuth)
- ANP は「オープンインターネット上の億単位のエージェント」向け。分散型ID、中央レジストリ不要
- 技術的には正しい問題を解いているが、西側企業での採用は地政学的に困難
- **最も可能性の高いシナリオ: A2A が DID 概念を吸収するか、ANP は中国リージョナル標準に留まる**

### 1.5 プロトコル層の全体構図

```
[ツール/DB/API]
      |
    [MCP]  ← 垂直: エージェント→ツール (Anthropic発, 事実上の標準)
      |
   [Agent]----[A2A]----[Agent]  ← 水平: エージェント間 (Google発, 標準化中)
      |                   |
    [MCP]               [MCP]
      |                   |
[ツール/DB/API]   [ツール/DB/API]
```

**上のどちらも Linux Foundation AAIF 配下。「戦争」ではなく「役割分担」。**

---

## Part 2: 歴史的プロトコル戦争から抽出した5つの法則

8つの歴史的事例 (HTTP/Gopher, TCP/IP/OSI, USB/FireWire, BT/WiFi/Zigbee, REST/SOAP/GraphQL, OAuth/OIDC, Kubernetes/Docker Swarm/Mesos, Linux Foundation の実績) を分析した結果:

### 法則1: 「動くコード」は「委員会仕様」に勝つ
TCP/IP > OSI, HTTP > Gopher, K8s > Mesos。実装の installed base が理論的優位性・政府命令・企業連合のすべてに勝つ。

### 法則2: ロイヤリティフリーは必ず勝つ
USB > FireWire ($1/port → 死), HTTP > Gopher (ミネソタ大学のライセンス料 → 死)。コモディティインフラでライセンス料を課した瞬間、開発者は逃げる。**最も信頼性の高い予測因子。**

### 法則3: シンプルさが採用段階で勝つ
REST > SOAP, TCP/IP > OSI。シンプルなプロトコルが先に普及し、後から複雑さを追加する (HTTP/1.0 は素朴 → HTTP/3 は精緻)。

### 法則4: 異なるレイヤのプロトコルは共存する。同一レイヤでは勝者総取り
BT/WiFi/Zigbee: 共存 (異なる電力/距離トレードオフ)。OAuth + OIDC: 共存 (認可 vs 認証)。一方、同一レイヤでは一強に収斂 (HTTP, TCP/IP, K8s)。

### 法則5: 中立ガバナンスが winner-take-all を加速する
K8s → CNCF, Linux → LF。中立財団は「競合の製品を採用する」恐怖を除去。ガバナンスが中立化した時点で、標準化の「戦争フェーズ」は終了する。

### 法則X (最重要): プロトコル層自体で金は稼げない

歴史上、プロトコル仕様の所有・ライセンスから持続的収益が生まれた事例は **ゼロ**。

| 金が生まれる層 | 例 | 規模 |
|---|---|---|
| マネージドサービス/プラットフォーム | AWS EKS, Okta, Auth0 | $1B〜$34B (Red Hat買収) |
| ツーリング/DX | Postman ($5.6B), Docker Desktop | $50M〜$6.5B |
| シリコン/ハードウェア実装 | Broadcom, Qualcomm (WiFi/BTチップ) | $10B+ |
| コンサル/インテグレーション | Accenture, Deloitte (K8s移行) | $100M+ |
| ディストリビューション/エンタープライズラッパー | Red Hat OpenShift | $1B+ |

**プロトコル創造者が最大の価値を獲得することは稀。** TCP/IP → DARPAではなくCisco。OAuth → WGではなくOkta。K8s → Googleではなく初期はRed Hat。

---

## Part 3: エージェントインフラ投資マップ (2026Q2)

### 3.1 カテゴリ別プレイヤーと資金

**エージェントメモリ ($43M調達済)**

| 企業 | ステージ | 調達額 | バリュエーション | 主要シグナル |
|------|---------|--------|----------------|-------------|
| Mem0 | Series A | $24M | 非公開 | 186M API calls/四半期, 41K GitHub stars, **AWS Agent SDK の排他的メモリプロバイダ** |
| Letta (旧MemGPT) | Seed | $10M | $70M | $1.4M revenue, UC Berkeley スピンアウト, Jeff Dean エンジェル |
| Cognee | Seed | EUR 7.5M | 非公開 | 500x パイプライン成長, 70+ 本番顧客 |
| Zep | Seed | $500K | 非公開 | YC W24, Graphiti 20K GitHub stars, SOC2/HIPAA |

**エージェントオーケストレーション ($465M調達済)**

| 企業 | ステージ | 調達額 | バリュエーション | 主要シグナル |
|------|---------|--------|----------------|-------------|
| LangChain / LangGraph | Series B | $260M | **$1.25B** | $16M revenue, 90M月間DL, 35% Fortune 500 |
| n8n | Series C | $180M | **$2.5B** | **$40M+ ARR**, 3K企業顧客, Nvidia出資 |
| CrewAI | Series A | $24.5M | $76M | $3.2M revenue, 45.9K GitHub stars |
| MS Agent Framework | Corporate | N/A | N/A | AutoGen + Semantic Kernel 統合, GA 2026Q1 |

**エージェント監視/評価 ($282M調達済)**

| 企業 | ステージ | 調達額 | 主要シグナル |
|------|---------|--------|-------------|
| Arize AI | Series C | $131M | ML可観測性のゴールドスタンダード |
| Braintrust | Series B | $80M | $800Mバリュエーション, 評価ファースト |
| Galileo AI | Series B | $68M | Luna-2 評価基盤モデル |
| LangSmith | LangChain一部 | 含$260M | LangChainエコシステムロックイン |
| Laminar | Seed | $3M | ブラウザセッション録画+トレース同期 |

**エージェントID/認証/信頼 ($37M調達済) ← 最も資金不足**

| 企業 | ステージ | 調達額 | 主要シグナル |
|------|---------|--------|-------------|
| t54 Labs | Seed | $5M | Ripple + Franklin Templeton 出資, "know your agent" |
| Alter | YC | 非公開 | パラメータレベル検証, リアルタイム監査 |
| Multifactor | Early | 非公開 | エージェントとのアカウント共有 |
| AIUC + Vijil | Early | $32M (合算) | エージェント監査・認証・保険 |

**エージェントガバナンス/コンプライアンス ($691M, 2022-2025)**

| 企業 | ステージ | 調達額 | 主要シグナル |
|------|---------|--------|-------------|
| Norm AI | Growth | $87M | 規制AIエージェント |
| nexos.ai | Series A | $43M | 欧州AIガバナンス |
| Geordie AI | Seed | $6.5M | RSAC 2026 Innovation Sandbox 優勝 |

**上場企業のエージェント事業**

| 企業 | 製品 | エージェント関連収益 |
|------|------|-----------------|
| Salesforce (CRM) | AgentForce | **$800M ARR**, 29K+ deals, 2B+ 自律アクション/月, SF史上最速成長 |
| ServiceNow (NOW) | Now Assist | **$600M+ ACV**, Moveworks買収 |
| Palantir (PLTR) | AIP | $1.18B Q3 revenue (+63% YoY), 米国商業+121% YoY |

### 3.2 マクロ数値

| 指標 | 数値 |
|------|------|
| 2025年 AI VC総額 | $211B (+85% YoY) |
| 2024年 AIエージェントスタートアップVC | $3.8B (前年3倍) |
| 2024-2025年 Big Tech acqui-hire | $40B+ |
| 2025年 AI M&A 開示額 | $146B+ |
| RSAC 2026 2週間のエージェントAIセキュリティ資金 | $392M |
| 2026Q1 全セクターVC | ~$300B (史上最高四半期) |
| エンタープライズ エージェントAI支出予測 2026 | $201.9B |
| パイロット率 vs 本番率 | **78% vs 14%** ← ギャップ = 機会 |
| Gartner認定「本物の」エージェント企業 | ~130 / 2,000+ マッピング済 |

---

## Part 4: 構造的に残っている4つの穴

MCP/A2A の上に乗る未解決のレイヤー。

### 穴1: エージェント間トランザクション / コミットメント意味論

**問題**: エージェントAがBに「$500以下でこれ買って」と頼んだ時の契約性が未定義。失敗時の補償、途中取消、部分的成功の意味論がない。

**現状**: A2A の Task lifecycle は submitted/working/completed/failed だが、「複数エージェントにまたがるトランザクションの原子性」は仕様外。

**歴史的類似**: SQL の ACID (Gray 1981) がアプリ層から整合性保証をDB層に移譲したのと同じ構図。マイクロサービスの Saga パターンも参考。

**社会実装見通し**: A2A extension として 2-3年で MVP、5-7年で標準化。ただし「LLM能力向上でマルチエージェント自体が不要になる」リスクが最大の地雷。

**確率評価**: パラダイムシフトになる確率 15-25%。リターンは巨大だが不確実。

### 穴2: エージェント・アイデンティティと監査可能性 ★最重要

**問題**: 5つのエージェントが連鎖して株を買った。「誰が承認した?」が答えられない。

**現状**: カテゴリ全体で **$37M しか資金が入っていない**（メモリの $43M、オーケストレーションの $465M と比較）。IETF の Agent Name Service (ANS) はまだ提案段階。Series A を達成した企業がゼロ。

**規制圧力**: EU AI Act 2026年8月施行 → 監査証跡が義務化される。米国もAI executive orders で accountability を要求。

**歴史的類似**: SSL/TLS 証明書。1998年時点では「面倒」「遅くなる」と嫌われたが、規制と事故が普及を強制した。

**社会実装見通し**: 規制駆動で **3-5年で金融/医療で必須化**。技術部品 (署名, Merkle, 因果ログ) は枯れている。

**確率評価**: 社会実装される確率 60-75%。地味だが確実。

### 穴3: エージェント間状態整合性 / 共有メモリ

**問題**: 並列エージェントが同じ事実の異なるバージョンで動く。

**現状**: Mem0, Letta, Cognee, Zep が商業競争中。AWS が Mem0 を排他的パートナーに選定。論文より製品の戦い。

**社会実装見通し**: 1-2年で勝者収束。Mem0 が現時点でリード (186M API calls/四半期, AWS 提携)。

### 穴4: 検証可能なエージェント推論 (zkML 応用)

**問題**: エージェントの出力を「なぜそう判断したか」を検証可能な形で返す仕組みがない。

**現状**: zkML は学術的に存在するが証明コストが 1000-10000倍。高ステークス領域 (医療診断, 法的助言) で限定実装。

**社会実装見通し**: 7-12年。証明コスト低下が律速。

---

## Part 5: 投資テーゼ (4つのオプション)

### テーゼ1: 「エージェントIDは新しいSSL」— ID/認証インフラに賭ける

**Bull**: 全自律エージェントにID・認証情報・監査証跡が必要。カテゴリ全体で$37M、数千億ドル規模のエージェント支出に対して桁違いに過少。EU AI Act が追い風。先行者はエージェント版 Verisign/Let's Encrypt になりうる。

**Bear**: AWS IAM / Azure AD / Google Cloud IAP が既存IDをエージェントに拡張するだけで済む可能性。単独カテゴリとして成立せずオーケストレーション層の一機能に吸収されうる。実証済み収益モデルなし。

**対象**: t54 Labs (seed), YC S26/W27 の新参者を注視。

### テーゼ2: 「ピック&ショベル」— 監視/評価プラットフォームに賭ける

**Bull**: 可観測性はあらゆるインフラ波の勝ちパターン (Datadog for cloud)。78%パイロット→14%本番のギャップ = 監視ツールなしにスケール不可能。Braintrust ($800M val) が機関投資家の確信を示す。

**Bear**: Datadog, Splunk (Cisco) が拡張参入。LangSmith がLangChainエコシステムでロックイン。コモディティ化リスク。

**対象**: Braintrust (Series B, 成長ステージ), Laminar ($3M seed, ハイリスク/ハイリターン)。

### テーゼ3: 「メモリ層」— エージェントメモリをコアインフラとして賭ける

**Bull**: メモリはエージェントにとってのデータベース。Mem0 が爆発的成長 (186M API calls/四半期, 30% MoM)。AWS 排他提携 = 巨大配信モート。カテゴリまだ $43M。

**Bear**: ベクトルDB各社 (Pinecone, Weaviate, Chroma) が「メモリ」機能を追加中。LLMプロバイダ自身がモデル内メモリを構築中 (ChatGPT memory, Claude memory)。収益化未証明 (Letta $1.4M revenue)。

**対象**: Mem0 (Series A, カテゴリリーダー), Cognee (Seed, 欧州, EU規制追い風)。

### テーゼ4: 「上場企業プラットフォーム収斂」— パブリックマーケットで賭ける

**Bull**: Salesforce AgentForce が初年度 $800M ARR — SF史上最速成長製品。ServiceNow + Moveworks でIT Service Management × エージェント。Palantir AIP 米国商業 +121%。配信力、ブランド信頼、企業関係でスタートアップに勝つ。

**Bear**: 「シート数危機」— AIエージェントが人間のシートを置換 → 従来のper-seat SaaS収益を食う。2026年2月のソフトウェアセルオフはまさにこの恐怖が原因。バリュエーション (Palantir 50x+ revenue) が完璧を織り込み済。

**対象**: Salesforce (CRM) = 実証済エージェント収益化, Palantir (PLTR) = 高成長だがバリュエーション懸念, ServiceNow (NOW) = 最もディフェンシブ。

### テーゼ横断比較

| テーゼ | リスク | 期待リターン | 時間軸 | 確信度 |
|--------|--------|------------|--------|--------|
| 1. エージェントID | 高 (カテゴリ消滅リスク) | 50-100x (seed) | 5-8年 | ★★★ |
| 2. 監視/評価 | 中 (大手参入) | 5-15x (growth) | 3-5年 | ★★★★ |
| 3. メモリ層 | 中 (吸収リスク) | 10-30x (Series A) | 3-5年 | ★★★★ |
| 4. 上場企業 | 低 (多角化) | 2-5x | 1-3年 | ★★★★★ |

---

## Part 6: 歴史パターンから見た MCP/A2A の今後

### タイムライン予測

| フェーズ | 歴史的類似 | 予測タイムライン |
|---------|-----------|----------------|
| 標準乱立 (MCP vs A2A vs ANP vs ACP) | TCP/IP vs OSI 初期 | 2024-2025 (済) |
| 中立ガバナンスへの統合 | K8s → CNCF 寄贈 | 2025年12月 (済, AAIF設立) |
| デファクト標準の確立/共存安定化 | K8s が2018年に勝利 (~4年) | 2027年中頃 (~2.5年) |
| 成熟したツーリングエコシステム | K8s ソリューション市場 2020+ | 2028-2029 |
| レイヤごとの勝者総取り | REST支配 ~2012 | 2029-2030 |

**AI プロトコル統合は歴史上最速で進行中。** MCP は16ヶ月で 97M インストール — K8s の約4倍の速度。理由:
1. 主要プレイヤーが戦争前にAAIFに参加
2. 開発者コミュニティが過去の時代より巨大
3. AIエージェントツーリングに即時の収益ポテンシャルがある

### 戦略的含意

1. **MCP か A2A に賭けるのはローリスク** — 同じガバナンス傘の下、同じ支援者。1995年の「インターネット」に賭けるのと同じ。
2. **高価値の賭けはプロトコルの上のツーリング/プラットフォーム** — Okta/Auth0 の再現 (オープン標準の上にサービスを構築)。
3. **独自エージェントプロトコルを構築する企業は負ける** — OAuth/OIDC に独自SSO が負けたのと同じ。
4. **ANP は投資対象外** — AAIF統合がガバナンス問題を決着済、歴史的に後発は18-24ヶ月で無関係化。

---

## Part 7: FIPA ACL の教訓 — 同じ失敗を繰り返さないために

1990年代に FIPA ACL という先行者があり、Speech Act 理論ベースでエージェント間通信を標準化しようとして失敗した。

**失敗理由:**
1. 意味論をエージェントの "mental state" で定義 → 実装検証不可能
2. 方言が乱立 → 異なる実装間で相互運用せず
3. 失敗時の意味論が場当たり的

**教訓:**
- Semantic を mental state ではなく **observable behavior + commitment** で定義すべき
- これは分散システムが学んだ教訓と同一 (Lamport 時計, Paxos, ブロックチェーン)
- 「プロセスが何を信じているか」ではなく「プロセスが外部に何を約束し、その約束が破られたか観測可能か」

**現在の MCP/A2A はこの罠を回避している**: 両方とも pragmatic な JSON-RPC で、「意味論の完全な形式化」を避けている。FIPA の過剰設計とは対照的。

---

## Part 8: 知的好奇心のための深層分析

### 「世界を再定義する」ポテンシャルの評価

候補Aの「エージェント合意プロトコル」を最初に提案した時点から、調査で判明した事実を踏まえて修正:

**修正前の仮説**: 「TCP/IP 前夜だ、新しいプロトコルを発明すれば世界を変えられる」
**修正後の現実**: 「TCP/IP は既に敷かれた (MCP/A2A)。SSL/TLS, ACID, OAuth に相当するものがまだ無い」

つまり、**「世界を再定義する」機会は、新しいトランスポート層ではなく、その上の信頼・整合性・アカウンタビリティ層にある。**

これは SQL → Bitcoin → Transformer の系譜とは異質で、むしろ:
- TCP/IP (1974) → SSL/TLS (1995) → ACID → OAuth/OIDC (2012)
- 「通信の発明」→「通信の上の信頼の発明」→「信頼の上の権限委譲の発明」

という **「レイヤの上昇」** パターンに位置する。

**最終的な評価**: SQL/Blockchain 級の「全く新しい概念」が出る場所としては、エージェントプロトコル自体よりも:
1. **「エージェント間の約束 (commitment)」の形式的定義** — ACID がDBに与えたものをエージェントに与える
2. **「エージェントのアカウンタビリティの暗号学的証明」** — PKI が通信に与えたものをエージェント行動に与える

の2つが最も「パラダイムシフト」に近い。ただし、どちらも TCP/IP や SQL ほどの「全くの新概念」ではなく、既存概念の新文脈への適用。

**「本当に新しい概念が生まれる」としたら**、それはおそらく「確率的エンティティ同士の合意とは何か」という問いへの答え。従来の分散合意 (Paxos, Raft) は決定的なプロセスを仮定している。LLM エージェントは本質的に確率的。ここに「何か」が生まれる可能性はあるが、まだ形が見えない。

---

## 参考ソース一覧

### プロトコル仕様
- [MCP Specification 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25)
- [A2A Protocol Specification v1.0](https://a2a-protocol.org/latest/specification/)
- [ANP Technical White Paper (arXiv)](https://arxiv.org/abs/2508.00007)
- [AP2 Payment Protocol Specification](https://ap2-protocol.org/specification/)

### ガバナンス
- [Anthropic: Donating MCP to AAIF](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [Linux Foundation AAIF Announcement](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
- [ACP Joins Forces with A2A (LF AI & Data)](https://lfaidata.foundation/communityblog/2025/08/29/acp-joins-forces-with-a2a-under-the-linux-foundations-lf-ai-data/)

### 採用・市場データ
- [MCP 97M Installs (AI Unfiltered)](https://www.arturmarkus.com/anthropics-model-context-protocol-hits-97-million-installs-on-march-25-mcp-transitions-from-experimental-to-foundation-layer-for-agentic-ai/)
- [A2A 150+ Organizations (PR Newswire)](https://www.prnewswire.com/news-releases/a2a-protocol-surpasses-150-organizations-lands-in-major-cloud-platforms-and-sees-enterprise-production-use-in-first-year-302737641.html)
- [Why MCP Won (The New Stack)](https://thenewstack.io/why-the-model-context-protocol-won/)

### スタートアップ・資金調達
- [Mem0 $24M Series A (TechCrunch)](https://techcrunch.com/2025/10/28/mem0-raises-24m-from-yc-peak-xv-and-basis-set-to-build-the-memory-layer-for-ai-apps/)
- [LangChain $1.25B (TechCrunch)](https://techcrunch.com/2025/10/21/open-source-agentic-startup-langchain-hits-1-25b-valuation/)
- [n8n $2.5B (TechFundingNews)](https://techfundingnews.com/n8n-raises-180m-series-c-2-5-billion-valuation-automation-ai/)
- [Braintrust $800M (SiliconANGLE)](https://siliconangle.com/2026/02/17/braintrust-lands-80m-series-b-funding-round-become-observability-layer-ai/)
- [Runlayer $11M (TechCrunch)](https://techcrunch.com/2025/11/17/mcp-ai-agent-security-startup-runlayer-launches-with-8-unicorns-11m-from-khoslas-keith-rabois-and-felicis/)
- [Composio $29M (SiliconANGLE)](https://siliconangle.com/2025/07/22/composio-raises-25m-funding-ease-ai-agent-development/)
- [Cognee EUR 7.5M (EU-Startups)](https://www.eu-startups.com/2026/02/german-ai-infrastructure-startup-cognee-lands-e7-5-million-to-scale-enterprise-grade-memory-technology/)
- [t54 Labs $5M (TheBlock)](https://www.theblock.co/post/391273/ripple-franklin-templeton-ai-agent-trust-startup-t54-labs)
- [Norm AI $48M (SiliconANGLE)](https://siliconangle.com/2025/03/11/ai-agent-powered-compliance-automation-startup-norm-ai-raises-48m/)

### 上場企業
- [Salesforce AgentForce $800M ARR](https://investor.salesforce.com/news/news-details/2025/Salesforce-Delivers-Record-Third-Quarter-Fiscal-2026-Results-Driven-by-Agentforce--Data-360/default.aspx)
- [Palantir Q3 2025](https://www.financialcontent.com/article/finterra-2026-1-14-palantir-pltr-2026-deep-dive-the-rise-of-the-agentic-ai-powerhouse)
- [ServiceNow $600M+ ACV](https://futurumgroup.com/insights/servicenow-q3-fy-2026-ai-agents-data-360-lift-bookings-and-fy26-outlook/)

### セキュリティ
- [MCP Tool Poisoning 72.8% (MCPTox)](https://zenity.io/blog/security/securing-the-model-context-protocol-mcp)
- [Supabase Cursor Incident](https://auth0.com/blog/mcp-specs-update-all-about-auth/)
- [RSAC 2026 $392M Agentic AI Security](https://www.startupdefense.io/blog/agentic-ai-security-rsac-2026-innovation-sandbox)

### 歴史分析
- [Gopher vs HTTP](https://www.minnpost.com/business/2016/08/rise-and-fall-gopher-protocol/)
- [OSI vs TCP/IP (IEEE Spectrum)](https://spectrum.ieee.org/osi-the-internet-that-wasnt)
- [FireWire Licensing](https://apple.slashdot.org/story/99/01/15/0937252/apple-to-charge-licensing-fees-for-firewire)
- [Docker Enterprise Sold to Mirantis](https://www.techtarget.com/searchitoperations/news/252473923/Docker-Enterprise-spun-off-to-Mirantis-company-restructures)
- [D2iQ (Mesosphere) Shutdown](https://techcrunch.com/2019/08/05/mesosphere-changes-name-to-d2iq-shifts-focus-to-kubernetes-cloud-native/)
- [Okta Revenue](https://finimize.com/content/okta-asset-snapshot)
- [$211B VC into AI in 2025 (Crunchbase)](https://news.crunchbase.com/venture/funding-data-third-largest-year-2025/)
