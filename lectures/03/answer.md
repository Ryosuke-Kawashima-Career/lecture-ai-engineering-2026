# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：If there is no source available in the glossary, it returns "「JEV」および「decision model」について講座の用語集を確認しましたが、用語集には登録されていませんでした。"

- `--trace` で見えたツール呼び出し（ツール名と引数）：
```text
[get_lecture_summary]
  指定した回（1始まりの整数）の講義の要点を返す。
  
  「第3回では何を学ぶ？」のように、回の内容や関係を聞かれたときに使う。
  引数: {"lecture": {"type": "integer"}}

[make_quiz]
  Return quizes of the designated lecture (which starts with "1")
  This function is called when asked to make some questions to test users' understanding.
  引数: {"lecture": {"type": "integer"}}
```
- 1つの質問での LLM 呼び出し回数（`requests`）：2
```text
第2回の内容の理解度をチェックするための確認問題を作成しました！

**【確認問題】**
（準備中またはAPIから取得できませんでしたが、第2回の内容をもとに考えてみましょう！）

第2回で学んだ主要な概念について、ご自身で「他人に説明するならどう言うか」を考えてみると、より理解が深まります。

何か特定の用語や内容について復習したい場合は、いつでもお声がけくださいね。

関係する回：第2回

--- trace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> Make a quiz to test the user's understanding of the 2th lecture…
[usage] RunUsage(cost=Decimal('0.0005451'), details={'text_prompt_tokens': 817}, input_text_tokens=817, input_tokens=817, output_tokens=120, requests=2, tool_calls=1)
```
- 追加したツールと、docstring に書いた説明：
```text
[make_quiz]
  Return quizes of the designated lecture (which starts with "1")
  This function is called when asked to make some questions to test users' understanding.
  引数: {"lecture": {"type": "integer"}}
```

## 宿題

- 取り組んだレベル：発展
- やったこと：
- 結果：
- 気づき・疑問：
