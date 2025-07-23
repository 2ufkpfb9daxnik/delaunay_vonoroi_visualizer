# 指示書：インタラクティブなアルゴリズム可視化Webアプリケーションの開発

あなたは計算幾何学とWebフロントエンド開発（特にReact, TypeScipt, d3.js, Tailwind CSS）に精通したエキスパートプログラマーです。
これから、「アルゴリズムとデータ構造」の講義課題として、ドロネー図とボロノイ図の双対性をテーマにした、インタラクティブな可視化Webアプリケーションを開発します。以下の要件に従って、ステップ・バイ・ステップで開発を進めてください。

## 1. プロジェクト概要

- **テーマ:** ドロネー図（ドロネー三角形分割）とボロノイ図の双対性の可視化
- **目的:** 以下の4つのアルゴリズムを実装し、その動作過程を視覚的に理解できるWebアプリケーションを作成する。
    1. 点集合からドロネー図を直接生成 (Bowyer-Watson)
    2. 点集合からボロノイ図を直接生成 (Fortune's)
    3. 生成済みのドロネー図から、双対性を利用してボロノイ図を生成
    4. 生成済みのボロノイ図から、双対性を利用してドロネー図を生成
- **最終成果物:** GitHub Pagesでホスティング可能な、単一ページ構成の静的Webサイト。

## 2. 技術スタック

- **プログラミング言語:** TypeScript
- **フレームワーク:** React (Viteを使用)
- **スタイリング:** Tailwind CSS
- **描画ライブラリ:** d3.js または p5.js (グラフ描画に適したものを選択してください)

## 3. アプリケーションのファイル構造案

開発の初期段階で、以下のファイルおよびフォルダ構造を作成してください。

src
├── App.tsx # アプリケーションのメインコンポーネント
├── main.tsx # エントリーポイント
├── index.css # Tailwind CSSのディレクティブ
│
├── components/ # UIコンポーネント
│ ├── ControlPanel.tsx # 右側の操作パネル全体
│ ├── VisualizationCanvas.tsx # 左側の描画キャンバス
│ ├── CodeViewer.tsx # コード表示用オーバーレイ
│ └── PointListViewer.tsx # 座標リスト表示用オーバーレイ
│
├── hooks/ # カスタムフック
│ └── useVisualizationState.ts # アプリケーション全体の状態管理フック
│
├── algorithms/ # 計算ロジック
│ ├── bowyerWatson.ts
│ ├── fortune.ts
│ ├── duality.ts # 双対性に基づく変換ロジック
│ └── types.ts # Point, Triangleなどの共通型定義
│
└── lib/ # ユーティリティ
└── geometry.ts # 幾何学的な計算ヘルパー関数

## 4. 機能要件詳細

### 4.1. UIレイアウト
- ページ全体は2カラム構成です。**左側に `VisualizationCanvas`**、**右側に `ControlPanel`** を配置してください。
- `CodeViewer` と `PointListViewer` は、`VisualizationCanvas` の上に**オーバーレイ表示**されます。これらの表示/非表示は `ControlPanel` のトグルスイッチで制御します。

### 4.2. コントロールパネル (`ControlPanel.tsx`)
以下の機能を持つUI要素を実装してください。

- **点の生成:**
    - `点の数を入力するフィールド` (type="number", デフォルト: 30)
    - `ランダムな点を生成` ボタン
    - `リセット` ボタン
- **アルゴリズム実行:**
    - `ドロネー図を生成 (Bowyer-Watson)` ボタン
    - `ボロノイ図を生成 (Fortune's)` ボタン
    - `ドロネー図 → ボロノイ図` ボタン (ドロネー図生成後に有効化)
    - `ボロノイ図 → ドロネー図` ボタン (ボロノイ図生成後に有効化)
- **表示設定:**
    - `領域を色付け` トグルスイッチ
    - `コードを表示` トグルスイッチ (CodeViewerの表示/非表示)
    - `座標リストを表示` トグルスイッチ (PointListViewerの表示/非表示)
- **アニメーション制御:**
    - `再生/一時停止` ボタン
    - `ステップ実行` スライダー: アニメーションの全ステップを`max`とし、現在のステップを`value`とするスライダー。
    - `速度(ms)` 数字入力フォーム: ステップ間の待機時間（ミリ秒）を指定。

### 4.3. 描画キャンバス (`VisualizationCanvas.tsx`)
- d3.jsまたはp5.jsを用いて、点、線、領域を描画します。
- **インタラクション:**
    - **点のホバー:** 点にマウスカーソルを合わせると、ツールチップでその点の座標と配列インデックス（例: `P5: (123.4, 567.8)`）を表示します。
    - **図形のクリック:** ドロネー三角形やボロノイセルをクリックすると、`PointListViewer`上でその図形を構成する点がハイライトされます。

### 4.4. コード表示エリア (`CodeViewer.tsx`)
- オーバーレイとして表示されます。
- **言語選択タブ:** `Python`, `C++`, `` のタブを設け、ユーザーが表示する擬似コードの言語を選択できるようにしてください。
- **リアルタイムハイライト:** 実行中のアルゴリズムのアニメーションステップと同期して、コード内の該当する行または関数ブロックがハイライトされるように実装してください。

### 4.5. 座標配列表示エリア (`PointListViewer.tsx`)
- オーバーレイとして表示されます。
- 現在キャンバス上にある全ての点の座標をインデックス付きのリスト形式（例: `P0: (x0, y0)`, `P1: (x1, y1)`, ...）で表示します。
- **クリック連動ハイライト:** `VisualizationCanvas`上で図形がクリックされた際、その図形を構成する点（三角形なら3点、セルなら母点1点）のリスト項目がハイライト（背景色を変更するなど）されるように実装してください。クリックされた図形に関連する点は、リストの最上部にまとめて表示されるとより分かりやすいです。

## 5. アルゴリズムと可視化の仕様

#### ① ドロネー図の直接生成 (Bowyer-Watson)
- **可視化:** 巨大な初期スーパー三角形の追加 → 点の逐次追加 → 新しい点を含む三角形の特定 → 不正な三角形の削除 → 辺のフリップによる再三角形化、というプロセスをステップごとに色を変えながら可視化してください。

#### ② ボロノイ図の直接生成 (Fortune's)
- **可視化:** スイープラインの移動、ビーチライン（パラボラ弧の連なり）の動的な変化、サイトイベントとサークルイベントの発生箇所を明確にハイライトしてください。

#### ③ ドロネー図 → ボロノイ図（双対性）
- **アルゴリズム:** 各ドロネー三角形の**外心**を計算し、それがボロノイ頂点となります。隣接する三角形の外心同士を結ぶとボロノイ辺になります。
- **可視化:** ドロネー三角形が1つずつハイライトされ、対応する外心がプロットされ、隣接する外心同士が結ばれていく過程をアニメーションで見せてください。

#### ④ ボロノイ図 → ドロネー図（双対性）
- **アルゴリズム:** 各ボロノイ辺を共有する2つのボロノイセルの**母点**同士を結ぶと、ドロネー辺になります。
- **可視化:** ボロノイ辺が1つずつハイライトされ、対応する2つの母点同士が線で結ばれてドロネー辺が生成される過程をアニメーションで見せてください。

## 6. 開発の進め方（ステップ・バイ・ステップ）

以下の順序で開発を進めていきます。各ステップの完了ごとにコードを提示してください。

**Step 1: プロジェクトセットアップと静的レイアウトの実装**
1.  Vite (`npm create vite@latest -- --template react-ts`) と Tailwind CSS を用いてプロジェクトを初期化してください。
2.  提示したファイル構造に基づき、各コンポーネントの雛形ファイル（`*.tsx`）を作成します。
3.  `App.tsx` で各コンポーネントを読み込み、Tailwind CSS を使って指定の2カラムレイアウト（左キャンバス、右パネル）を静的に構築してください。この時点では、ボタンやフォームは機能を持たない見た目だけの状態で構いません。

**Step 2: 状態管理と基本機能の実装**
1.  `useVisualizationState.ts` を作成し、アプリケーションの状態（点のリスト、アルゴリズムの選択状態、アニメーション設定など）を管理するロジックを実装します。（Zustandなどのライブラリ利用を推奨）
2.  「ランダムな点を生成」と「リセット」ボタンの機能を実装し、状態が更新されることを確認します。
3.  `VisualizationCanvas` で、状態として保持されている点のリストを描画するロジックを実装します。

**Step 3: アルゴリズムの実装とアニメーション連携**
1.  まず `algorithms/bowyerWatson.ts` に、計算ロジックを実装します。この際、アニメーションの各ステップの状態（どの三角形が追加されたか、削除されたか等）を配列として返すように設計してください。
2.  `ControlPanel` の実行ボタンが押されたら、このアルゴリズムを実行し、返されたステップの配列を状態として保存します。
3.  `VisualizationCanvas` とアニメーション制御機能（再生/一時停止、スライダー）を連携させ、保存されたステップを元にアニメーションを描画する機能を実装します。
4.  他の3つのアルゴリズムについても、同様に実装を進めてください。

**Step 4: オーバーレイ機能と高度なインタラクションの実装**
1.  `CodeViewer` と `PointListViewer` の表示/非表示トグル機能を実装します。
2.  `CodeViewer` のタブ切り替え機能と、アニメーションステップに応じたコードハイライト機能を実装します。
3.  `PointListViewer` と `VisualizationCanvas` のクリック連動ハイライト機能を実装します。

**Step 5: 仕上げとリファクタリング**
- 全機能の動作確認を行い、UIの微調整、バグ修正、コードの可読性向上などのリファクタリングを行ってください。

## 7. コンポーネントと関数の雛形
以下に、各ファイルの責務と、実装すべき主要な関数・コンポーネントの雛形を示します。これを基に実装を進めてください。
src/algorithms/types.ts

// アプリケーション全体で使用する基本的な型を定義します。

// 点を表す型
export type Point = {
  id: number; // 配列内でのユニークなインデックス
  x: number;
  y: number;
};

// 辺を表す型 (2つの点のインデックスで構成)
export type Edge = {
  p1: number;
  p2: number;
};

// 三角形を表す型 (3つの点のインデックスで構成)
export type Triangle = {
  p1: number;
  p2: number;
  p3: number;
  // Bowyer-Watsonアルゴリズムで使う外心や外接円の半径も保持すると便利
  circumcenter?: Point;
  circumradiusSq?: number;
};

// アニメーションの各ステップを表現する型
export type AnimationStep = {
  type: string; // 'ADD_POINT', 'FLIP_EDGE', 'HIGHLIGHT_TRIANGLE', 'SWEEP_LINE'など
  data: any; // ステップに応じたデータ (例: ハイライトする三角形のIDなど)
  codeLine: number; // このステップに対応するコードの行番号
};


src/lib/geometry.ts

import { Point, Triangle } from '../algorithms/types';

/**
 * 3つの点からなる三角形の外心を計算します。
 * @returns {Point} 外心の座標
 */
export function calculateCircumcenter(p1: Point, p2: Point, p3: Point): Point {
  // ... 幾何学的な計算ロジック
  return { id: -1, x: 0, y: 0 };
}

/**
 * ある点が三角形の外接円の内部に含まれるか判定します。
 * @returns {boolean} 内部にあればtrue
 */
export function isPointInCircumcircle(point: Point, triangle: Triangle): boolean {
  // ... 判定ロジック
  return false;
}
// ...その他、2点間の距離計算など、必要なヘルパー関数をここに追加します。


src/algorithms/bowyerWatson.ts

import { Point, Triangle, AnimationStep } from './types';

/**
 * Bowyer-Watsonアルゴリズムを実行し、アニメーションステップの配列を生成します。
 * @param points - 入力となる点の配列
 * @returns {AnimationStep[]} アニメーションの各フレームの状態を記録した配列
 */
export function generateBowyerWatsonSteps(points: Point[]): AnimationStep[] {
  const steps: AnimationStep[] = [];
  // 1. 巨大なスーパー三角形を初期化 (step追加)
  // 2. 点を一つずつ追加していくループ
    // 3. 新しい点を含む三角形(bad triangles)をリストアップ (step追加)
    // 4. bad trianglesを削除し、多角形の穴を作成 (step追加)
    // 5. 穴の辺と新しい点を結び、新しい三角形を作成 (step追加)
    // 6. 辺のフリップ処理 (step追加)
  // 7. スーパー三角形に関連する三角形を削除 (step追加)
  return steps;
}


src/hooks/useVisualizationState.ts

import create from 'zustand';
import { Point, Triangle, Edge, AnimationStep } from '../algorithms/types';

type VisualizationState = {
  points: Point[];
  delaunayTriangles: Triangle[];
  voronoiEdges: Edge[];
  // ... その他の描画データ

  animationSteps: AnimationStep[];
  currentStepIndex: number;
  isPlaying: boolean;
  speed: number;

  isCodeVisible: boolean;
  isPointListVisible: boolean;
  highlightedCodeLine: number;
  highlightedPointIds: number[];
};

type VisualizationActions = {
  generateRandomPoints: (count: number, width: number, height: number) => void;
  startAlgorithm: (algorithm: 'bowyerWatson' | 'fortune' | 'delaunayToVoronoi' | 'voronoiToDelaunay') => void;
  reset: () => void;
  setPlaying: (isPlaying: boolean) => void;
  setCurrentStepIndex: (index: number) => void;
  // ... その他のアクション
};

// Zustand を使用して状態を一元管理するカスタムフック
export const useVisualizationState = create<VisualizationState & VisualizationActions>((set, get) => ({
  // --- 初期状態 ---
  points: [],
  // ...

  // --- アクション ---
  generateRandomPoints: (count, width, height) => {
    // ... ランダムな点を生成し、stateを更新するロジック
    set({ points: newPoints });
  },

  startAlgorithm: (algorithm) => {
    // ... 選択されたアルゴリズムを実行し、animationStepsを生成・セットする
  },
  
  reset: () => {
    // ... 全ての状態を初期値に戻す
  },
  // ... 他のアクションの実装
}));


src/components/VisualizationCanvas.tsx

import React, { useRef, useEffect } from 'react';
import * as d3 from 'd3';
import { useVisualizationState } from '../hooks/useVisualizationState';

export function VisualizationCanvas() {
  const svgRef = useRef<SVGSVGElement>(null);
  // Zustandストアから現在の状態を取得
  const { points, delaunayTriangles, currentStepIndex, animationSteps } = useVisualizationState();

  // D3.jsによる描画処理
  useEffect(() => {
    const svg = d3.select(svgRef.current);
    // 1. 古い描画をクリア
    svg.selectAll('*').remove();

    // 2. 現在のアニメーションステップに基づいて描画内容を決定
    const currentStep = animationSteps[currentStepIndex];
    if (!currentStep) {
      // アニメーション中でなければ、最終結果または初期の点を描画
      // ... d3による点の描画ロジック ...
      // ... d3による三角形の描画ロジック ...
      return;
    }

    // 3. アニメーションステップに応じた描画処理
    // (例: 'HIGHLIGHT_TRIANGLE' なら特定の三角形の色を変える)
    // ... d3によるアニメーション描画ロジック ...

  }, [points, delaunayTriangles, currentStepIndex, animationSteps]); // 依存配列

  // マウスイベントのハンドラ
  const handlePointHover = (event: React.MouseEvent, pointId: number) => { /* ... */ };
  const handleTriangleClick = (event: React.MouseEvent, triangle: Triangle) => { /* ... */ };

  return (
    <div className="w-full h-full bg-gray-100">
      <svg ref={svgRef} width="100%" height="100%"></svg>
    </div>
  );
}


src/components/ControlPanel.tsx

import React from 'react';
import { useVisualizationState } from '../hooks/useVisualizationState';

export function ControlPanel() {
  // Zustandストアからアクションを取得
  const { 
    generateRandomPoints, 
    startAlgorithm, 
    reset, 
    setPlaying,
    // ... 他のアクション
  } = useVisualizationState();

  // 各UI要素のイベントハンドラ
  const handleGenerateClick = () => {
    // ... 点の数を取得して generateRandomPoints を呼び出す
  };

  const handleStartDelaunay = () => {
    startAlgorithm('bowyerWatson');
  };

  return (
    <div className="p-4 bg-gray-50 h-full flex flex-col space-y-4">
      <h2 className="text-xl font-bold">コントロールパネル</h2>
      
      {/* --- 点の生成セクション --- */}
      <div>
        <label>点の数:</label>
        <input type="number" defaultValue={30} className="border p-1" />
        <button onClick={handleGenerateClick} className="bg-blue-500 text-white p-2 rounded">
          ランダムな点を生成
        </button>
      </div>

      {/* --- アルゴリズム実行セクション --- */}
      <div className="flex flex-col space-y-2">
        <button onClick={handleStartDelaunay} className="...">ドロネー図を生成 (Bowyer-Watson)</button>
        {/* ... 他のアルゴリズムボタン ... */}
      </div>

      {/* ... アニメーション制御、表示設定のUI ... */}
    </div>
  );
}

## 8. 開発における行動規範

これから始まる開発セッションにおいて、あなたは以下の行動規範を絶対的なルールとして遵守してください。毎回の応答を生成する前に、必ずこのセクションの内容を再確認し、厳密に従うこと。

【毎回唱えるべき行動規範】
自己認識とタスク確認:
応答を生成する前に、まず「私は今、どのファイルのどの部分（コンポーネント、関数、フック）を実装するように求められているのか？」を自問し、提示されたプロジェクト全体の設計図（ファイル構成やコンポーネントの役割分担）と現在地を常に意識します。
エラー修正の最優先:
私（ユーザー）がエラーを報告した場合、またはあなた自身が生成したコードに論理的な誤りやバグを発見した場合は、新しい機能の実装よりも先に、その修正を最優先してください。修正が必要な箇所と、その具体的な修正案を明確に提示してください。
高品質なコードの責務:
あなたはエキスパートプログラマーです。生成するコードは常に以下の品質基準を満たしてください。
型安全: TypeScriptの型を最大限に活用し、any型は原則として使用しません。
可読性: 意図が明確にわかる変数名、関数名を使用し、複雑なロジックには適切なコメントを付与します。
責務の分離: 各関数やコンポーネントは、単一の責務を持つように設計します。
状態管理の一元化:
アプリケーションの状態は、フック useVisualizationState に集約します。コンポーネント内で安易に useState を使うのではなく、状態の「単一の情報源（Single Source of Truth）」の原則を徹底してください。状態の更新ロジックも、原則としてこのフック内のアクションとして実装します。
漸進的な開発:
一度にすべてのファイルを変更しようとしないでください。指示されたステップに従い、一つのタスクに集中して、関連する少数のファイルを変更するという形で、漸進的に開発を進めてください。
応答の形式:
コードを提示する際は、どのファイルに対する変更なのかを必ず明記し、変更点について簡潔な説明を加えてください。


