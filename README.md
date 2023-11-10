## 目次
- [目次](#目次)
- [リポジトリ概要](#リポジトリ概要)
- [リポジトリ作成の目的](#リポジトリ作成の目的)
- [入力と出力](#入力と出力)
- [ディレクトリ構成概要](#ディレクトリ構成概要)
- [ディレクトリ構成詳細](#ディレクトリ構成詳細)
- [python version](#python-version)
- [使い方](#使い方)
- [入力データについて](#入力データについて)
- [configファイルについて](#configファイルについて)


## リポジトリ概要
- [横須賀市の小学校の給食](https://www.city.yokosuka.kanagawa.jp/8345/kyuushoku/kyuusyoku-menu-open.html)についてのオープンデータを読み込み、前処理、グラフ出力をするパッケージ
- パッケージは作成中(現状はデータ読み込み→前処理→1種類のグラフ出力のみ)
- 最終的なイメージはデータ前処理、分析、予測、結果出力を一気通貫で可能なシステムにしたい

## リポジトリ作成の目的
- 目的
  - データ分析パッケージの開発を実践するため
- 分析対象データの選定理由
    - 横須賀市のオープンデータが取得しやすく、操作しやすい(csv)ため
    - 地域ごとにデータがあり、給食について地域差があるのか興味があり、調べたいため

## 入力と出力
- 入力
  - 1月分の地域ごとの学校給食献立csvデータ
- 出力
  - 全期間の地域ごとの学校給食献立データを前処理したものをグラフ化したpngデータ

## ディレクトリ構成概要
- school_lunch_analysis
  - analyzer
    - データ前処理、グラフ化のパッケージ
  - settings
    - 読み込むデータに従う設定ファイル
  - data
    - 読み込みたいデータを格納するディレクトリ
    - 複数のcsvデータをまとめたzipファイルを格納する

## ディレクトリ構成詳細
- analyzer
  - main.py
    - 実行ファイル
  - config.py
    - データ前処理操作を設定する設定ファイルの受付を行う
  - read_data.py
    - csvデータの読み込みを行う
  - preprocessing.py
    - データの前処理を行う
  - output.py
    - 前処理結果のグラフ出力を行う
- settings
  - config.json
    - データ前処理操作の設定ファイル。中身は[configファイルについて](#configファイルについて)
- data
  - {調査対象データ名(自由に決める)}.zip
    - 以下にデータ例を示す
    - kyusyoku.zip
      - gakoukyushokuod0404a.csv
      - gakkoukyusyokuod0404b.csv
      - ...
      - [入力データについて](#入力データについて)でデータを説明する

## python version
- python 3.11

## 使い方
1. config.jsonに設定値を設定し、settings/に配置。設定方法は[configファイルについて](#configファイルについて)を参考に設定する
2. dataディレクトリに[横須賀市の小学校の給食](https://www.city.yokosuka.kanagawa.jp/8345/kyuushoku/kyuusyoku-menu-open.html)から取得した全期間のcsvデータを一つのディレクトリにまとめzip化して格納。
3. python3 -m analyzer config.json で実行
   - 例：python3 -m analyzer config.json
   - ※settingsディレクトリに格納されたjsonのconfigファイルを指定する
   - 前処理実行時に、csvファイル内のデータ入力範囲外に何らかのデータが存在した場合は、その情報を out_of_range というファイルに書き出し出力する
4. resultディレクトリが作成され、前処理データをグラフ化した結果が出力される
   - 出力結果例：
   - compare_date_contents_all_group.png
     - 縦軸:各種数値データ、横軸:日付 での地域ごとに比較したグラフ


## 入力データについて
- 横須賀市の小学校5地域(a~e)における月ごとの献立表データ
- 命名規則
  - gakkoukyusyokuod0404a.csv
  - gakkoukyusyokuod0404b.csv
  - ...
  - od以下の0404a, 0404bが元号、月、地域グループ番号
  - 0404a: (04, 04, a) -> (元号、月、地域グループ番号)
- 構造
  - 日付、献立名と、献立に使用される材料とその分量、栄養を一覧にしている
  - 以下、構造例

| A日付 | 献立名 | 材料名 | 分量（g） | エネルギー （kcal） | たんぱく質 （g） | 脂質 （g） | ナトリウム （mg） |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2022/4/13 | カレーライス | 米 | 70 | 239 | 4.3 | 0.6 | 1 |
| 2022/4/13 | カレーライス | 麦 | 10 | 33 | 0.7 | 0.2 | 0 |
| 2022/4/13 | カレーライス | 油 | 0.3 | 3 | 0 | 0.3 | 0 |
| ... | ... | ... | ... | ... | ... | ... | ... |

## configファイルについて
- データの前処理を行うにあたり、処理を行いたいデータのカラムや値を指定する
- 以下を参考に、config.jsonを設定する(発展予定)

| 大項目 | 中項目 | データ型 | 入力例 | 説明 |
| --- | --- | --- | --- | --- |
| analysis_target | ... | str | "kyusyoku" | 調査を行いたいzipデータ名 <br> dataディレクトリに格納されたデータを指定する|
| formatting | -> | -> | -> | データ前処理(csvデータ内の入力データ(数値、文字列)以外の処理に関する項目) |
| formatting | rename_col | str | "日付" | ※変更予定、現状は「日付」で固定 <br> ※データごとに同じ意味で異なるカラム名があった場合に統一カラム名に変更したい <br> 地域グループごとのcsvデータの日付カラムは、「A日付」のように <br> 地域グループ番号 + 日付 <br> となっている <br> これを地域グループ番号を除いた「日付」に統一する |
| manipulating | -> | -> | -> | データ前処理(csvデータ内の入力データの欠損値や文字列処理に関する項目) |
| manipulating | nal_name | str | "水" | ※変更予定、現状は「水」で固定 <br> Nanの処理(0埋め)を行いたい材料名を指定 <br> 現状は「水」以外にnanがない <br> 今後、そのほかの材料名に欠損値が出た場合はその材料名を指定できるようにする |
| manipulating | str_num_col | str | "分量（g）" | ※変更予定、現状は「分量 （g）」で固定 <br> 数値データが一意な入力値でない場合の処理 <br> 現状は「分量 （g）」で「120～130」のような文字列で数値データが幅を持つような意味で値が入力されている箇所のみ存在する <br> 「～」を取り除き平均値で埋めなおす処理をしている <br> 例：120～130 -> 125 <br> 今後、そのほかの栄養に関するカラムで欠損値が出た場合はそのカラムを指定できるようにする |
| manipulating | datetime_col | str | "日付" | ※変更予定、現状は「日付」で固定 <br> datetime型に変換したいカラム名を指定 <br> rename_colで日付に統一しているため、日付で固定 <br> datetime型に変換するカラムは日付のみなので、config.jsonで指定しなくてもよいように変更予定 |


