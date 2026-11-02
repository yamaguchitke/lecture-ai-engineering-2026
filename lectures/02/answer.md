# 第2回 解答

## 演習

- 使ったモデル・設定（`result.json` の `config` から）：
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 20,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 16,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }

- 自分で学習を実行したか（`executed_training_myself`）：はい
- 学習前：`format_rate` =0.09523809523809523　 / `content_rate` =0.0　
- 学習後：`format_rate` =1.0　 / `content_rate` =0.9047619047619048　
- 退行した質問：なし
- 学習後に残った失敗と、その型（根拠なしの質問に断定した / 根拠ありの質問に確認要求で逃げた / 形式の崩れ）：

[残った失敗]
15	成績はいつ出ますか	True	{"answer": "確認が必要"}	×/×	{"answer": "提出期限は 9 月 30 日 23:59（日本時間）です。", "needs_review": false}	○/×
20	オフィスアワーは事前予約が必要ですか	True	{"answer": "否", "needs_review": false}	○/×	{"answer": "質問は毎週火曜 17:00〜18:00 のオフィスアワーで受け付けます。", "needs_review": false}	○/×

[型]
根拠なしの質問に断定した

- 知識を問う質問（セクション 6）の結果（学習前の分類 → 学習後の分類）と気づいたこと：
  - 記載のある通り、確認が必要と、断定が出る(課題の提出期限を読んでいる)

- 学習で変わったこと・変わらなかったこと：
  - 出力形式はしっかりと変わり安定している
  - 「知らないことについて確認を求めるか、断定するか」といった判断までは保証されない(変わらない)

- 参考：config
{
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 20,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 16,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }
  },
  "versions": {
    "transformers": "5.18.0",
    "peft": "0.21.1",
    "trl": "1.14.2",
    "torch": "2.11.0+cu130"
  },
  "method": "LoRA（量子化なし）",
  "before": {
    "label": "before",
    "n": 21,
    "format_rate": 0.09523809523809523,
    "content_rate": 0.0,
    "both_rate": 0.0,
    "seconds": 24.8
  },
  "after": {
    "label": "after",
    "n": 21,
    "format_rate": 1.0,
    "content_rate": 0.9047619047619048,
    "both_rate": 0.9047619047619048,
    "seconds": 45.5
  },
  "regressed": [],
  "executed_training_myself": true
}

## 宿題

- 取り組んだレベル：基礎 / 実装 / 発展
　→基礎
- 変えた設定やデータ：
　→num_train_epochsを20→10に変更
- 結果（比較表や数字）：
  - 学習前：`format_rate` =0.09523809523809523　 / `content_rate` =0.0　
  - 学習後：`format_rate` =1.0　 / `content_rate` =0.9047619047619048　

{
  "config": {
    "base_model": "Qwen/Qwen3.5-0.8B",
    "revision": "2fc06364715b967f1860aea9cf38778875588b17",
    "seed": 42,
    "num_train_epochs": 10,
    "warmup_steps": 6,
    "lr": 0.0002,
    "lora_r": 16,
    "lora_alpha": 32,
    "max_seq_len": 512,
    "max_new_tokens": 96,
    "gen": {
      "do_sample": false
    }
  },
  "versions": {
    "transformers": "5.18.0",
    "peft": "0.21.1",
    "trl": "1.14.2",
    "torch": "2.11.0+cu130"
  },
  "method": "LoRA（量子化なし）",
  "before": {
    "label": "before",
    "n": 21,
    "format_rate": 0.09523809523809523,
    "content_rate": 0.0,
    "both_rate": 0.0,
    "seconds": 22.4
  },
  "after": {
    "label": "after",
    "n": 21,
    "format_rate": 1.0,
    "content_rate": 0.9047619047619048,
    "both_rate": 0.9047619047619048,
    "seconds": 43.6
  },
  "regressed": [],
  "executed_training_myself": true
}

- ファインチューニングが向く場面・向かない場面についての考察：
  - モデルに新しい知識を覚えさせる手段というより、既存の能力を特定の形式・手順・口調で安定して発揮させる手段
  - 向いている場面
    1. 出力形式を毎回そろえたい場合
    2. 同じ処理を大量に繰り返す場合
    3. 小さいモデルを特定用途に使いたい場合
    4. データを外部に出したくない場合
  - 向いていない場面
    1. 最新情報や社内文書など、新しい知識を追加したい場合
    2. 一度しか使わない処理や、利用量が少ない場合
    3. 学習データが少ない、または品質がばらばらな場合
    4. 正確な知識や判断を保証したい場合
  - まとめ
    - ファインチューニングが向くのは、知識の更新よりも、モデルの振る舞いを安定させることが目的の場合
    - 特に、出力形式、文体、分類基準、処理手順などが固定され、同じ処理を大量に繰り返す用途に適している
    - 一方、最新情報を扱う場合や、正確な知識を追加したい場合には向いていない。
    - また、学習データが不十分だと、元の能力を失ったり、学習データの誤りに過度に引きずられたりする危険がある

