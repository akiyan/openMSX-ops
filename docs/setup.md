# 導入

Linux で C++23 対応の GCC、make、Python 3 と、SDL2・SDL2_ttf・libpng・Tcl 8.6・zlib・OpenGL・GLEW・ALSA の開発用パッケージを用意する（Ubuntu では `libsdl2-dev libsdl2-ttf-dev libpng-dev tcl-dev zlib1g-dev libgl-dev libglew-dev libasound2-dev`）。
画面のない環境では `xvfb-run`（`xvfb`）を使う。Laserdisc（Ogg・Theora・Vorbis）は不要。

```sh
git clone --branch akiyan https://github.com/akiyan/openMSX.git openmsx
cd openmsx
./configure
make -j8
python3 tests/akiyan/midi_out_smf.py
python3 tests/akiyan/record_pause.py
python3 tests/akiyan/record_loadstate.py
```

実行ファイルは `derived/x86_64-linux-opt/bin/openmsx` にできる。試験は `--binary` で別の実行ファイルも指定できる。
22.0 の `meson.build` は存在しないファイルを参照して設定に失敗するため、上流の `configure` と `make` を使う。

## 時刻つきの MIDI 出力

`midi-out-smf` は MIDI 出力を Standard MIDI File（フォーマット 0）に書き出す。
各メッセージには、最後のバイトが届いたときのエミュレーター内の時刻を、接続した時点からの経過として付ける（1 tick = 20 µs、25000 PPQ・120 bpm）。
`set throttle off` で実時間より速く動かしても演奏の時刻は崩れない。 [Issue #1](https://github.com/akiyan/openMSX/issues/1)

```tcl
set midi-out-smf-filename /absolute/path/to/out.mid
plug MSX-MIDI-out midi-out-smf
```

- 先頭に、テンポと、tick 0 のエミュレーター内の時刻を書いたマーカー（`openMSX emutime 秒`）を置く。同じ実行の `record` や `machine_info time` と合わせるときに使う。
- メッセージごとにトラック終端と長さを書き直すため、実行中や強制終了の後でも完全な SMF として読める。
- SysEx は `F0 長さ …` で記録する（256 バイトを超えると後続を `F7` で記録）。リアルタイムメッセージ（F8〜FF）は記録しない。
- ステートの読み込みや巻き戻しで時刻が戻ったときは差分 0 として続ける。ステートには接続を保存しない（`midi-out-logger` と同じ）。

書き出した SMF は FluidSynth などで音声に変換する。

```sh
fluidsynth -ni -F out.wav -r 44100 FluidR3_GM.sf2 out.mid
```

## ALSA の MIDI 出力

上流の機能。ALSA シーケンサーのポートは、クライアント名が pluggable 名になる（例：`plug MSX-MIDI-out {FLUID Synth (fsmidi)}`）。
実時間で送るため、高速実行には `midi-out-smf` を使う。
