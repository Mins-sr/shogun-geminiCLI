# yenプロジェクト 現状分析報告書

## 1. プロジェクト概要
yenプロジェクトは、macOS向けのインテリジェントな画面キャプチャ・OCRツールです。マウスで「円」を描くという直感的なジェスチャーで、特定の領域を素早くキャプチャし、OCRによってテキスト範囲を最適化した上でクリップボードにコピーします。

## 2. 技術スタック
- **言語**: Swift
- **UIフレームワーク**: SwiftUI, AppKit
- **主要ライブラリ/フレームワーク**:
  - **Vision**: OCR（文字認識）およびテキストブロックの検出。
  - **ScreenCaptureKit**: macOSのネイティブ画面キャプチャ。
  - **Carbon / AppKit**: グローバルなマウスイベント監視。
  - **CoreGraphics**: 座標計算、画像処理、矩形演算。

## 3. プロジェクト構造
```
/Users/toma/project/yen/
├── yen/
│   ├── Managers/
│   │   ├── HotkeyManager.swift   # ホットキー管理
│   │   └── BufferManager.swift   # キャプチャデータの保持・管理
│   ├── Services/
│   │   ├── ScreenCaptureService.swift  # 画面キャプチャ実行
│   │   ├── OCRService.swift            # Visionを用いたOCR処理
│   │   ├── RectangleOptimizer.swift    # 認識結果に基づく矩形最適化
│   │   ├── CircleGestureDetector.swift # 円ジェスチャーの検知ロジック
│   │   ├── GlobalMouseMonitor.swift    # 全画面でのマウス移動監視
│   │   └── ClipboardService.swift      # クリップボード操作
│   ├── Views/
│   │   ├── AdjustmentView.swift  # キャプチャ後の微調整UI
│   │   ├── DebugOverlay.swift    # 開発用：軌跡や検出円の表示
│   │   └── OverlayWindow.swift   # 各種オーバーレイのウィンドウ制御
│   └── yenApp.swift              # アプリの基点、メニューバー制御
└── run_yen.sh                    # ビルド・実行用スクリプト
```

## 4. 実装済み機能一覧

### A. 円ジェスチャー検知 (Circle Gesture Detection)
- 全画面でマウスの動きを監視。
- 重心を計算し、1.5周（540度）以上の円運動を検知すると発動。
- 時計回り・反時計回りの両方に対応。

### B. 視覚的フィードバック (Visual Feedback)
- デバッグモード時、マウスの軌跡をリアルタイムで表示。
- 円が検出された際、その中心と半径、バウンディングボックスを画面上に可視化。

### C. インテリジェント・キャプチャ (Smart Capture)
- ジェスチャーで描かれた円の範囲を即座にキャプチャ。
- `ScreenCaptureKit` を利用した高精度な取り込み。

### D. OCR連携と範囲最適化 (OCR & Rectangle Optimization)
- キャプチャ画像内からテキストを自動検出。
- 複数のテキストブロックを近接性に基づきグループ化。
- 円の範囲に含まれる主要なテキストグループに合わせて、キャプチャ矩形を「ぴたっ」と吸着させるように自動調整。

### E. クリップボード連携 (Clipboard Integration)
- 最適化された画像を自動的にシステムクリップボードにコピー。
- 成功時にサウンドフィードバック（Glass音）を再生。

### F. 手動微調整機能 (Adjustment UI)
- 自動最適化された結果が意図と異なる場合、ハンドルを操作して矩形範囲を手動で調整可能。
- 調整後の画像を再度クリップボードにコピー。

### G. メニューバー・常駐 (Menu Bar Integration)
- メニューバーにアイコンを表示し、設定（デバッグモード切り替え等）や終了が可能。
- Dockには表示されないバックグラウンド・アクセサリ・アプリ。

---
**調査担当**: 足軽1号
**日付**: 2026-01-29
