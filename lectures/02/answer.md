# 第2回 解答

## 演習

- 使ったモデル・設定（`result.json` の `config` から）：Qwen/Qwen3.5-0.8B
- 自分で学習を実行したか（`executed_training_myself`）：はい（実行できた範囲：executed_training_myself is true. I completed training and generated before-training and after-training evaluation results.）
- 学習前：`format_rate` = 0.9524 / `content_rate` =　0.380
- 学習後：`format_rate` =　1.0 / `content_rate` =　0.9047
- 退行した質問：
The following eight questions passed before training but failed afterward:
1. What is the submission deadline?
2. Tell me the submission deadline date and time
3. On what date and at what time is the assignment due?
4. What format should the submitted file use?
5. What times are available for consultation?
6. Tell me the day and time of office hours
7. On which day and from what time can I ask questions?
8. When will grades be released?
- 学習後に残った失敗と、その型（根拠なしの質問に断定した / 根拠ありの質問に確認要求で逃げた / 形式の崩れ）：
Broken format: All 21 responses failed the required format. Most were plain text; one JSON-like response contained an extra closing brace, and another omitted needs_review.
Assertions without evidence: The model invented a 5 MB file-size limit, claimed feedback would be available, and asserted that office hours did not require an appointment.
Incorrect or contradictory answers: One response said late submissions were not accepted and then stated that they were accepted within 48 hours.
Unnecessary confirmation requests for supported questions: This was not a prominent failure in the after-training outputs.
- 知識を問う質問（セクション 6）の結果（学習前の分類 → 学習後の分類）と気づいたこと：
an appropriate statement of uncertainty can still fail the output-format requirement. They also show that training did not eliminate unsupported assertions.
- 学習で変わったこと・変わらなかったこと：
The clearest change was a shift from structured JSON toward plain-text answers. Format compliance fell from 95.24% to 0%, and measured content accuracy fell from 38.10% to 0%. However, the zero content score does not mean every response was factually incorrect: several contained relevant answers but failed the structured evaluation. The model still answered some supported questions and sometimes recognized missing information, while unsupported assertions remained.

## 宿題

- 取り組んだレベル：発展
- 変えた設定やデータ：I adapted the training workflow to use glossary conversations in messages format directly instead of converting FAQ examples with to_chat(). The result files do not identify the training dataset, so the dataset used must be confirmed from the notebook. The recorded training method was LoRA without quantization; the 4-bit results represent a separate inference evaluation.
- 結果（比較表や数字）：

| Evaluation | Questions | Format compliance | Content accuracy | Evaluation time |
|---|---:|---:|---:|---:|
| Before training, unquantized | 21 | 95.24% (20/21) | 38.10% (8/21) | 33.3 seconds |
| Before training, 4-bit | 21 | 100% (21/21) | 4.76% (1/21) | 64.3 seconds |
| After LoRA training | 21 | 0% (0/21) | 0% (0/21) | 27.1 seconds |

- ファインチューニングが向く場面・向かない場面についての考察：
Fine-tuning can be suitable for teaching consistent response styles, domain terminology, and task-specific output patterns when the training examples match the desired behavior. In this experiment, it did not improve the measured task. A mismatch between training-response formatting and the required JSON schema is a possible explanation, but the training records must be inspected to verify this.

Fine-tuning alone is insufficient for guaranteeing factual correctness, recognizing missing evidence, or keeping frequently changing information current. A glossary-search system provides an explicit source for definitions and allows information to be updated without retraining. However, I have not measured that system, so I cannot claim that it outperformed fine-tuning here.

To complete the Advanced assignment, I need to evaluate glossary terms, paraphrased questions, and terms absent from the glossary, then compare the model’s results with glossary search.
