# openMSX-ops

`openMSX/openMSX` を直接フォークした `akiyan/openMSX` の変更情報、導入手順、検証記録を管理する。
実装と試験、機能や不具合の Issue は本体に置く。 [openMSX 本体](https://github.com/akiyan/openMSX/tree/akiyan) · [Issue](https://github.com/akiyan/openMSX/issues)

既定ブランチの `akiyan` は、固定した上流コミットに独自差分を 1 コミット載せる。
初回は openMSX 22.0 を基準に、エミュレーター内の時刻つきで MIDI 出力を SMF に書き出す pluggable を加えた。 [変更情報](openmsx_akiyan_fork.md) · [上流 22.0](https://github.com/openMSX/openMSX/releases/tag/RELEASE_22_0)
2026-11-25、一時停止や録音の開始・停止のたびに `record` の音が欠ける不具合を直した。同じ日に、その修正で `loadstate` と `reverse` が落ちる不具合を直した。

AI の自動操作や高速な反復（スクリプト化、高速実行、観測、録画）の効率がよくなる機能を、特定のタイトルに依存しない汎用の機能として加える。

- [導入と試験](docs/setup.md)
- [上流への追従](docs/upstream-sync.md)
- [初回の検証記録](releases/2026-11-24.md)
- [録音の修正の検証記録](releases/2026-11-25.md)
- [読み込みの修正の検証記録](releases/2026-11-25-2.md)

ここに置く文書は MIT ライセンス。本体のライセンスは上流（GPL-2.0）に従う。
試験は C-BIOS と ROM を必要としない拡張で行い、市販機の ROM を使わない。 [文書のライセンス](LICENSE) · [本体のライセンス](https://github.com/akiyan/openMSX/blob/akiyan/doc/GPL.txt)
