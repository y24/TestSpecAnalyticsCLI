# TestStat-CLI

```
  _____         _   ____  _        _      ____ _     ___
 |_   _|__  ___| |_/ ___|| |_ __ _| |_   / ___| |   |_ _|
   | |/ _ \/ __| __\___ \| __/ _` | __| | |   | |    | |
   | |  __/\__ \ |_ ___) | || (_| | |_  | |___| |___ | |
   |_|\___||___/\__|____/ \__\__,_|\__|  \____|_____|___|
```

Excelテスト仕様書からテスト結果を集計するCLIツールです。

## 概要

このツールは、Excelファイル（.xlsx）に記録されたテスト結果を読み取り、以下の集計を行います：

- テストケース数の集計
- テスト結果別（Pass/Fail/Blocked...）の集計
- 日別・担当者別・環境別の集計
- 完了率・消化率の算出
- テーブル形式でのコンソール出力、Excel/CSV形式でのレポートなど様々な形式での出力

## インストール

### 前提条件

- Python 3.7以上

### セットアップ

```bash
# リポジトリのクローン
git clone <repository-url>
cd TestStatCLI

# 依存パッケージのインストール
pip install -r requirements.txt
```

## 使い方

### 基本コマンド

本ツールのフォルダを環境変数PATHに追加することで、`tstat` というコマンドで実行することができます。

```bash
tstat [オプション] [ファイルパス/フォルダパス]
```

または、下記のように直接実行します。

```bash
.\tstat.bat [オプション] [ファイルパス/フォルダパス]
```

### コマンドラインオプション

| オプション | 短縮形 | 説明 | デフォルト値 |
|------------|--------|------|--------------|
| `path` | - | 集計対象のファイルまたはフォルダのパス | 必須 |
| `-c, --config` | `-c` | 設定ファイルのパス | `config.json` |
| `-l, --list` | `-l` | プロジェクトリストファイルのパス（JSON/YAML/TXT形式） | なし |
| `-f, --output-format` | `-f` | 出力形式（table/json/csv/excel） | `table` |
| `-o, --output-file` | `-o` | 出力ファイルパス | なし（コンソール出力のみ） |
| `-j, --json-output` | `-j` | JSON形式で出力 | `false` |
| `-v, --verbose` | `-v` | 詳細ログ出力（エラー発生時のトレース） | `false` |
| `-p, --clipboard` | `-p` | 集計データをTSV形式でクリップボードにコピー | `false` |
| `-P, --clipboard-only` | `-P` | クリップボードのみに出力（コンソール出力を抑制） | `false` |
| `--date-range` | - | 日付範囲フィルタ（YYYY-MM-DD形式、終了日は省略可能） | なし |
| `--tester` | - | 担当者フィルタ（部分一致） | なし |
| `--exact-match` | - | 担当者・環境フィルタで完全一致を使用 | `false` |
| `--result-type` | - | 結果タイプフィルタ（複数指定可） | なし |
| `--environment` | - | 環境フィルタ（部分一致） | なし |
| `--detailed` | - | 複数ファイル処理時にファイル別の詳細結果も表示 | `false` |
| `-h, --help` | `-h` | ヘルプ表示 | - |

### オプション使用例
```bash
# 単一ファイルの集計
tstat path/to/your_file.xlsx

# 複数ファイルの集計（複数のxlsxファイルを直接指定）
tstat path/to/file1.xlsx path/to/file2.xlsx path/to/file3.xlsx

# フォルダ内の全Excelファイルを集計
tstat path/to/your_folder

# プロジェクトリストファイル使用（JSON形式）
tstat -l project_list.json

# プロジェクトリストファイル使用（YAML形式）
tstat -l project_list.yaml

# プロジェクトリストファイル使用（テキスト形式）
tstat -l list_sample.txt

# カスタム設定ファイルを使用
tstat path/to/your_file.xlsx -c custom_config.json

# JSON形式で出力
tstat path/to/your_file.xlsx -j

# CSV形式でファイル出力
tstat path/to/your_file.xlsx -o results.csv

# Excel形式でファイル出力
tstat path/to/your_file.xlsx -o results.xlsx

# 複数ファイル処理でCSV出力
tstat path/to/your_file.xlsx -o summary.csv

# 詳細ログ出力
tstat path/to/your_file.xlsx -v

# TSV形式でクリップボードにコピー
tstat path/to/your_file.xlsx -p

# クリップボードのみに出力
tstat path/to/your_file.xlsx -P

# 日付範囲フィルタ
tstat --date-range 2024-01-15 2024-01-20 path/to/your_file.xlsx

# 担当者フィルタ
tstat --tester テスト太郎 path/to/your_file.xlsx

# 結果タイプフィルタ
tstat --result-type Pass Fail path/to/your_file.xlsx

# 環境フィルタ
tstat --environment セット1 path/to/your_file.xlsx

# 複合フィルタリング
tstat --date-range 2024-01-15 2024-01-20 --tester テスト太郎 --result-type Pass path/to/your_file.xlsx

# 詳細出力オプション（複数ファイル処理時）
tstat --detailed path/to/file1.xlsx path/to/file2.xlsx
```

## 出力形式

### テーブル形式出力（デフォルト）

```
==================================================
Summary Results
==================================================

File: path/to/your_file.xlsx
Total Cases: 150
Available Cases: 145
Excluded Cases: 5

TOTAL RESULTS:
┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────────┬─────────────┐
│ Pass    │ Fixed   │ Fail    │ Blocked │ Suspend │ N/A     │ Total   │ 完了数   │ 消化数   │ 完了率(%)   │ 消化率(%)   │
├─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────────┼─────────────┤
│ 120     │ 5       │ 10      │ 3       │ 2       │ 0       │ 140     │ 127     │ 140     │ 87.59       │ 96.55       │
└─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────────┴─────────────┘

STATISTICS:
┌─────────────┬─────────┐
│ Metric      │ Count   │
├─────────────┼─────────┤
│ All         │ 150     │
│ Available   │ 145     │
│ Executed    │ 140     │
│ Completed   │ 127     │
│ Incompleted │ 13      │
│ Planned     │ 150     │
└─────────────┴─────────┘

STATUS: 進行中
Start Date: 2024-01-15
Last Update: 2024-01-20
```

### JSON形式出力（`-j, --json-output` オプション）

```json
{
  "file": "path/to/your_file.xlsx",
  "stats": {
    "all": 150,
    "available": 145,
    "excluded": 5,
    "executed": 140,
    "completed": 127,
    "incompleted": 13,
    "planned": 150
  },
  "total": {
    "Pass": 120,
    "Fixed": 5,
    "Fail": 10,
    "Blocked": 3,
    "Suspend": 2,
    "N/A": 0,
    "Total": 140,
    "完了数": 127,
    "消化数": 140,
    "完了率(%)": 87.59,
    "消化率(%)": 96.55
  },
  "run": {
    "status": "進行中",
    "start_date": "2024-01-15",
    "last_update": "2024-01-20"
  }
}
```

### 複数ファイル処理

複数のxlsxファイルを同時に処理する場合、以下の方法があります：

#### 1. 直接指定による複数ファイル処理
```bash
# 複数のxlsxファイルを直接指定
tstat.exe file1.xlsx file2.xlsx file3.xlsx

# パスに空白が含まれる場合は引用符で囲む
tstat.exe "D:\Script\TestStat-CLI\input_sample\sample1.xlsx" "D:\Script\TestStat-CLI\input_sample\sample2_abcdegfg.xlsx"
```

#### 2. プロジェクトリストファイルによる複数ファイル処理
```bash
# JSON形式のプロジェクトリストファイルを使用
tstat.exe -l project_list.json

# YAML形式のプロジェクトリストファイルを使用
tstat.exe -l project_list.yaml

# テキスト形式のプロジェクトリストファイルを使用
tstat.exe -l file_list.txt
```

#### 複数ファイル処理の出力内容
複数ファイルを処理した場合、以下の情報が表示されます：

- **SUMMARY TOTAL RESULTS**: 全ファイルの統合集計結果
- **FILE BREAKDOWN**: 各ファイルの個別サマリー
- **ERROR SUMMARY**: エラーが発生したファイルの一覧
- **OVERALL STATUS**: 全体のステータス情報
- **各ファイルの詳細**: 各ファイルの個別詳細集計（`--detailed`オプション指定時のみ）

**デフォルト動作**: 複数ファイル処理時は、サマリー情報のみが表示され、各ファイルの詳細な結果（日別集計、担当者別集計、環境別集計など）は表示されません。

**詳細出力**: `--detailed`オプションを指定すると、各ファイルの詳細な結果も表示されます。

### CSV形式出力（`-f, --output-format` オプションで `csv` を指定）

CSV形式でファイルに出力する場合、以下のような構造でデータが出力されます：

**単一ファイル処理の場合**:
- TOTAL RESULTS: 集計結果のサマリー
- STATISTICS: 統計情報
- DAILY BREAKDOWN: 日別集計
- BY NAME: 担当者別集計
- BY ENVIRONMENT: 環境別集計

**複数ファイル処理の場合**:
- SUMMARY TOTAL RESULTS: 統合集計結果
- SUMMARY STATISTICS: 統合統計情報
- INDIVIDUAL FILES: 各ファイルのサマリー

### Excel形式出力（`-f, --output-format` オプションで `excel` を指定）

Excel形式でファイルに出力する場合、複数のシートに分けてデータが整理されます：

**単一ファイル処理の場合**:
1. TOTAL RESULTS - 集計結果のサマリー
2. STATISTICS - 統計情報
3. DAILY BREAKDOWN - 日別集計
4. BY NAME - 担当者別集計
5. BY ENVIRONMENT - 環境別集計
6. METADATA - ファイル情報・処理条件

**複数ファイル処理の場合**:
1. SUMMARY TOTAL RESULTS - 統合集計結果
2. SUMMARY STATISTICS - 統合統計情報
3. INDIVIDUAL FILES - 各ファイルのサマリー
4. DAILY BREAKDOWN - 統合日別集計
5. BY NAME - 統合担当者別集計
6. BY ENVIRONMENT - 統合環境別集計
7. METADATA - 処理情報・フィルタ条件

### 出力ファイル名（`-o, --output-file` オプション）

フィルタリング条件を指定した場合、ファイル名に条件が自動的に含まれます：

```bash
# 基本出力
python test_stat_cli.py -o results.xlsx sample1.xlsx
# → results.xlsx

# 日付フィルタ
python test_stat_cli.py -o results.xlsx --date-range 2024-01-15 2024-01-20 sample1.xlsx
# → results_2024-01-15_to_2024-01-20.xlsx

# 複合フィルタ
python test_stat_cli.py -o results.xlsx --date-range 2024-01-15 2024-01-20 --tester テスト太郎 sample1.xlsx
# → results_2024-01-15_to_2024-01-20_テスト太郎.xlsx
```

## 設定ファイル仕様

### config.json

デフォルトで `config.json` の設定を使用します。
`-c, --config` で任意のファイルを読み込むこともできます。

```json
{
  "read_definition": {
    "sheet_search_keys": ["テスト項目"],
    "sheet_search_ignores": [],
    "header": {"search_col": "A", "search_key": "#"},
    "tobe_row": {"keys": ["期待", "実施対象"]},
    "result_row": {"keys": ["結果"], "ignores": ["期待結果"]},
    "person_row": {"keys": ["担当者"]},
    "date_row": {"keys": ["日付", "実施日"]},
    "plan_row": {"keys": ["計画"]},
    "excluded": ["対象外"]
  },
  "test_status": {
    "results": ["Pass", "Fixed", "Fail", "Blocked", "Suspend", "N/A"],
    "completed_results": ["Pass", "Fixed", "Suspend", "N/A"],
    "executed_results": ["Pass", "Fixed", "Fail", "Blocked", "Suspend", "N/A"],
    "labels": {
      "completed": "完了数",
      "executed": "消化数",
      "planned": "計画数",
      "not_run": "未着手"
    }
  },
  "output_definition": {
    "state": {
      "completed": {"name": "完了", "foreground": "darkgreen", "background": "darkseagreen1"},
      "in_progress": {"name": "進行中", "foreground": "dodgerblue4", "background": "skyblue"},
      "not_started": {"name": "未着手", "foreground": "dimgray", "background": "silver"}
    }
  }
}
```

### 設定項目の説明

#### read_definition
- `sheet_search_keys`: 対象シートを検索するキーワード
- `header`: ヘッダー行の検索設定
- `result_row`: 結果列の検索設定
- `person_row`: 担当者列の検索設定
- `date_row`: 日付列の検索設定
- `plan_row`: 計画列の検索設定
- `excluded`: 除外対象のキーワード

#### test_status
- `results`: 結果タイプの名称および順序
- `completed_results`: 完了数のカウントに含める結果タイプ
- `executed_results`: 消化数のカウントに含める結果タイプ
- `labels`: 各指標の表示ラベル

#### output_definition
- `state`: ステータス表示の設定

## Excelファイル形式

### 対応形式
- ファイル形式: .xlsx
- エンコーディング: UTF-8

### シート構造
1. **ヘッダー行**: `#` で始まる行をヘッダーとして認識
2. **データ行**: ヘッダー行以降の行をデータとして処理
3. **列構成**:
   - 結果列: テスト結果（Pass/Fail/Blocked等）
   - 担当者列: テスト担当者名
   - 日付列: テスト実施日
   - 計画列: テスト計画日（オプション）

### サンプルデータ
```
# テスト項目    結果    担当者    日付      計画
テストケース1   Pass    テスト太郎     2024-01-15 2024-01-10
テストケース2   Fail    テスト次郎     2024-01-16 2024-01-10
テストケース3   Blocked テスト三郎     2024-01-17 2024-01-10
```

## エラーハンドリング

### 主なエラーケース

1. **ファイル未検出**
   ```
   ERROR: 指定されたパスが存在しません: /path/to/file.xlsx
   ```

2. **設定ファイル不正**
   ```
   ERROR: 設定ファイルのJSON形式が不正です: config.json
   詳細: Expecting ',' delimiter: line 10 column 5
   ```

3. **Excelファイル形式エラー**
   ```
   ERROR: ファイル処理中にエラーが発生しました: sample.xlsx
   詳細: [Errno 13] Permission denied
   ```

4. **データ読み取りエラー**
   ```
   ERROR: シートが見つかりませんでした。
   ```

### エラー対処法

- **ファイルが見つからない**: パスを確認し、ファイルが存在することを確認
- **権限エラー**: ファイルの読み取り権限を確認
- **設定ファイルエラー**: JSON形式の構文を確認
- **シートが見つからない**: Excelファイル内に「テスト項目」を含むシートが存在することを確認

## exeファイルのビルド

### 前提条件

- Python 3.7以上
- Windows環境（exeファイル作成のため）

### ビルド手順

#### 方法1: バッチファイルを使用（推奨）

```bash
# ビルド実行
build_exe.bat

# または、配布パッケージまで作成
package_exe.bat
```

#### 方法2: Pythonスクリプトを使用

```bash
# exeファイルのビルド
python build_exe.py build

# ビルドファイルのクリーンアップ
python build_exe.py clean

# 配布パッケージの作成
python package_exe.py

# 配布パッケージのクリーンアップ
python package_exe.py clean
```

### ビルド結果

ビルドが成功すると、以下のファイルが作成されます：

- `dist/tstat.exe` - メインの実行ファイル
- `dist_package/` - 配布用フォルダ
- `TestStat-CLI_v1.0.0_*.zip` - 配布用ZIPパッケージ

### 配布パッケージの内容

```
dist_package/
├── tstat.exe              # 実行ファイル
├── config.json            # 設定ファイル
├── config_example.json    # 設定ファイル例
├── README.md              # 使用方法（英語）
├── 使用方法.txt           # 使用方法（日本語）
├── assets/
│   └── logo.txt          # ロゴファイル
└── samples/              # サンプルファイル
    ├── input_sample/     # 入力サンプル
    ├── list_samples/     # リストサンプル
    └── batch_samples/    # バッチサンプル
```

### exeファイルの使用方法

```bash
# ヘルプ表示
tstat.exe --help

# 単一ファイルの集計
tstat.exe "path/to/test_spec.xlsx"

# ディレクトリ内の全Excelファイルを集計
tstat.exe "path/to/test_folder"

# 設定ファイルを指定
tstat.exe "test_spec.xlsx" -c config.json

# 結果をCSVファイルに出力
tstat.exe "test_spec.xlsx" -o "output.csv" -f csv

# クリップボードにコピー
tstat.exe "test_spec.xlsx" -p
```

### トラブルシューティング

#### よくあるエラー

1. **enum34パッケージエラー**
   ```
   ERROR: The 'enum34' package is an obsolete backport...
   ```
   **対処法**: `python -m pip uninstall enum34 -y` を実行してから再ビルド

2. **PyInstallerが見つからない**
   ```
   ModuleNotFoundError: No module named 'PyInstaller'
   ```
   **対処法**: `pip install pyinstaller` を実行

3. **ビルドエラー**
   ```
   CalledProcessError: Command returned non-zero exit status
   ```
   **対処法**: 
   - 依存パッケージを再インストール: `pip install -r requirements.txt`
   - ビルドファイルをクリーンアップ: `python build_exe.py clean`
   - 再ビルドを実行

## 開発・カスタマイズ

### プロジェクト構造
```
TestStatCLI/
├── test_stat_cli.py       # メインCLI
├── config.json            # 設定ファイル
├── requirements.txt       # 依存関係
├── build_exe.py          # exeビルドスクリプト
├── build_exe.bat         # exeビルドバッチ
├── package_exe.py        # 配布パッケージ作成スクリプト
├── package_exe.bat       # 配布パッケージ作成バッチ
├── setup.bat             # Windowsセットアップスクリプト
├── tstat.bat             # Windows実行スクリプト
├── tstat.sh              # Linux実行スクリプト
├── list_samples/         # サンプルファイル
│   ├── list_sample.json  # jsonサンプル
│   ├── list_sample.txt   # txtサンプル
│   └── list_sample.yaml  # yamlサンプル
├── utils/                # ユーティリティ
│   ├── __init__.py       # パッケージ初期化
│   ├── ReadData.py       # データ読み取り
│   ├── OpenpyxlWrapper.py # Excel操作
│   ├── Logger.py         # ログ機能
│   ├── OutputWriter.py   # 出力処理
│   ├── DataConversion.py # データ変換
│   ├── Labels.py         # ラベル管理
│   ├── ClipboardWriter.py # クリップボード出力
│   └── Utility.py        # 汎用ユーティリティ
└── assets/               # アセット
    └── logo.txt          # ロゴ
```

### 拡張ポイント
- `utils/ReadData.py`: データ読み取りロジックのカスタマイズ
- `config.json`: 設定による動作のカスタマイズ
- `test_stat_cli.py`: 出力形式のカスタマイズ
- `build_exe.py`: exeビルド設定のカスタマイズ

## ライセンス

MIT
