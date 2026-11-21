# 第5回 解答

## 演習

- 回答モデル（実行時の`[model]`）：gemini-3.5-flash-lite
- 比較した質問：What is the difference between RAG and finetuning?
- 対象資料（全文投入と他の方式で範囲が違う点も記録）：基盤モデルの仕組み（PDF p.20, p.46-48）, 基盤モデルとデータ（PDF p.9-10, p.14, p.17）

### 各方式の結果

| 方式 | 回答の要点・正しさ | 引用ページと根拠 | 時間（秒） | 入力／出力トークン数 | モデル応答回数 | 生成API推定費用（USD） |
|---|---|---|---|---|---|---|
| 全文投入 | The contents of output and source of references are correct. | 基盤モデルの仕組み（PDF p.20, p.46-48）, 基盤モデルとデータ（PDF p.9-10, p.14, p.17） |回答完了まで 4.02 秒（検索・埋め込みを含む）| 入力 117,012 tokens / 出力 527 tokens| モデル応答 1 回  | 生成APIの推定コスト: $0.036421 USD（埋め込み・索引構築分は含まない） |
| ベクトル検索 | the retrieved chuncks were more relevant to RAG and finetuning than the proposed sources in the full context run. | 第2回・PDF p.46, p.47 | 回答完了まで 3.88 秒（検索・埋め込みを含む） | 入力 2,732 tokens / 出力 215 tokens | モデル応答 2 回 | 生成APIの推定コスト: $0.001357 USD（埋め込み・索引構築分は含まない） |
| Tree | Compared to the results of the vector search, the number of retrieved sources is larger and hierarchically structured. The output was more detailed than the full context and vector search system's. | 第2回・PDF p.46, 47 | 回答完了まで 7.39 秒（検索・埋め込みを含む） | 入力 13,086 tokens / 出力 584 tokens | モデル応答 6 回 | 生成APIの推定コスト: $0.005386 USD（埋め込み・索引構築分は含まない） |
| Graph | The differences of RAG and Finetuning were explained in a few sentences. The content of the retrieved source was relevant to the query. | 第2回・PDF p.46, 48, 49 | 回答完了まで 3.51 秒（検索・埋め込みを含む） | 入力 5,100 tokens / 出力 376 tokens | モデル応答 2 回 | 成APIの推定コスト: $0.002470 USD（埋め込み・索引構築分は含まない） |

<!-- 未実行・算定不可の場合は、その旨を書いてください。質問を追加した場合は、質問ごとに表を分けてください。 -->

- 検索だけを実行して見つかったページと、その本文で確認できたこと： The four methods fetched the same pages but the retrived parts were different. The fullcontext and tree retried more minute parts than the other two.
- `--trace`で見えたツール呼び出し（ツール名・引数・回数、Treeでたどった目次など）： The tree method called more than three to six times as much as the other three.
- 取得した情報が足りなかった例、取得できたのに回答へ使えなかった例： All of the methods succeeded in intergrating the fetched chuncks. This might be partially reinforced by the evolution of fundation models.
- 今回の用途に採用したい方式と、その理由・比較条件の限界： The vector search system would be balanced considering the number of tool calls and input tokens. Since all of the methods returned the answers correctly, it is difficult to distinguish favorable methods from incorrect ones.
- 気づき・疑問： Since the quality of the foundation models is drastically increasing, the importance of context engineering is shifting toward to less cost and response time.

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎 / 実装 / 発展
- やったこと（変更したファイル・設定・処理、または設計した取得方法）：
- 比較した質問と、固定した条件（モデル・対象資料など）：
- 結果（変更前後の回答・根拠、`--trace`の出力、時間・トークン数・生成API推定費用など）：
- 改善した例と、残る失敗・制約：
- 気づき・疑問：
