# Op.

[English](README.en.md)

## Op. 1.000（Windows版）

完全無料の個人開発メディアプレイヤーです。Windows 10/11 x64向けに配布を準備しています。内部バージョンと更新用タグは `1.0.0` / `v1.0.0` です。

- ローカルの音楽・動画、ライブラリ、プレイリスト、お気に入り、バックアップ
- EQ、自動音量調整、曲間ギャップレス、画面の端に配置できるミニプレイヤー
- 日本語・英語、ライト・ダークテーマ

配布先は[GitHub Releases](https://github.com/yanai00/op./releases)です。公開前はダウンロードできません。設定済みの更新先が公開アクセスできることを公開時に確認してください。

ネイティブ音声・排他出力は設定から有効にする試験機能です。長時間の物理ビットパーフェクトは未認証です。macOS/Linux・モバイルの製品版は未検証です。

配布前の結果と制限は[1.000のチェック記録](docs/RELEASE_1_000.ja.md)を参照してください。

## 開発記録（過去版の記述を含む）

以下には過去の版の設計・実装記録が含まれます。現在の配布範囲は上記とチェック記録を優先してください。


旧称Preserver。仮ロゴは細い線で描いた「Op.」とピンクのピリオドです。既存のデータ保存キーとバックアップ形式は互換性のため維持しています。

ローカル音源とSubsonic互換サーバーを扱う、個人用の音楽プレイヤー。白とピンクのUI、音域ごとに反応する弦のビジュアライザーを備えています。

## 展開方針

**Tauri 2** のアプリ基盤を追加しました。既存のHTML/CSS/JavaScriptを内蔵し、Nodeサーバーを起動せずにローカルの音楽・動画を扱います。Windowsを最初の検証対象とし、macOS・Linux・Android・iOS・iPadOSへ段階的に展開します。再生は現在もシステムWebViewとWeb Audioで、独立したネイティブ再生エンジンへの移行はまだです。

## ローカルアプリの開発・ビルド

Node.jsとRust stable、各OSの[Tauri開発環境](https://v2.tauri.app/start/prerequisites/)を用意して `npm install` します。WindowsはC++ Build ToolsとWebView2が必要です。このPCにはユーザー領域へRustを追加しました。実行スクリプトは `.cargo/bin` を探すため、システムPATHの変更は不要です。

- `npm run desktop:dev`：内蔵画面をビルドして開発用アプリを起動。
- `npm run desktop:build`：配布用アプリを生成。WindowsはNSISインストーラー。
- `npm run desktop:build -- --debug`：動作確認用のデバッグ版を生成。
- `npm run desktop:check`：開発環境を確認。
- `npm run build:desktop`：許可した画面ファイルだけを `dist/` にコピー。

Windowsの出力先は `src-tauri/target/release/`、インストーラーはその中の `bundle/nsis/` です。デバッグ版は `release` の代わりに `debug` に出力します。キャッシュ、ユーザー音源、Python環境、Nodeサーバーは同梱しません。署名と自動アップデートは未設定です。

ブラウザとアプリのライブラリは保存領域が別です。ブラウザ版の設定から「ローカル音源も含める」でバックアップを保存し、アプリ版で復元してください。ブラウザ側の元データは残ります。アプリ版でもファイル選択で追加でき、保存方式は従来と同じIndexedDBです。フォルダ監視はWebViewのAPI対応に依存し、ネイティブなフォルダ登録・監視は今後移植します。

アプリ版のSubsonic接続とPythonによる実験用BPM解析は未移植です。接続操作では準備中の案内を表示し、Pythonへの自動リクエストは送りません。タグBPM表示とブラウザ内の音に反応する波形は残ります。動画の形式・PiPなどは各OSのWebViewが対応する範囲です。

macOS/Linuxは各OS上でビルドします。AndroidとApple向けのエントリーポイントは用意していますが、端末プロジェクト生成・権限・バックグラウンド再生・ストア配布は未実装です。iOS/iPadOSのビルドと署名にはmacOSとXcodeが必要です。現在の土台だけで全OS検証済みという意味ではありません。

## 起動

Node.js 22以上で `npm install` → `npm run dev`。ブラウザで http://127.0.0.1:4173/ を開きます。Nodeのサーバーはループバックだけで待ち受けます。通常の音楽再生・管理にPythonは不要です。BPM解析環境は任意です。

初回起動時のライブラリは空です。デモ音源・合成音は含めません。以前のデモ項目は読み込み時に除外し、追加した音楽・動画は維持します。

## 主な操作

- **Library → ＋追加**：音源を追加、音源フォルダを登録。音源・曲情報・ジャケット・お気に入り・プレイリスト・キューと再生位置をブラウザに保存します。
- **アルバム**：同じアルバム名・年・埋め込みジャケットの曲をまとめます。ジャケットがない場合はアルバムアーティストで区別します。アルバム再生はディスク番号→トラック番号順です。
- **曲／アルバムの ⋯ → プロパティ**：曲情報、音声形式、ファイル情報を確認。「編集」タブで曲名・アルバム・アーティスト・ディスク／トラック番号・ジャケットなどを変更します。変更はPreserver内に保存し、元のファイルには書き戻しません。
- **キュー**：右端のホバーまたはキューボタンで開く。ドラッグや上下ボタンで並べ替え。各曲のキュー＋アイコンで最後に追加します。
- **再生**：シャッフル／全曲リピート／1曲リピート、OSのMedia Session連携に対応します。
- **削除**：ライブラリからの削除はソフト削除。直後の「元に戻す」、またはライブラリ操作メニューから復元できます。元ファイルは削除しません。

## 日常の再生操作

- **動画の続きから再生**：動画ごとに位置を記憶します。再生中は5秒ごと、曲の変更・一時停止などで保存します。最後の3秒まで見た動画は次回先頭から始めます。「⋯」から「続きから再生／最初から再生」を選べます。
- **キーボード**：Spaceで再生／一時停止、←→で5秒シーク（Shiftで30秒）、↑↓で音量を5%変更、Mでミュート、Fで動画の全画面を切り替えます。入力欄、スライダー、ダイアログの操作中は割り込みません。
- **ドロップ追加**：Listen／Libraryのどちらでも、音楽・動画ファイルを画面にドロップして追加できます。キューの並べ替えには干渉しません。フォルダは追加メニューから登録します。
- **再生速度**：再生バーの「1×」または設定 → 再生から0.5〜2倍を選択します。設定を保存し、音楽・動画に適用します。等倍以外は通常再生を使い、ピッチ維持はブラウザの対応範囲です。
- **再接続**：曲・動画の「⋯ → ファイルを再接続」で移動した元ファイルを選び直せます。元と違う内容の場合は確認してから接続し、曲情報・お気に入り・プレイリストの参照は残します。

## 並べ替え

Libraryの並べ替えで、追加順・曲名・アーティスト・アルバム・再生時間・リリース年・最近再生した順を選べます。隣の矢印で昇順／降順を切り替え、設定は保存されます。年や再生履歴がない項目は最後に置きます。アルバム順では降順でも各アルバム内のディスク・トラック番号順を保ちます。「最近」のタブは最新順に固定します。「この一覧を再生」は表示順でキューを作ります。

## 最近の再生

Libraryの「最近聴いた曲」「最近見た動画」で、各メディアの再生履歴を新しい順に表示します。実際に約3秒再生した項目を記録し、同じ項目は最新の1件にまとめます。削除済みの項目は表示しません。履歴はこのブラウザ内に保存され、バックアップにも含まれます。

## 動画のプロパティ

動画の「⋯ → プロパティ → 編集」でタイトル、制作者、シリーズ、カテゴリ、制作年、説明、サムネイルを変更できます。シリーズなどは空欄にもできます。変更はPreserver内に保存し、元動画の埋め込みタグや映像自体には書き戻しません。解像度・FPSなどの技術情報は読み取り専用です。音楽のプロパティは従来の項目を使います。

## 動画再生

Library → ＋追加 → 音楽・動画ファイルからローカル動画を追加できます。Library上部の「音楽」「動画」でライブラリを分けて表示します。最後に選んだ種類を記憶します。MP4、WebMなど、ブラウザがデコードできる動画が対象です。拡張子が対応していても内部のコーデックが非対応なら取り込めません。変換は行いません。

Listenでは同じフレーム内に映像を表示し、音に反応する波形も残します。下の再生・シーク・音量・キューを共用します。映像下の「音声のみ」で従来のジャケットと波形に切り替え、「全画面」「小窓再生」はブラウザが対応する場合に利用できます。サムネイルと解像度を取得し、音楽と同じ保存・プロパティ編集・お気に入り・バックアップに対応します。動画は通常再生経路を使い、ギャップレスのデコード対象から除外します。字幕、音声トラック切り替え、動画配信サービスとの接続は未対応です。

## 設定（ヘッダーの歯車／Libraryの⋯）

設定はウィンドウ全体に表示し、左のカテゴリから切り替えます。狭い画面ではカテゴリが横並びになります。音量・速度・シャッフル・リピート・ギャップレス・自動音量・Listenへの移動、ライブラリの表示順を変更できます。キーボード操作一覧と保存領域の使用量も確認できます。変更は即時反映し、従来の保存方式を引き継ぎます。アプリアイコンは白背景付きです。

### 音源フォルダ

File System Access APIに対応するブラウザでは、読み取り専用のフォルダを登録できます。起動時と、画面を開いている間の1分ごとに、サイズ・更新日時が変わったファイルだけを取り込みます。再許可が必要なときは「更新」を押します。非対応ブラウザでは、フォルダ選択で重複を除いて取り込みます。元フォルダから消した曲は、ライブラリのコピーには残ります。内容が更新されたファイルは新しい版へ置き換え、編集情報・ジャケット・プレイリストとお気に入りの参照を引き継ぎます。

### ストリーミング

Navidrome／Subsonic互換サーバーのURL、ユーザー名、パスワードを入力して接続します。アルバム一覧と曲情報をLibraryへ取り込み、再生時はNodeのローカルプロキシ経由でストリーミングします。Rangeリクエストに対応しています。

パスワードは保存しません。接続トークンはNodeのメモリとタブのsessionStorageだけで保持します。Nodeを再起動した後、またはタブのセッションを終了した後は再接続が必要です。接続先はユーザーが指定したサーバーに限られ、外部のサービスへの自動アップロードはありません。

仕様：[Subsonic API](https://www.subsonic.org/pages/api.jsp)。実際の接続先はまだ未設定です。互換サーバーのモックで認証・一覧・Range配信を検証しています。

### バックアップと復元

設定、曲情報、ジャケット、プレイリスト、お気に入り、再生状態をZIPで保存します。「ローカル音源も含める」を選ぶと音源ファイルも含まれます。リモート音源は接続先の情報のみです。ZIPは音源を小さなチャンクで書き出します。

復元前に内容を表示し、既存ライブラリとマージするか、一覧を置き換えるかを選べます。直前の復元は「前回の復元を取り消す」で戻せます。音源を含めないバックアップを別のブラウザで復元した場合、元と同じ音源を追加するとSHA-256で再接続します。認証情報やフォルダの権限はバックアップに含めません。

### 音量の自動調整

設定 → 再生 → 「音量を自動でそろえる」で切り替えます（初期状態オフ）。音楽と動画の再生中のRMS音量を参照し、最大約+18 dB／最小約−12 dBの範囲で小音量の動画・音楽を持ち上げて補正します。無音は増幅せず、ピーク用コンプレッサーを併用します。大きい音への変化は速く追従し、ギャップレスの曲切り替えでも増幅量をリセットします。音量スライダーはそのまま使えます。オフでは処理をバイパスします。設定とバックアップに保存されます。曲全体を事前に測るLUFS正規化ではなく、再生開始や音量の変化への追従には数秒かかります。

### ギャップレス再生

ローカル音源をデコードし、次の曲を先読みしてAudioBufferSourceNodeの再生時刻を接続します。シャッフル／リピートとキューの変更にも追従します。保存済み曲の再生位置を保った一時停止・シークに対応します。

メモリ使用量を抑えるため、1曲のデコード見込みが128 MiBを超える音源や、先読みの合計が256 MiBを超える場合は通常再生になります。次の曲のデコードが間に合わない場合も通常の曲送りに戻ります。元ファイルに含まれる無音・エンコーダーパディングは自動トリミングしません。リモート音源はHTMLAudioでストリーミングし、この時刻接続は適用しません。

## 検証とビルド

- `npm run check`：JavaScriptの構文チェック。
- `npm test`：アルバム・バックアップ／復元・フォルダ差分・再生順・ギャップレス・ストリーミングプロキシなどの20テスト。
- `npm run build:metadata` / `npm run build:archive`：ブラウザ用バンドルを再生成。

初期の3曲は架空のデモ音源です。音源と設定はブラウザの http://127.0.0.1:4173 の保存領域にあり、サイトデータを消すと失われます。必要に応じてバックアップを保存してください。

---

## 開発履歴

以下は過去の試作や検証の記録です。現行の操作・対応範囲は上記を参照してください。

## BLSTM拍解析（実験）

Python環境: `.venv/Scripts/python -m pip install madmom-infer==0.2.0 soundfile==0.14.0`。
Libraryで音源を追加・選択し、TEMPO横の「BLSTM解析」を押します。音源はlocalhostの解析サーバー内で処理し、外部へ送信しません。初回のみ学習済み重みをダウンロードします。結果は `.analysis-cache/` に音源のSHA256単位で保存します。解析完了まで軽量リアルタイム検出を使い、完了後は検出拍と局所テンポに同期します。手動BPMが優先です。

音声デコードはSoundFile対応形式（FLAC/WAV等）。M4A等は現在モデル解析で未対応の場合があります。モデル: CPJKU madmomの8モデルBLSTMアンサンブル + DBN、実装: madmom-infer 0.2.0。モデル重みはCC BY-NC-SA 4.0（非商用）。個人用PoCでの試用向けです。


## BeatNet（現在の試験ボタン）

TEMPO横の「BeatNet解析」はBeatNet 1.1.3のモデル1（CRNN）と公式の因果的カスケード粒子フィルタを使います。ブラウザからファイルをローカルAPIへ渡して一括実行する方式です。解析器はオンライン方式ですが、現時点で再生中の音声チャンクをサーバーへ逐次送信する機能はありません。元のBLSTM APIも保持しています。

実行環境は `.venv-beatnet`（既存のPyTorch CPU環境を参照）。再構築時は `python -m venv --system-site-packages .venv-beatnet` の後、同環境で `pip install --no-deps BeatNet==1.1.3 madmom-infer==0.2.0 soundfile==0.14.0`、`pip install cffi`。PyTorch、NumPy、SciPy、Matplotlibが必要です。古いmadmomの状態空間APIをmadmom-inferで置換し、NumPyのin1d互換処理をアプリ側に限定しています。マイクは使いません。

モデル・アルゴリズム出典: https://github.com/mjhydri/BeatNet (Mojtaba Heydari et al., ISMIR 2021)。モデル種別を含めてキャッシュを分離しています。

波は太い順に40–180Hz、180–650Hz、650–3000Hz、3000–14000Hzに対応。各帯域の強さと立ち上がりを独立に描画し、全体の振れ幅にはRMS音量のエンベロープを掛けています。


BeatNetは現在、CRNNの隠れ状態とPFの粒子状態を保ちながら1秒ブロックで処理し、NDJSONで途中結果を反映します。ファイル読込と特徴量抽出は先に行うため、初回表示まで準備時間があります。初期の不安定な結果は8拍以上・55–215 BPMの条件で抑制します。未解析位置では軽量検出を継続します。波の振れ幅は描画高の39%を上限とし、粒子も上下3pxの内側へ制限します。


## 追加時の自動比較

音源追加直後に比較ジョブをキューへ登録し、長窓ベースライン→BeatNetの順に自動実行します。Library下部に推定BPM、解析処理時間、キャッシュ・転送を含む取得時間を表示します。複数ファイルも順番に処理します。長窓は10秒区間・5秒シフトのスペクトルフラックス自己相関を集約する独自の比較用実装で、CRI BEATWIZのVLW実装を再現するものではありません。正解注釈なしの数値比較は精度評価ではありません。


## 候補判定方式（現在の追加時の標準処理）

追加時は `/api/analyze-tempo` で `analyze-tempo.py` を実行します。長窓の周期候補に半分・倍・2:3/3:2の候補を残し、低中高域のオンセットに対する位相・拍支持率と区間合意を比較します。競合する場合だけ、BeatNetモデル1のCRNNを選んだ短区間（最大3×16秒）に実行します。PFは使いません。専用の拍単位分類器を新規学習したものではありません。

区間合意・候補スコア差による確定判定は未較正のヒューリスティクスです。自動で常に正しい音価を決められるわけではなく、競合は「候補」として画面に残します。Libraryまたはプレイヤーで候補を選ぶか、×½／×2／手動入力で補正できます。自動確定・手動補正は音源バイト列のSHA256をキーにブラウザのlocalStorageへ保存。同一ファイルの再追加時に復元し、再生中にライブ推定で上書きしません。音源そのもの・ジャケットの永続保存とは別です。候補段階の値は演出に使いますが確定表示はしません。

この版は個人用PoC。CRNNモデルの制約とライセンスは従来のBeatNet検証と同様です。元の8曲の正解値はコードに含めていません。

検証：`.venv-beatnet/Scripts/python tests/test_tempo.py`、`node tests/test-tempo-state.cjs`。追加時のアップロードUIの実ファイルテストはブラウザ権限チェックで拒否されたため未確認。候補解析の実ファイル実行と、固定表示・補正保存ロジックは個別に確認しています。

波の4本は同じ幅に対して0.72／1.48／2.73／4.65周期を描き、進む速さも別々に設定しています。音域別の太さ・RMSダイナミクス・上下の振れ幅上限を維持します。


## 再生画面の音声情報（現在のUI）

TEMPO・解析BPM・補正操作・Libraryの解析欄は表示しません。波の動き用のテンポ処理は背景で維持しています。形式、ビットレート、サンプリングレート、チャンネル数、ファイルサイズを追加音源のメタデータから表示し、可逆圧縮音源は取得できた場合にビット深度も表示します。推定した音声仕様や固定のLOSSLESS表記は使いません。

BPMは `metadata.common.bpm` に正の有限値がある場合だけ音声情報に表示します。解析値・保存した補正値からBPM表示への代用はしません。デモ曲にはファイルの仕様を仮定せず DEMO SOUND / Synthesized と表示します。旧来のBPM UIに関する説明は開発履歴です。

検証：`node tests/test-audio-info.cjs`。提供MP3で実測情報 MP3 /128 kbps /44.1 kHz /Stereo /3.78 MB、BPMタグなしを確認。


## キュー開閉の修正

右端ホバーはドキュメントのpointermoveで一元判定し、スライド中の見かけの境界や背景レイヤーのpointerenterには依存しません。右端16pxで150ms待って開き、メニューの最終表示範囲を離れて260msで閉じます。クリックで開いた場合はメニューへ入るまで移動を待ちます。明示的な開閉で古いホバータイマーを破棄し、古いrequestAnimationFrameや非表示タイマーも世代チェックで無効化します。

`node tests/test-queue-ui.cjs`：右端展開、直接離脱、内部滞在、再入場、古い開閉タイマーとクリックの競合を確認。画面でも右端からの展開、内部での滞在・削除、削除後の連番とフォーカスを確認。削除済みの再生中曲は1曲リピートでキューに復活しません。

## Collection features (2026-10-27)

- `collection.js` adds IndexedDB persistence of audio Files, artwork, library metadata, playlists, queue order, volume and playback position. Audio data is written to a separate store once per content hash. Restore is paused; playback starts with a user action.
- Library supports tracks, albums, artists, text search including albums, sorting, multiple selection, favorites, folder import and SHA-256 exact-file duplicate detection. Deletion is a soft archive; the restore button restores archived tracks and playlists.
- Playlists support create/rename/archive, add/remove tracks, play the displayed list, drag ordering and keyboard-accessible up/down buttons. Queue supports next/last insertion and the same ordering controls; a track appears at most once in a queue or playlist.
- Metadata editing supports title, artist, album artist, album, genre, year and track number, with batch editing. Empty fields in batch editing leave existing values unchanged. Edits apply inside Preserver; original file tags are not rewritten. Existing per-track and album artwork editing is persisted.
- Media Session API connects supported OS/browser media keys to play, pause, previous/next and seek. OS integration depends on the host browser.
- Storage belongs to the browser origin `http://127.0.0.1:4173`. No account, remote music server, device sync, lyrics or gapless playback is implemented by these changes. Clearing site storage removes the saved library; original source files remain unchanged.

Validation: `node tests/test-collection.cjs`, `node tests/test-queue-ui.cjs`, `node tests/test-audio-info.cjs`, `npm run check`, and `node --check collection.js`. Browser QA verified metadata edits and playlist membership surviving reload, plus queue reorder surviving reload. Real music-file import and OS media keys were not re-tested in browser during this change.

### Playback order controls
- Queue rows have a drag grip and before/after insertion guides. Dragging suspends hover auto-close so the drawer remains usable; keyboard users retain up/down controls.
- Player exposes shuffle and a three-state repeat control (off / queue / single track), persisted with the collection. Shuffle visits each queued track once per pass and keeps previous/next history. With repeat off, automatic playback stops at the end; manual next can still wrap.
- Validation: `node tests/test-playback-order.cjs`, collection/queue checks, and native browser drag-and-drop verified changing the displayed queue order. Mode buttons were verified through all states.

### Legacy Japanese metadata
`metadata-text.js` recovers reversible Shift-JIS or UTF-8 tag bytes mistakenly decoded as Latin-1, guarded by encoding markers, strict decoding and control-character checks. Correct Japanese and ordinary western text are retained. The metadata bundle applies the correction on import; collection startup also repairs saved track display fields while preserving IDs and migrating album artwork associations. Audio files are never rewritten. Tests: `node tests/test-metadata-text.cjs`; all eight supplied MAGIC MP3 titles verified against filenames, and the saved シネマ (instrumental) track and artists verified in browser after migration.

### Emitted waveform
The title waveform records four band-dependent source signals in a bounded time history (`wave-history.js`). Each x position reads an earlier source sample, making beat/loudness changes travel from the circle to the right over three seconds instead of scaling every point simultaneously. Idle breathing, source beats, ribbon thickness/band mapping and free particles remain. Emission smoothing prevents rapid envelope changes from becoming sharp corners; y displacement stays bounded. `node tests/test-wave-history.cjs` verifies delayed onset arrival, interpolation, buffer bounds and reset behavior.

Wave motion refinement: ribbon crests now share a single rightward flow distance, and beat packets translate along the lines with that flow. Band envelopes are sampled by distance, so tempo changes alter travel speed without making the whole line breathe at once. Idle flow continues at a slower speed. The waveform test also checks that a crest keeps its shape when its position advances to the right.

Pressure-driven wave emission: audio waves are now generated by the measured short-time level (RMS envelope), not a continuously amplified sine carrier or BPM clock. Rising energy emits a packet, sustained loud sound emits bounded follow-up packets, and packet size is fixed by level plus per-band energy at birth. Only a faint ambient carrier remains during quiet passages. Beats still pulse the source ring; emitted packets travel right without being resized by later sound. Tests cover silence gating, stronger output for louder input and bounded emission frequency.

### String-style waveform trial
The active title visualization now uses four damped modal strings, excited by positive changes in band energy and RMS. New note energy adds to the existing modal velocities instead of spawning travelling packets. Higher bands use finer spatial modes and faster visible oscillation; low bands retain longer tails. The ends remain fixed and displacement is softly bounded. Idle breathing, source beat pulse and independent particles remain. This is a visual resonance model, with frequencies scaled for readability. `node tests/test-string-wave.cjs` checks excitation, decay, overlapping strikes, dynamic strength and silence damping.

Five-band strings: logarithmic-style audible ranges are 20–80, 80–320, 320–1250, 1250–5000 and 5000–20000 Hz. Byte FFT decibel values are converted to linear power before band averaging, then mapped to a visual envelope. Each string receives its own band attack and amplitude envelope (25 ms attack / 120 ms release); a global loudness rise cannot excite a band with no signal. Line thickness decreases toward higher frequencies. Tests use isolated synthetic FFT tones to check band routing, level response and Nyquist clipping.

### Piano registers and overtone particles
The five string bands now use piano note boundaries: A0–C2 (27.5–130.8128 Hz), C2–C3, C3–C4, C4–C5, and C5–C8 (1046.5023–4186.0091 Hz). Reference: https://jp.yamaha.com/musical-instruments/pianos/explore/about/piano/ . Particle density is driven by a heuristic count of prominent peaks near integer multiples of candidate roots, with tolerance for spectral resolution and slight stretch. This is a visual estimate, not a polyphonic note transcription or exact physical harmonic count. An additional 8192-point analyser is sampled about 9 times per second; the main waveform analyser remains unchanged. Particles still drift independently and fade smoothly; at most 32 are displayed. Reference for harmonic relationships: https://phys.unsw.edu.au/jw/harmonics.html . `node tests/test-overtones.cjs` covers pure tones, harmonic enrichment, unrelated peaks and slightly stretched partials.
# op.



### Windowsのアイコン更新とインストーラー

v0.1.3では実行ファイルを `Op.exe` に変更し、ショートカットのアイコンを同梱の `op-taskbar-v013.ico` に明示的に指定します。旧実行ファイルへのショートカットも移行し、インストール後にWindowsシェルへ関連付け変更を通知します。識別子とユーザーデータの保存先は変更しません。起動中ウィンドウの大小のアイコンを取得する検証はありますが、タスクバー上の実表示そのものを保証するテストとは区別します。

NSISはTauri公式テンプレートをローカルに保持し、白い背景、Op.ロゴ、ピンクの波、Segoe UIと日本語の案内で装飾しています。元テンプレート: https://github.com/tauri-apps/tauri/blob/dev/crates/tauri-bundler/src/bundle/windows/nsis/installer.nsi （MITライセンスを installer/LICENSE-MIT に同梱）。装飾画像は scripts/build-installer-art.ps1 で再生成できます。

## アプリ内アップデート

設定の「アップデート」から自動確認、自動ダウンロード、手動確認を選べます。署名を検証し、適用はユーザーが「更新して再起動」を押したときに行います。公開先と鍵の管理、GitHub Actionsの手順は UPDATES.md を参照してください。配信の公開と新旧バージョン間の実更新検証は別途必要です。

## 視聴と操作の調整（v0.1.6）

再生位置はメディアの時計に合わせて滑らかに更新します。シークのドラッグ中はプレビューを保ち、離した時に一度だけ適用します。シークバー上でも矢印で5秒、Shift併用で30秒移動します。再生終了後の再生ボタンは先頭から再開します。Ctrl/⌘+Kで検索、検索中のEscと「検索をクリア」で条件を解除できます。複数の取り込みは順番に処理し、進捗と失敗件数を表示します。表示と操作の設定では、OSに合わせたモーション軽減と右端キューの自動展開を切り替えられます。これらの表示設定は端末ごとに保存します。

## テーマ

設定 → 表示と操作 → テーマで、白・ピンクのライト、黒・水色のダーク、OS追従を切り替えます。テーマは端末に保存し、起動時の画面読み込み前に復元します。ライト時の設定背景は純白です。波形と背景の色も追従しますが、ジャケットや動画の実画像にはフィルターを掛けません。Windowsのウィンドウテーマも連動させます。

## デスクトップのミニプレイヤー（v0.1.12）

Windowsアプリの「設定 → 表示と操作 → デスクトップにミニプレイヤーを表示」で有効にします（初期状態はオフ）。メイン画面があるモニターの右端に常に手前で表示し、カーソルを合わせると再生操作が開きます。ジャケット、進捗、再生・一時停止、前後の項目、お気に入り、シーク、テーマはメイン画面と同期します。ジャケットを押すとメイン画面を復帰します。×で非表示にでき、設定から再表示できます。メイン画面を最小化したり×で閉じても操作できます。通知領域の「Op. を終了」で完全終了します。独立した音声エンジンは作らず、元の再生を制御します。表示のオン・オフは次回起動時にも引き継ぎます。現段階では右端固定です。

`node scripts/smoke-mini.cjs` は一時プロファイルと生成した音声を使い、実際のWebViewウィンドウの開閉、ホバー、ジャケット共有、再生操作、テーマ同期を確認します。

## ミニプレイヤーのノッチ表示（v0.1.13）

Codenotchのデザイン仕様（https://github.com/vinzdg/codenotch/blob/main/docs/specs/2026-08-28-usage-notch-design.md）を参考に、画面端の逆丸角ノッチと、左側に浮かぶ操作カードに分けました。右端のジャケット位置を保ったままカードが展開し、閉じるときはフェード後にウィンドウを縮めます。再生中の小さなインジケーターはモーション軽減設定に従います。参考元のソースコードや画像は同梱していません。

## バックグラウンド常駐（v0.1.14）

Windowsでは×でメイン画面を閉じると、ウィンドウを隠して通知領域に常駐します。再生とミニプレイヤーは継続します。Op.の通知領域アイコンを左クリックすると画面を復帰し、右クリックメニューから再生・一時停止、ミニプレイヤーの切り替え、完全終了を選べます。終了時は再生位置などの保存を待って終了し、保存エラー時は画面に戻して案内します。「設定 → アプリと保存領域」にも完全終了ボタンを用意しています。二重起動は既存ウィンドウを復帰させ、別の再生エンジンを起動しません。OS起動時の自動起動は追加していません。

ノッチ幅は72から56論理px、操作カードの高さは160から148論理pxに縮小しました。`node scripts/smoke-background.cjs` で通常のウィンドウクローズ、非表示中の音声時計の進行、ミニプレイヤーの維持、二重起動からの復帰、明示的な完全終了を一時プロファイルで検証します。

## ミニプレイヤーの自動切り替え（v0.1.15）

ミニプレイヤーは初期設定でオンです。メイン画面を表示中は隠し、最小化または×で閉じると右端に表示します。メイン画面を復帰すると自動で隠れ、次回は操作カードを閉じた状態で表示します。設定やミニプレイヤーの×で明示的にオフにした場合は、その選択を維持します。

ノッチは56px幅を維持して縦長にし、縦に並べた9本の短い横バーが周波数帯域ごとに幅を変えます。固定の再生アニメーションをやめ、実際のFFT値を使います。無音・停止時は小さく戻り、モーション軽減時は静止します。非表示中はジャケットの変換やミニプレイヤーへの定期送信を止めます。

## 一体型ミニプレイヤーと上下移動（v0.1.16）

吹き出しと隙間をなくし、画面端の本体が横に伸びて再生操作を表示します。透明ウィンドウに付いていたWindowsのネイティブ影・白い縁も無効にしました。上部の小さなつまみ、または操作ボタン以外の余白を上下にドラッグすると、右端に沿って移動します。位置は画面内に収まり、次回起動時にも復元します。つまみにフォーカスして↑↓で調整、Home/Endで上端/下端に配置できます。展開・収納で高さは変わりません。

## ミニプレイヤーのシグネチャ波形（v0.1.17）

縦の棒スペクトラムを、Listenと同じ減衰弦モデルを使った5本の波形に変更しました。太さごとにピアノの音域に対応する周波数帯で反応し、実音のダイナミクスで振幅を調整します。細いノッチ内では縦向きに描き、小さな丸から線が伸びます。停止・無音でもわずかな揺らぎを残し、粒子は音量に合わせて独立して流れます。ミニプレイヤーが隠れている間は描画を停止し、モーション軽減時は静止します。

## ミニプレイヤーの開閉安定化（v0.1.18）

WindowsではWebViewの描画面を384×224論理pxで固定し、開閉時にウィンドウのサイズや座標を変更しません。閉じた状態の不可視領域はネイティブのウィンドウ領域から外し、背後のアプリの操作を遮りません。表示の伸縮は内部の一体型ボディで行います。ネイティブの領域変更は直列化し、閉じる途中に戻ってきたカーソルや非表示中のフレーム待ちで古い処理が後から反映されないようにしました。メイン画面復帰中のミニプレイヤー再表示も抑止します。

## キューとミニプレイヤーの再生操作（v0.1.19）

キューの見出しを簡潔にし、件数・合計時間、コンパクトな行、ホバー時のジャケット再生操作、細いスクロールバーに整理しました。下の詩的な案内文は削除しています。ミニプレイヤーは隠れたメインボタンのクリック経由ではなく、明示的な再生・一時停止の指示を送ります。応答中は連打を抑え、再生状態の世代番号で古い通知によるアイコンの巻き戻りを防ぎます。WindowsのWebViewにはウィンドウ間の操作でもメディアを再生できる同一の再生ポリシーを設定しています。自動的な再生開始は追加していません。

## ファイル関連付けと外部ファイルからの起動（v0.1.20）

Windowsインストーラーは、MP3/FLAC/WAV/M4A/AAC/OGG/Opus、MP4/M4V/WebM/MOV、M3U/M3U8/PLSの14形式をOp.の「プログラムから開く」候補として登録します。音声・動画・プレイリスト用の専用ICOも同梱します。インストール完了ページと「設定 → アプリと保存領域」のリンクからWindowsの既定アプリ設定を開き、対象形式をOp.に設定できます。既存の拡張子の既定値やUserChoiceは上書きしません。

ファイルをダブルクリックして起動すると、現在のライブラリ保存方式に従ってファイルを追加し、再生を始めます。起動済みのOp.にもファイルを渡せます。同じ内容の音源は既存のライブラリ項目を再利用します。ローカルプレイリストは記載順に読み込み、プレイリストとしても保存します。UTF-8/BOM・UTF-16・Shift-JISのプレイリスト、相対/絶対パスとfile: URIに対応します。HTTPストリーミングURLや入れ子のプレイリストは取り込みません。

ファイル読み込み用IPCは、起動引数とプレイリストから受け取ったファイルの読み取り専用ハンドルだけを扱います。任意パスを受け取る汎用ファイルAPIは公開しません。読み込みは4MiBごとに転送してハンドルを解放します。元の音源は変更しません。SQLite/ネイティブ参照への保存基盤移行は、この変更とは別です。

ミニプレイヤーの×は操作パネルをたたむだけに変更しました。表示のオン・オフは設定または通知領域で変更します。過去の×操作ですでにオフになった設定は、自動では書き換えず保持します。

検証: `node scripts/check-file-associations.cjs`（インストーラー生成後）、`cargo test --manifest-path src-tauri/Cargo.toml --lib open_files`、`node scripts/smoke-file-open.cjs`。インストーラーの実行とWindowsでの既定アプリ選択はユーザーが行います。


<!-- op-language-note -->
## 表示言語

日本語と英語に対応しています。Windowsインストーラーの開始時、またはアプリの「設定 → 外観 → 言語 / Language」から変更できます。言語を変えても音楽再生は継続します。


## 利用許諾

Op.独自部分は、個人・業務とも無料で利用できる[独自の無料利用許諾](LICENSE.txt)で提供します。支援は任意です。ソースコードの一般的な改変・再配布・他製品への再利用などは許可していません。第三者ライセンス、法令、プラットフォーム規約による権利は別扱いです。

第三者の表記・ライセンス・ソース入手先は[第三者ライセンス一覧](licenses/THIRD_PARTY_NOTICES.txt)を参照してください。公開ソースを非公開にする設定とは別です。

<!-- op-support:start -->
## ☕ Op.の開発支援

Op.は、音楽と音への情熱から生まれた、個人開発の完全無料メディアプレイヤーです。

Op.を気に入っていただけたら、コーヒー1杯で開発を応援してもらえるとうれしいです。支援は完全に任意です。支援の有無にかかわらず、すべての機能を無料で利用できます。

[☕ Buy me a coffee](https://buymeacoffee.com/shirone_astral)
<!-- op-support:end -->
