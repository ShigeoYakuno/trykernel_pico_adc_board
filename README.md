# trykernel pico　ADCボード

## ADC選定
- 市販ADCの入手性から、下記の3つに絞った
- HX711は通信方式が疑似SPIでpicoで未対応のインタフェースだった
- ビッドバンギングで疑似SPIを作るとオーバヘッド大きいので採用を見送った
- あとはコストとのバランスでNAU7802を採用した。

![image](https://github.com/user-attachments/assets/ffa3315b-0303-479c-87c2-52053b5eece5)


## NAU7802 TIPS
- データシートより以下の2点の注意点が分かった
- 
1. AVDDはDVDDを上回ってはいけない
2. I2Cの電圧(DVDD)は、接続するマイコンと同じ電圧を推奨する

- マイコンはpicoなので、DVDDは3.3Vとした。
- AVDDも3.3V以下の縛りの中、なるべく出力を大きくしたいので、3.3Vとした。
- 2番目はI2Cラインだけの問題なので、レベルシフタ回路を使えば解決できそうではあるが、、3.7Vリポバッテリーでの駆動を考えているので、素直にLDOを介して3.3Vに落として使うことにした。
- チャージポンプ(倍電圧回路)で3.7V➡7.4Vに変換し、I2Cラインはレベルシフタを搭載し、AVDDとDVDDを5V系で構成する案も考えたが、まずはシンプルな案で様子見をする。

## アナログ回路部
- DVDDとAVDDは同じ3.3Vだが攻勢を分ける。DVDDはpico内蔵の電源から供給される3.3Vを使う
- AVDDはノイズ影響を懸念して、3.7VからLDOで3.3Vを作る
- リファレンス回路とセンサの差動入力回路をAVDDから構成電圧が変動しても追従するように設計(レシオメトリック)

### リファレンス回路

![image](https://github.com/user-attachments/assets/26a00a84-744d-4a43-a4fc-b1ceb698f6e3)

- 精密金属皮膜抵抗(誤差1%)使用。
- 3.3Vから分圧で541mVをFSRとする。
- コンデンサもC0G特性を使用する。

### アンチエイリアス回路
- fcは380Hzで設計

- ![image](https://github.com/user-attachments/assets/8c5a9b93-fd61-498f-b153-58ae25ddef63)

- NAU7802のOSRが分からないが、FMOD=200kHzと見積もってナイキスト周波数(100KHz)で50dB付近まで落ちる計算

![image](https://github.com/user-attachments/assets/f7d55b93-96ab-4cbe-bc4b-b9afe964bf2a)




