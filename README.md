# Safety Prompt Generation

LLMを使うシステムの安全性を評価するプロンプトを生成するJupyterノートブック

## 概要

このノートブックは、LLM（Large Language Model）を使用したシステムの安全性を評価するためのテストケース生成を自動化するツールです。3段階のステップで以下の処理を行います：

1. **Step1**: 対象システムの安全性観点を特定
2. **Step2**: 出力されるべきでない情報を特定
3. **Step3**: テストケース生成とExcel出力

現在、銀行チャットボット（`bank`）と社内情報検索システム（`hr`）に対応しています。

## 利用環境

- Windows 11 PC
- Amazon Web Service（AWS Bedrock）
- Python
- JupyterLab

## 事前準備

### Pythonのインストール

インストールされていない場合は、以下の公式サイトからダウンロードしてインストールしてください。
- [Python公式サイト](https://www.python.org/downloads/)

### AWS CLIのインストール

AWS CLIを使用してAWSクレデンシャルを設定する場合は、以下の公式ドキュメントを参照してインストールしてください。
- [AWS CLI インストールガイド](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### AWSアクセスキーの取得

1. AWSコンソールにログイン
2. IAM（Identity and Access Management）にアクセス
3. 新しいユーザーを作成し、プログラムアクセス権限を付与
4. AWS Bedrockへのアクセス権限を持つポリシーをアタッチ
5. アクセスキーIDとシークレットアクセスキーを取得・保管

### AWS Bedrockの有効化

1. AWSコンソールでAWS Bedrockサービスにアクセス
2. 使用するリージョン（ap-northeast-1）を選択
3. Claude Sonnet 4モデルへのアクセス権限を有効化
4. 必要に応じてモデルのアクセス許可を設定

### Python環境の構築

1. 仮想環境を作成（推奨）：
   ```bash
   python -m venv .venv
   .venv\Scripts\activate  # Windows
   ```

2. 必要なパッケージをインストール：
   ```bash
   pip install -r requirements.txt
   ```

3. AWSクレデンシャルの設定：
   - AWS CLIを使用する場合：
     ```bash
     aws configure --profile safety_prompt
     ```
   - 手動で設定する場合：`~/.aws/credentials`に以下を追加
     ```
     [safety_prompt]
     aws_access_key_id = YOUR_ACCESS_KEY
     aws_secret_access_key = YOUR_SECRET_KEY
     region = ap-northeast-1
     ```

## 使用方法

### JupyterLabの起動

1. 仮想環境をアクティベート：
   ```bash
   .venv\Scripts\activate
   ```

2. JupyterLabを起動：
   ```bash
   jupyter lab
   ```

3. ブラウザで自動的に開くJupyterLab画面で、ノートブックファイル(generate_safety_prompt.ipynb)を開く

### ノートブックの実行

1. **対象システムの設定**：
   - `target = "bank"`または`target = "hr"`を設定
   - 対応するプロンプトファイルが`prompts/`フォルダに存在することを確認

2. **セルの順次実行**：
   - Shift+Enterで各セルを順番に実行
   - 各ステップの実行結果を確認しながら進める

3. **手動チェックポイント**：
   - Step1実行後：抽出された観点の確認・修正
   - Step2実行後：出力されるべきでない情報の確認・修正（必要に応じて）

4. **結果の出力**：
   - 最終的に`test_cases.xlsx`ファイルが生成される
   - Excelファイルには以下の列が含まれる：
     - `viewpoint`: 安全性観点
     - `target`: 対象システム
     - `note`: 説明
     - `point`: テストポイント
     - `operation`: 操作内容
     - `expected`: 期待結果
     - `prompt`: テスト用プロンプト

## プロンプトファイルの構成

ノートブックは以下のプロンプトファイルを使用します：

- `prompts/step1_prompt_bank` または `prompts/step1_prompt_hr`
- `prompts/step2_prompt_bank` または `prompts/step2_prompt_hr`
- `prompts/step3_prompt_bank` または `prompts/step3_prompt_hr`

これらのファイルは事前に準備されている必要があります。

## 注意事項

- AWS Bedrockの使用には料金が発生します（1回あたり数百円程度）
- 生成されるテストケースの数は、特定される安全性観点の数に依存します
- 大量のテストケース生成時は、APIの使用制限に注意してください
- 生成されたテストケースは、実際の使用前に内容を確認することを推奨します