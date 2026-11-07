# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：data/glossary.jsonのhomework-levelsにある正しい回答が返ってきた。
- `--trace` で見えたツール呼び出し（ツール名と引数）：
search_glossary({'query': 'LoRA'})とsearch_glossary({'query': 'QLoRA'})の2回
- 1つの質問での LLM 呼び出し回数（`requests`）：2回
- 追加したツールと、docstring に書いた説明：
agent.tool_plain(tools.make_quiz)を追加
def make_quiz(lecture: int) -> str:
    """指定した回の用語から、学習確認用の問題を1問作る。

    「第N回の確認問題を出して」と頼まれたときに使う。
    """
    candidates = [
        entry for entry in load_glossary()
        if entry["lecture"] == lecture
    ]

    if not candidates:
        return f"第{lecture}回の用語が見つからないため、問題を作れません。"

    entry = random.choice(candidates)
    return json.dumps(
        {
            "question": f"「{entry['term']}」の意味は？",
            "answer": entry["definition"],
        },
        ensure_ascii=False,
    )

## 宿題

- 取り組んだレベル：基礎
- やったこと：agent.pyのINSTRUCTIONSの中で「回答の最後に、関係する回を示す」を「関係する回と確認問題を1問示す」に変更した。
- 結果：変更前はsearch_glossaryを2回呼び出す結果だったが、変更後はsearch_glossaryの2回に加え、確認問題を示すためにmake_quizも呼び出し、計3回となった。最終結果として、指示どおり「教師ありファインチューニング（SFT）の意味は？」という確認問題が示された。
- 気づき・疑問：指示が複雑になればなるほど、LLMモデルを呼び出す回数が増加する。
