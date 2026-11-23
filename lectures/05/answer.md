# 第5回 解答

## 演習

- `--list-tools` で見えたツール名と、第3回（`--mcp` なし）との違い：MCP では `glossary_search_glossary`、`glossary_get_lecture_summary`、`fetch_fetch` の3個が見えた。第3回の関数ツールは `search_glossary`、`get_lecture_summary`、`make_quiz`。MCP のツール名にはサーバー名の接頭辞が付き、設定に追加した `fetch` のツールも見える。
- `--mcp --trace` で見えたツール呼び出し（ツール名と引数）と、`requests` の回数：
[tool call]   glossary_search_glossary({'query': '3レベル'})　 requests=2
- MCP サーバーに公開したツールと、その説明文：glossary_get_lecture_summary
  指定した回（1始まりの整数）の講義の要点を返す。
  
  「第3回では何を学ぶ？」のように、回の内容や関係を聞かれたときに使う。
  引数: {"lecture": {"title": "Lecture", "type": "integer"}}
- fetch サーバーを足したときのツール呼び出し（ツール名と引数）：[tool call]   fetch_fetch({'url': 'https://modelcontextprotocol.io/'})

## 宿題

- 取り組んだレベル：基礎 
- やったこと：`glossary` の接続設定を stdio と Streamable HTTP で切り替え、`--list-tools` と同じ質問の `--trace` を試した。
- 結果：
  - HTTP 接続時の `--list-tools` では、`glossary_search_glossary`、`glossary_get_lecture_summary`、`fetch_fetch` の3個が表示された。
  - stdio 接続時の `--trace` では、`glossary_search_glossary({'query': '3レベル'})` が呼ばれた。ツール呼び出しは1回、`requests=2` だった。
  - stdio は設定ファイルの `command` と `args` を使い、エージェントが MCP サーバーを子プロセスとして起動する。HTTP はサーバーを別に起動し、`http://127.0.0.1:8000/mcp` に接続する。HTTP サーバー起動時にポート使用中のエラーが出たが、すでに別プロセスでサーバーが起動していたためだった。
  - docstring を変更した場合の `--list-tools` とツールの呼ばれ方の比較は、まだ未実施。
- 気づき・疑問：どちらの接続方法でも、モデルが使うツールとして表示され、質問に応じて用語集検索が呼び出された。一方、stdio はクライアントがサーバーを起動するのに対し、HTTP はサーバーを先に起動しておく必要があり、起動方法とポート管理が異なる。docstring の変更がモデルのツール選択に与える影響も確認したい。
