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
- アセンブリからバイナリが生成されてるのが視覚的にわかって楽しかった
- multはいけなさそうに見えたが、いけた
- fillはわからん
  - そのうちやる
### Chapter05
- Memory
  - RAMをちゃんと理解することが大事そう
  - RAMのアドレスは2^14なのに、なんでメモリのアドレスは2^15まで受けられるの？
  - RAMとSCREENの両方にloadを通すのはNG
    - 仕様どおりにアドレスの値で分岐させる
  - アドレスの値によってoutがRAM,SCREEN,KEYBOARDなのかが変わるため、アドレスで分岐させる
    - 2進でアドレスの値を書いてみるといけた
  - Mux4Wayとかでもいけそう？
- CPU
  - 4章にあるA命令、C命令の理解が重要そう（C命令はALUと対応？）
    - C命令で、a=0のときはA、a=1のときはMになる
    - C命令で、DはALUのxに対応、A,Mはyに対応 -> aはALUのy入力に使う？
    - c1\~c6はそれぞれ、ALUのzx\~noに対応？ -> してそう！！
  - [すばらしい表](https://nihemak.hatenablog.com/entry/2019/04/28/150541#CPU)があったので参考にさせていただきました

  | bit  | A命令 | C命令|
  | :--: | :-:   | :-   |
  | 15 | 0 | 1 |
  | 14 | v | 1 |
  | 13 | v | 1 |
  | 12 | v |a : ALUのy |
  | 11 | v |c1 : zx |
  | 10 | v |c2 : nx |
  | 9  | v |c3 : zy |
  | 8  | v |c4 : ny |
  | 7  | v |c5 : f |
  | 6  | v |c6 : no |
  | 5  | v |d1 : Aレジスタへ保存 |
  | 4  | v |d2 : Dレジスタへ保存 |
  | 3  | v |d3 : Memory[A]へ保存 |
  | 2  | v |j1 : ALUout < 0 |
  | 1  | v |j2 : ALUout = 0 |
  | 0  | v |j3 : ALUout > 0 |
  - PCのloadは、ALUのzr,ngとj123のAndをとらないとうまくいかない（常にloadしてしまう）
    - j123の仕様がよくわかってない
  - BuiltInChipを使わないとエラーになった
- Computer
  - 実装は問題なし
  - tstとoutを見てみると理解が深まった
    - *-external.tstとはいったい..？
  - とてもすごい＆感動
- CPU実装がとても苦しかったが、勉強になった