# Haru Tokyo — LINE公式アカウント デモページ

## Project overview

Mishona（株式会社ミショナ）の採用適性テスト提出物。
美容室「Haru Tokyo」のLINE公式アカウント相当のデモWebページ。
単一HTMLファイル構成でモバイルファーストに設計。

## Submission deadline

2026年4月23日（木）

## Objective

* LINE公式アカウントのUI/UXを忠実に再現したデモページを作成
* インタラクティブな予約ボット（reservation.html）を実装
* 無料プラン相当の機能範囲内で制作
* PdM候補としての設計・実装・UX思考を示す
* PdMチェックリストアプリ（Phase1_Gate.html）でプロダクト開発ライフサイクル管理力を実証
* L Message（エルメ）の競合分析・セグメント定義でPdMリサーチ力を実証

## Files

|File|Role|
|-|-|
|index.html|メインページ — LINE公式アカウントUI（4タブ）|
|reservation.html|予約ボット — 4ステップ予約フロー＋電話フォールバック|
|Phase1_Gate.html|PdMチェックリストアプリ — 6フェーズ・QAゲート・localStorage永続化・ダークモード|
|DISCOVERY.md|Phase 1 ディスカバリー＆プランニング文書|
|PRD.md|プロダクト要求仕様書（PdMチェックリストMVP）|
|COMPETITIVE_ANALYSIS_1.md|L Message競合分析 — LINE自動化SaaS市場|
|SEGMENT_POSITIONING.md|ターゲットセグメント＆ポジショニング定義|
|CLAUDE.md|このファイル — Claude Code用プロジェクト定義|
|AGENTS.md|Codex用プロジェクト定義|

## Tech stack

* Vanilla HTML / CSS / JavaScript — 単一ファイル、ビルドツール不要
* Google Fonts (Noto Sans JP)
* CSS variables（ライト/ダークモード対応）
* localStorage（状態保持）
* No frameworks, no npm, no dependencies

## index.html structure

* LINE風トップバー（戻るボタン・タイトル・メニュー）
* カバー写真エリア
* プロフィールブロック（アバター・友だち数・自己紹介・友だち追加ボタン）
* 4タブ: ホーム / メニュー / クーポン / 店舗情報
* ホーム: リッチメニュー6ボタン・キャンペーンカード・お知らせ
* メニュー: カテゴリ別料金表
* クーポン: 使用可能クーポン2枚（つかうボタン）
* 店舗情報: 住所・営業時間・地図プレースホルダー

## reservation.html structure

* 4ステップウィザード: メニュー → 日時 → お客様情報 → 完了
* ボットバブルUI（アシスタント感を演出）
* インタラクティブカレンダー（定休日・過去日・空き表示）
* 時間スロット（空きあり / 満席 の区別）
* フォームバリデーション（名前・電話番号 必須）
* 予約確認サマリー画面
* 全ステップに電話フォールバック: 03-1234-5678

## Coding rules — MUST follow

* CSS variables only — hardcoded colors NEVER allowed
* Max width: 390px（スマートフォン幅）
* Font size minimum: 11px
* Dark mode must work via prefers-color-scheme
* No console errors
* Phone failsafe href="tel:0312345678" must remain on every screen
* Japanese text must use Noto Sans JP
* LINE green: #06C755 (never substitute)
* Haru Tokyo rose accent: #c8846a

## What NOT to change

* The LINE green color (#06C755) — brand critical
* The tel: link on all reservation steps — failsafe requirement
* The 4-tab structure of index.html — matches LINE Official Account spec
* Salon name: Haru Tokyo / ハル東京

## Suggested improvements (post-submission)

* Add Microsoft Clarity tracking snippet (replace YOUR\_PROJECT\_ID)
* Add smooth CSS transitions to tab switching
* Add loading skeleton for calendar
* Add swipe gesture support for tab navigation

