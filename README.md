# nand2tetris
## Setup
- ソフトウェアをダウンロード https://www.nand2tetris.org/software<br>
- javaをいれてなかったので`sudo apt install default-jre`<br>
- 4Kモニターだどシミュレータ画面が小さすぎたため、[これ](http://nand2tetris-questions-and-answers-forum.52.s1.nabble.com/4k-resolution-not-DPI-aware-td4030046.html)をみて、HardwareSimulator.shのjavaコマンドに`-Dsun.java2d.uiScale=2`オプションを追加
```
....
java -Dsun.java2d.uiScale=2 -classpath "${CLASSPATH}:bin/classes:BuiltIn:bin/lib/Hack.jar:bin/lib/HackGUI.jar:bin/lib/Simulators.jar:bin/lib/SimulatorsGUI.jar:bin/lib/Compilers.jar" HardwareSimulatorMain &
....
```
## Memo
### Chapter01
- .hdlでは変数名に`_`は使えないらしい
- Muxのときにカルノー図を書いてみたらきれいにできた
### Chapter02
- ALUでつまった
  - 疑似コードを「そのまま」実装したらいけた
  - HDLの文法はちゃんとよもう（戒め）
### Chapter03
- カウンタの実装がよくわからない
  - 結局、他の方の実装をみてやった
  - なぜ動かないのかが不明
  ```
  // これは動いて
  Inc16(in = regout, out = inc16out);
  Mux16(a = regout, b = inc16out, sel = inc, out = incout);
  Mux16(a = incout, b = in, sel = load, out = loadout);
  Mux16(a = loadout, b[0..15] = false, sel = reset, out = resetout);
  Register(in = resetout, load = true, out = out, out = regout);

  // これは動かない
  Mux16(a = regout, b[0..15] = false, sel = reset, out = resetout);
  Mux16(a = resetout, b = in, sel = load, out = loadout);
  Inc16(in = loadout, out = inc16out);
  Mux16(a = loadout, b = inc16out, sel = inc, out = incout);
  Register(in = resetout, load = true, out = out, out = regout);
  ```
  - cmpを見た感じ、reset=1のときはload,incにかかわらずresetで、load=1のときはincにかかわらずloadとなっている。そのため、reset>load>incの順に強く、弱い方から書かないとうまく動かない（と思われる）
### Chapter04
- multはいけなさそうに見えたが、いけた
- fillはわからん
  - そのうちやる