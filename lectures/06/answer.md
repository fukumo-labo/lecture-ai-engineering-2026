# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：glossary_search_glossary（第5回までのエージェントは search_materials）
- STEP 2 の [tool call]：glossary_search_glossary({'query': '3レベル'})
- STEP 3 の [tool call]：fetch_fetch({'url': 'https://modelcontextprotocol.io/'})
- STEP 4 の [tool call]：aws_aws___search_documentation({'search_phrase': 'Amazon S3 versioning overview'})
- 気づき・疑問：
  - 実行環境は手元の Mac（Codespaces ではない）。モデルは gemini-3.5-flash-lite
  - STEP 2：モデル応答 2 回（入力 709 / 出力 170 tokens）。1 回目でツールを呼び、2 回目で結果を読んで答えていた
  - STEP 3：fetch サーバーは最初に robots.txt を取りに行き、トップページの 307 リダイレクトを追って `.md` のページを取得していた。既定で robots.txt を守る動作がログで見えた
  - STEP 4：`aws` を足すと、`aws_aws___` で始まるツールが 5 個（read_documentation、search_documentation、list_regions、get_regional_availability、retrieve_skill）増えた。サーバー名 `aws` とツール名の `aws___` がつながって、名前が長くなる。質問の内容は AWS に送られる
  - 設定ファイルに数行足すだけでツールが増えるので、足す前に提供元とリスクを確かめる必要がある、というのを実感した

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎
- やったこと：`app/mcp_server.py` の TODO の下に `server.tool()(tools.get_lecture_summary)` を 1 行書き足した。`app/mcp_servers.json` と `app/mcp_agent.py`（エージェント側）は変えていない
- 結果：
  - `--list-tools` の一覧に `glossary_get_lecture_summary` が増えた
  - 「第2回では何を学びますか？」の `[tool call]`：glossary_get_lecture_summary({'lecture': 2})。モデル応答 2 回（入力 5,932 / 出力 240 tokens）
  - `uv run pytest -q`：21 passed
- 気づき・疑問：
  - エージェントは起動のたびにサーバーへツールの一覧を問い合わせるので、サーバー側に 1 行足すだけで、エージェント側を変えずに使えた
  - 引数 `lecture` には、モデルが質問の「第2回」から 2 を入れていた。関数の型ヒントと docstring が、そのままモデルへの説明になっている
  - 要点ツールが返した第2回の題は「ファインチューニングとLoRA」で、実際の第2回の講義資料の題「基盤モデルの仕組み」と違った。ツールが返すデータが古いと、モデルはそのまま答えてしまうので、データの更新も必要だと思った
