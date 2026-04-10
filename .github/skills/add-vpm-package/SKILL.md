---
name: add-vpm-package
description: "VPMリポジトリへの公開パッケージ追加・バージョン更新。Use when: パッケージ追加, バージョン更新, VPM, vpm.json 編集, パッケージ公開"
argument-hint: "パッケージ名とバージョン（新規ならdescriptionも）"
---

# VPM パッケージ追加・更新

docs/vpm.json を編集して VPM リポジトリにパッケージを追加または更新する。

## 必須入力

| 操作 | 必須パラメータ |
|------|----------------|
| **バージョン更新** | パッケージ名, バージョン |
| **新規パッケージ追加** | パッケージ名, バージョン, description |

## displayName

ユーザーが指定しない場合、リポジトリ名をそのまま使う（例: `jp.notek.udonfs` → `UdonFS`）。

## 確認事項

バージョン更新・新規追加のどちらでも、以下の変更がないか必ずユーザーに確認する：

- `unity` (例: `"2022.3"`)
- `vpmDependencies` (例: `"com.vrchat.worlds": "^3.10.2"`)
- `vrchatVersion` (例: `"2026.1.3"`)

指定がなければ、同パッケージの直近バージョンの値を引き継ぐ（新規パッケージの場合は既存パッケージの最新値を参考にする）。

## 固定値（index.json から継承）

以下のフィールドは既存の index.json の値をそのまま使う。ユーザーに聞かない：

```json
{
  "author": {
    "name": "伊東ノト",
    "email": "notoito.public+notek@gmail.com",
    "url": "https://notoitoh.notek.jp"
  }
}
```

## URL パターン

```
https://github.com/notek/{リポジトリ名}/releases/download/{version}/{パッケージ名}-{version}.zip
```

- `リポジトリ名`: パッケージ名からドメインプレフィックス (`jp.notek.`) を除いた部分（例: `jp.notek.udonfs` → `UdonFS`）
- ユーザーがリポジトリ名を明示した場合はそちらを使う

## 手順

### 1. 入力を収集

ユーザーの指示から以下を特定する：

- **操作種別**: 新規追加 or バージョン更新
- **パッケージ名**: `jp.notek.xxx` 形式
- **バージョン**: セマンティックバージョニング
- **description**: 新規の場合のみ必須
- **依存関係の変更**: unity, vpmDependencies, vrchatVersion に変更があるか確認

不足があれば質問する。

### 2. docs/vpm.json を読み込む

現在の vpm.json の全体構造を読み取る。

### 3. JSON を編集

#### バージョン更新の場合

対象パッケージの `versions` オブジェクトに新しいバージョンエントリを追加する。既存バージョンは残す。

```json
"packages": {
  "jp.notek.xxx": {
    "versions": {
      "1.0.1": { ... },
      "1.0.0": { ... }
    }
  }
}
```

**注意**: 新しいバージョンを上（先頭）に配置する。

#### 新規パッケージ追加の場合

`packages` オブジェクトに新しいパッケージキーを追加する。

### 4. JSON を検証

- JSON として valid であること
- 必須フィールドが全て存在すること: `name`, `displayName`, `version`, `unity`, `vpmDependencies`, `description`, `author`, `vrchatVersion`, `url`

### 5. Git で main に push

```bash
git add docs/vpm.json
git commit -m "Add {パッケージ名} {version}"
git push origin main
```

- main ブランチで直接作業する（ブランチを切らない）
- コミットメッセージ例:
  - 新規: `Add jp.notek.xxx 1.0.0`
  - 更新: `Update jp.notek.xxx to 1.0.1`
