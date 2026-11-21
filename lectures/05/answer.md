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

- 取り組んだレベル：発展
- やったこと（変更したファイル・設定・処理、または設計した取得方法）： Implemented `select_retrieval_method()` in `app/retrieval.py` and added `--route` to `app/agent.py`. Document/index navigation terms select Tree; terminology comparisons and relationships select Graph; other questions use vector search. Tree matching has priority when a query matches both groups. The routing is a deterministic keyword heuristic, not an LLM classifier.
- 比較した質問と、固定した条件（モデル・対象資料など）： "What is the difference between RAG and finetuning?" is the target question. The model and the target resource were fixed.
- 結果（変更前後の回答・根拠、`--trace`の出力、時間・トークン数・生成API推定費用など）： The router selects Graph for the comparison question (`[retrieval] graph`); it uses the existing `search_graph` tool and graph instructions. The earlier vector and Graph measurements in the table above are the baseline runs, not a separately measured `--route` run. I did not rerun the live Gemini comparison, so routed answer/citations and time, token, and cost measurements remain unverified. The routing tests passed (12 focused tests total).

  Run the following in one terminal to capture the comparable live results and check all three routes:

  ```bash
  AIE_QUESTION='What is the difference between RAG and finetuning?'
  uv run python -m app.agent --trace "$AIE_QUESTION"
  uv run python -m app.agent --route --trace "$AIE_QUESTION"
  uv run python -m app.agent --route --trace 'Which documents and indexes cover RAG?'
  uv run python -m app.agent --route --trace 'How does LoRA work?'
  uv run --frozen pytest -q tests/test_tools.py tests/test_agent.py
  ```

  Record the selected route, actual answer and cited pages, `[tool call]` arguments/count, `[time]`, `[usage]` input/output tokens and response count, and `[cost]`. For the target question the routed run should select Graph; compare it with the default vector run above under the same model and document scope.
- 改善した例と、残る失敗・制約： The RAG-versus-finetuning question is routed to Graph, which is intended for relationships between terms. An explicit document/index lookup is routed to Tree, and a general explanation falls back to vector search. Keyword matching can misclassify ambiguous wording or a question that mentions a document without asking to navigate the materials; Graph can also add related pages that do not contain the answer.
- 気づき・疑問： Routing makes the retrieval method follow the apparent question intent without requiring a separate command-line choice. It does not itself improve answer quality: the routing rules and retrieved evidence still need to be checked with `--trace`, and the live routed comparison remains to be measured.
