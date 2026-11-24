# openMSX akiyan fork の変更情報

直上流は `openMSX/openMSX`。
初回の基準は 22.0（`RELEASE_22_0`）の `13007fee0b46eaa545b5d254aa1808950de821a5`。
独自コミットのメッセージはこのページの URL とする。 [本体](https://github.com/akiyan/openMSX/tree/akiyan) · [基準コミット](https://github.com/openMSX/openMSX/commit/13007fee0b46eaa545b5d254aa1808950de821a5)

## AI エージェント向け操作の比較

**✗ 非対応　△ 実時間のみ　◯ 自動操作可**

| 操作 | 公式本家 22.0 | akiyan |
|---|:---:|:---:|
| MIDI 出力をホストの MIDI ポートへ送る（ALSA など） | △ | △ |
| MIDI 出力の生のバイト列をファイルへ記録（`midi-out-logger`） | ◯（時刻なし） | ◯（時刻なし） |
| MIDI 出力をエミュレーター内の時刻つきで SMF に記録（`throttle off` でも崩れない） | ✗ | **◯** `midi-out-smf` |

本家から引き継いだ自動操作の経路は Tcl スクリプト（`-script`）、`-control stdio`、`throttle`、セーブステート、`debug read/write`、`record` など。 [本家のマニュアル](https://openmsx.org/manual/)

## 時刻つきの MIDI 出力（`midi-out-smf`）

MIDI 出力の接続先に `midi-out-smf` を加えた。Standard MIDI File（フォーマット 0）に書き出す。
各メッセージに、最後のバイトが届いたときのエミュレーター内の時刻を付ける（接続時点が tick 0、1 tick = 20 µs）。
ホストの実時間に依存しないため、実時間より速く動かした実行でも演奏の時刻を残せる。
tick 0 のエミュレーター内の時刻をマーカーに書き、同じ実行の録画と合わせられる。
メッセージごとに完全な SMF として書き直し、強制終了の後でも読める。 [仕様](docs/setup.md#時刻つきの-midi-出力) · [Issue #1](https://github.com/akiyan/openMSX/issues/1)
