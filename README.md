# git 運用ルール

## 改訂履歴

* 2020/06/13 初稿

## 当ルールの存在意義

git flow の運用方針は比較的規模が大きく、且つ、分散リポジトリの利点を生かしたものでありコミュニティによる参加型開発には向いているものの、案件ベースの開発で、単一リモートリポジトリでは少しルールを変更する必要があると考え、当ルールを文書化することとした。

基本的にはgit flowに倣うものだが、当RFCでは、その一部簡略化したり、意味を変更する。
またgit flowと同様にルールは複雑ではなくシンプルにすべきであり、その点は踏襲している。

## ブランチ階層

```sh
/
  +-- master
  +-- staging
    +-- development
    +-- dev/
      +-- work/
        +-- (作業者名)
      +-- (共同作業名)
  +-- fix/
    +-- #(問題番号)
  +-- hotfix/
    +-- #(問題番号)
  +-- custom
    +-- (リリース先・製品名等)/
      +-- master
      +-- staging
      +-- development
      +-- dev/
        +-- work/
          +-- (作業者名)
      +-- (共同作業名)
      +-- fix/
        +-- #(問題番号)
      +-- hotfix/
        +-- #(問題番号)
```

## mergeルール

* mergeは必ず新しいcommitを作成すること。
* fast-forward mergeは、merge箇所が見難くなるため禁止とする。
* ただし、作業用のローカルブランチからのfast-forward mergeは許される。

## master ブランチ

### 用途

stagingブランチでテストを行い、リリースもstagingブランチで行う。リリースが完了した時点でmasterブランチにmergeを行い、merge commit 名にリリース時のバージョンを記載する。

### 制限事項

直接の編集は許されず、必ずマージを行う。

### 命名規則
(なし)

## staging ブランチ

### 用途

リリースに向けたテスト用ブランチ。

Jenkinsのデフォルトブランチは当stagingブランチとする。

developmentブランチで開発・デバッグを行い、テストタイミングでstagingブランチにmergeをする。merge commit 名はバージョン番号に RC, beta, alfa 等を付記する。

### 制限事項

直接の編集は許されず、必ずマージを行う。

### 命名規則

(なし)

## development ブランチ（旧developブランチ）

### 用途

開発者向け運用ブランチ。

テストタイミングでstagingにmergeする。

なお「develop」は名詞動詞が乱れているので非推奨としましたが、過去に作成したdevelopブランチについては継続使用可能とする。

### 制限事項

(なし)

### 命名規則

(なし)

## dev/work/ ブランチ

### 用途

各個人が開発を進めるブランチ。

このdev/workブランチを以て developmentブランチで直接作業することを禁止するものではない。

自身が進めている箇所が他の開発者に影響が出る場合に使用する。

なお、developmentブランチへのマージする時期は適宜（特段の事情が無い限り作業単位が望ましい）行うこと。
また、developmentブランチへのマージ作業は各個人の責任において行う。

### 制限事項

* 特段の事情がない限り、各個人が自身の作業用のリモートブランチを持てるのは１個とする。（ローカルブランチは除く）
* 特定のcommitから作業を進めたい場合には、その特定のcommitを自身のブランチにmergeしてから作業を進める。

### 命名規則

“dev/work/(開発者名)”

## dev/ ブランチ

### 用途

複数人で特定機能を実装する場合の併合ブランチ。

ルールはdevelopmentブランチと同等で制限はない。
developmentが成熟期に入って、機能追加・テスト機能追加などを想定しているため、成長期に無暗にブランチ作成はしないでください。成長期では dev/work/ で作業を行ってください。

### 制限事項

(なし)

### 命名規則

“dev/(共同作業名)”
fix/ ブランチ

### 用途

バグフィックス専用ブランチ。

ブランチ名に問題番号を採用することで、問題の対応をトラッキングすることを目的とする。ただし、軽微な問題は当該ブランチを作成せず、直接developmentブランチで作業を行うことを許可する。その場合はcommit名に「#問題番号」を接頭詞として付記すること。

### 制限事項

masterブランチからの分岐は許されない。masterブランチからの分岐はhotfixを使用すること。

### 命名規則

“fix/#(問題番号)”

## hotfix/ ブランチ

### 用途

fix/ ブランチと同様だが、当該ブランチは master ブランチからの分岐のみである。
かなり緊急を要する場合のみ使用されるので、基本的に利用されないのではと想定される。

### 制限事項

masterブランチからのみ分岐し、他のブランチからの分岐は許されない。

### 命名規則

“hotfix/#(問題番号)”

## custom/ ブランチ

### 用途

OEM案件等でカスタマイズが必要な場合のルートとなるブランチ。

当該ブランチの以下に、master, staging, development 等のルートブランチからの階層がネストして存在する。ネストされた各ブランチの意味は前述の用途・制限事項・命名規則に準ずる。

### 制限事項

ルートブランチに存在するmaster, development, staging, dev ブランチへのmergeは禁止し、cherry pickを使用すること。

### 命名規則

“custom/(リリース先・製品名等)”
