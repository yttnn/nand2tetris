# nand2tetris
## setup
ソフトウェアをダウンロード https://www.nand2tetris.org/software<br>
javaをいれてなかったので`sudo apt install default-jre`<br>
4Kモニターだどシミュレータ画面が小さすぎたため、[これ](http://nand2tetris-questions-and-answers-forum.52.s1.nabble.com/4k-resolution-not-DPI-aware-td4030046.html)をみて、HardwareSimulator.shのjavaコマンドに`-Dsun.java2d.uiScale=2`オプションを追加
```
....
java -Dsun.java2d.uiScale=2 -classpath "${CLASSPATH}:bin/classes:BuiltIn:bin/lib/Hack.jar:bin/lib/HackGUI.jar:bin/lib/Simulators.jar:bin/lib/SimulatorsGUI.jar:bin/lib/Compilers.jar" HardwareSimulatorMain &
....
```
## memo
### Chapter01
- .hdlでは変数名に`_`は使えないらしい
- Muxのときにカルノー図を書いてみたらきれいにできた