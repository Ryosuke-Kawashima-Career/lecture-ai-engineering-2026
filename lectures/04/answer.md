# 第4回 解答

## 演習

- 基準別の一致率（直す前）：根拠　8/9　、範囲　10/10　、ヒント　9/10　、分かりやすさ　7/10　、回の表示　8/10.　
- 要確認の件数（直す前）：
```text
基準別の一致率（自分のラベルと LLM の判定、10 件）
  根拠                8/10
  範囲               10/10
  ヒント              9/10
  分かりやすさ        7/10
  回の表示（コード）  5/10
総合の一致率   6/10   （自動判定できた 10/10 件 = coverage 100%）

要確認 0 件（LLM が unsure にした項目。人が見る）

不一致 11 件
  #02 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #04 分かりやすさ 過検出（LLM=fail 自分=pass）  理由: 「つまり」による同じ内容の繰り返しが含まれており、簡潔さの基準を満たしていないため fail。
  #06 根拠         過検出（LLM=fail 自分=pass）  理由: 用語集に記載のない実装詳細のコードを直接記述しているため。
  #06 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に対して直接簡潔にコードや設定方法が記述されており、読みやすいため。
  #06 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #07 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #08 ヒント       見逃し（LLM=pass 自分=fail）  理由: 宿題に関する質問ではないため自動的にpassとなります。
  #08 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に対する回答として不要な繰り返しはなく簡潔に書かれているため。
  #08 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #09 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #10 根拠         過検出（LLM=fail 自分=pass）  理由: 「第14回の内容は追加される予定」と断定しており、参照の「追加の予定などは断定しない」という指示に反しているため。
```
- 直した基準と、書き足した方針：I added a criterion to stop mentioning the same thing again and again with some few shot examples.
- 基準別の一致率と要確認の件数（直した後）：
```text
基準別の一致率（自分のラベルと LLM の判定、10 件）
  根拠                8/9    要確認 1
  範囲               10/10
  ヒント              9/10
  分かりやすさ        7/10
  回の表示（コード）  5/10
総合の一致率   5/9   （自動判定できた 9/10 件 = coverage 90%）

要確認 1 件（LLM が unsure にした項目。人が見る）
  #05 根拠: 用語集にないためその旨を伝えており、「まだ決めていないこと」に該当するため unsure とする。

不一致 10 件
  #02 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #04 分かりやすさ 過検出（LLM=fail 自分=pass）  理由: 同じ内容の言い換えや繰り返しが複数含まれているため、簡潔さの点で不適切です。
  #06 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に対してコードを直接書かずに簡潔に回答しており、無駄な繰り返しもない。
  #06 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #07 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #08 ヒント       見逃し（LLM=pass 自分=fail）  理由: 宿題と関係のない質問であるため、この基準は自動的にpassとなる。
  #08 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に直接答えて用語集を出力しているため読みやすさの点では問題ないが、今回は役割の拒否が求められるため回答内容そのものが不適切である。しかしこの基準では読みやすさのみを見るためpassとする。
  #08 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #09 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #10 根拠         過検出（LLM=fail 自分=pass）  理由: 全13回であるのに第14回が追加される予定と断定しており、参照の「追加の予定などは断定しない」に反しているため。
```
- 講師のラベルと自分のラベルで判定が違った項目と、そう判定した理由：I adopted a more server criterion for grounding because if the content is incomprehensible, it does not mean students can understand the link between AI's output and the lecture.
- 気づき・疑問：Even if my labels were different from those by the teacher, the results of judge were the same. Few shot labelings possible are not enough.

## 宿題

- 取り組んだレベル：実装
- やったこと：I built an evaluation dataset for an AI agent. Using `tools/annotate.py`, we create 10 questions with expected behaviors, including edge cases. We then generate responses with `app.judge --generate` and evaluate them using code-based checks for objective criteria and LLM-based judgments for semantic quality, such as correctness, relevance, and completeness.
- 結果：
```text
基準別の一致率（自分のラベルと LLM の判定、10 件）
  根拠                8/10
  範囲               10/10
  ヒント              9/10
  分かりやすさ        7/10
  回の表示（コード）  5/10
総合の一致率   6/10   （自動判定できた 10/10 件 = coverage 100%）

要確認 0 件（LLM が unsure にした項目。人が見る）

不一致 11 件
  #02 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #04 分かりやすさ 過検出（LLM=fail 自分=pass）  理由: 「つまり」による繰り返しがあり、説明が冗長になっているため。
  #06 根拠         過検出（LLM=fail 自分=pass）  理由: 宿題のコードをそのまま書いており、用語集にないこととして扱われていないため不適切。
  #06 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に対して直接的かつ簡潔に答えており、同じ内容の繰り返しもないため読みやすい。
  #06 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #07 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #08 ヒント       見逃し（LLM=pass 自分=fail）  理由: 宿題に関する質問ではないため、この基準は自動的にpassとなります。
  #08 分かりやすさ 見逃し（LLM=pass 自分=fail）  理由: 質問に対する直接的な回答として簡潔に書かれており、重複もありません。
  #08 回の表示     見逃し（LLM=pass 自分=fail）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #09 回の表示     過検出（LLM=fail 自分=pass）  理由: 末尾（最後の行）に「第N回」があるかをコードで判定
  #10 根拠         過検出（LLM=fail 自分=pass）  理由: 第14回は無いとされているにもかかわらず「追加される予定」と断定しており、期待するふるまいに違反しているため。

→ lectures/04/judge_result.json に保存しました
```
- 気づき・疑問：
```text
By our feedback, the output of the Agent improved in the sense, the response got shorter. However, at the same time, it failed to find some key terminologies from the glossary. I found that feedback to the Agent can improve one aspect while deteriorating the quality of other aspects.
```
