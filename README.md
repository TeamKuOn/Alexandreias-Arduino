# Alexandreias

## 1.Overview
テストコードやサンプルコード専用のプロジェクトを管理するリポジトリである．
マイコンやセンサーの動作確認や，ライブラリの動作確認などを行う．
マイコンの開発環境には PlatformIO 環境を用いているため，事前に環境構築を行う必要がある．


こういった「システムの部品」を蓄積することで，システム作りの際に「部品を組み合わせる」だけで済み，開発の効率が上がることを期待している．

このリポジトリには，動作するプログラムのみpushすることを許可する．

---

## 2.Directory 

ディレクトリ構造を以下に示す．
```
.
├── Arduino
└── Raspberry-pi
```

---
## 3.Branch
必ず"develop"ブランチから各自の名前の名前のブランチを作成し（```$ git branch```），管理する．
ブランチ名は利用者が容易に特定できるものであれば，本名でもニックネームでも何でも良い．

**Example**

```
user@MacBook:~/workspace/software-parts$ git branch
  master
  develop
* wasou 
```

---

## 4. About PlatformIO
PlatformIO は，マイコンの開発環境を構築するためのツールである．

### 4.1. PlatformIO のインストール
PlatformIO は，Python で書かれているため，Python がインストールされている必要がある．
Python は，以下のコマンドでインストールできる．
```
$ brew install python
```

PlatformIO は，Python のパッケージ管理ツールである pip を用いてインストールする．
```
$ pip install -U platformio
```

### 4.2. PlatformIO の使い方
PlatformIO は，コマンドラインツールである．
以下のコマンドで，PlatformIO のヘルプを表示できる．
```
$ platformio --help
```

PlatformIO は，プロジェクトを作成することで，開発環境を構築する．
以下のコマンドで，プロジェクトを作成する．
```
$ platformio init 
```

使用するマイコンがあれば，以下のコマンドで，プロジェクトを作成する．
```
$ platformio init --board <board ID>
```

ボードID は，以下のコマンドで確認できる．
```
$ platformio boards
```

例として，Arduino Mega 2560 を使用する場合は，以下のコマンドで Arduino Mega 2560 のボードID を調べることができる．
```
$ platformio boards mega2560


Platform: atmelavr
======================================================================================================================
ID                           MCU         Frequency    Flash    RAM    Name
---------------------------  ----------  -----------  -------  -----  ------------------------------------------------
ATmega2560                   ATMEGA2560  16MHz        256KB    8KB    ATmega2560
megaADK                      ATMEGA2560  16MHz        248KB    8KB    Arduino Mega ADK
megaatmega2560               ATMEGA2560  16MHz        248KB    8KB    Arduino Mega or Mega 2560 ATmega2560 (Mega 2560)
controllino_maxi             ATMEGA2560  16MHz        248KB    8KB    Controllino Maxi
controllino_maxi_automation  ATMEGA2560  16MHz        248KB    8KB    Controllino Maxi Automation
controllino_mega             ATMEGA2560  16MHz        248KB    8KB    Controllino Mega
fysetc_f6_13                 ATMEGA2560  16MHz        252KB    8KB    FYSETC F6 V1.3
prusa_rambo                  ATMEGA2560  16MHz        252KB    8KB    Prusa RAMBo
reprap_rambo                 ATMEGA2560  16MHz        252KB    8KB    RepRap RAMBo
sparkfun_megapro8MHz         ATMEGA2560  8MHz         252KB    8KB    SparkFun Mega Pro 3.3V/8MHz
sparkfun_megapro16MHz        ATMEGA2560  16MHz        248KB    8KB    SparkFun Mega Pro 5V/16MHz
sparkfun_megamini            ATMEGA2560  8MHz         252KB    8KB    SparkFun Mega Pro Mini 3.3V
```

プロジェクトを作成すると，以下のディレクトリ構造になる．
```

├── .gitignore
├── .piolibdeps
├── .pioenvs
├── .piopackages
├── .vscode
├── include
├── lib
├── platformio.ini
└── src
```

### 4.3 Build

プロジェクトのディレクトリに移動し，以下のコマンドで，プログラムをビルドする．
```
$ platformio run
```

ビルドが成功すると，以下のディレクトリにビルドされたプログラムが生成される．
```
├── .pioenvs
│   └── megaatmega2560
│       ├── firmware.hex
│       ├── firmware.elf
│       ├── firmware.map
│       ├── firmware.eep
│       └── firmware.bin
```

### 4.4 Upload & Monitoring

ビルドされたプログラムは，マイコンに書き込むことができる．
以下のコマンドで，マイコンに書き込むことができる．
```

$ platformio run --target upload
```

動作の確認は Aruino IDE と同様に，シリアルモニタを用いることができる．
以下のコマンドで，シリアルモニタを起動する．
```
$ platformio device monitor
```

#### 4.4.1 Upload on WSL2

WSL 上で PlatformIO を使用する場合は，usbipd-win を利用することで Windows を経由してマイコンに書き込むことができる．

WSL2 には，まだUSBのシリアルを直接扱う機能は無いため直接書き込むことはできないが，Windows を介してアクセスする方法が提供されている．
##### Install usbipd-win
以下のリポジトリからファイルをダウンロードし， usbipd-win をインストールする．

<a href="https://github.com/dorssel/usbipd-win/releases">dorssel / usbipd-win</a>

##### Linux に USBIP ツールとハードウェア データベースをインストールする
USB/IP プロジェクトのインストールが完了したら、ユーザー空間ツールと USB ハードウェア識別子のデータベースをインストールする必要があります。

```
sudo apt install linux-tools-5.4.0-77-generic hwdata
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/5.4.0-77-generic/usbip 20
```

この時点で、USB デバイスを共有するために Windows でサービスが実行され、共有デバイスに接続するために必要なツールが WSL にインストールされます。

##### デバイスをWindows側でアタッチ

Arduino を接続後，管理者権限で実行している Windows PowerShell にて以下のコマンドで BusID を確認する．
```
PS C:\Users\wasou> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-1    062a:4106  USB Input Device                                              Not attached
2-2    056e:1058  USB Input Device                                              Not attached
2-4    2341:0042  Arduino Mega 2560 (COM6)                                      Not attached
2-11   048d:6006  USB Input Device                                              Not attached
2-13   04f2:b71a  HD Webcam, IR Camera                                          Not attached
2-14   8087:0026  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
```

今回は BusID は ```2-4``` なので，以下のコマンドでアタッチする．

```
PS C:\Users\wasou> usbipd wsl attach --busid 2-4
usbipd: info: Using default distribution 'Ubuntu'.
```

アタッチが成功すると，以下のように表示される．

```
PS C:\Users\wasou> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-1    062a:4106  USB Input Device                                              Not attached
2-2    056e:1058  USB Input Device                                              Not attached
2-4    2341:0042  Arduino Mega 2560 (COM6)                                      Attached - Ubuntu
2-11   048d:6006  USB Input Device                                              Not attached
2-13   04f2:b71a  HD Webcam, IR Camera                                          Not attached
2-14   8087:0026  インテル(R) ワイヤレス Bluetooth(R)                            Not attached
```

デタッチする場合は，以下のコマンドを実行する．

```
PS C:\Users\wasou> usbipd wsl detach --busid 2-4
```

接続の確認は WSL 上でも以下のコマンドを実行する．

アタッチ前
```
wasou@DESKTOP-R2V8MP0:~/Kuon/Alexandreias/Arduino/DHT11_Temp_Humid_sensor$ dmesg | grep tty
[    0.049766] printk: console [tty0] enabled
```

アタッチ後
```
wasou@DESKTOP-R2V8MP0:~/Kuon/Alexandreias/Arduino/DHT11_Temp_Humid_sensor$ dmesg | grep tty
[    0.049766] printk: console [tty0] enabled
[  626.012936] cdc_acm 1-1:1.0: ttyACM0: USB ACM device
```


#### Permission Error

以下のエラーが出た場合は，WSL 上にて以下のコマンドで，権限を変更する．

```
*** [upload] could not open port /dev/ttyACM0: [Errno 13] Permission denied: '/dev/ttyACM0'
```

```
wasou@DESKTOP-R2V8MP0:~/Kuon/Alexandreias$ sudo chmod 666 /dev/ttyACM0
```


### 4.5 Library
ライブラリの設定は，platformio.ini に記述するか，コマンドラインで指定する．

使用するライブラリがあれば，以下のコマンドで，ライブラリの名前を確認し，インストールする．
```
$ platformio lib search <library name>
$ platformio lib install <library name>
```

例として，LiquidCrystal を使用する場合は，以下のコマンドで，ライブラリの名前を確認する．
```
$ platformio lib search FreeRTOS

Found 73 packages (page 1 of 8)

mincrmatt12/STM32Cube Middleware-FreeRTOS
Library • 10.3.1+f4-1.26.1 • Published on Tue Apr 27 13:22:05 2021
This library links in the version of FreeRTOS shipped with the STM32Cube framework. 

ehubin/FreeRTOS-libopencm3
Library • 10.2.0 • Published on Tue Jul  2 22:34:58 2019
This is a packaged ...

.....

feilipu/FreeRTOS
Library • 10.4.6-8 • Published on Sun Aug  7 13:08:33 2022
<h3>FreeRTOS Real Time Operating System implemented for AVR (Uno, Nano, Leonardo, Mega).</h3>. The primary design goals are: Easy to use, Small footprint, Robust. Uses Watchdog Timer for 15ms resolution. Slow blink = stack overflow. Fast blink = heap malloc() failure.
```

ライブラリの名前を確認したら，以下のコマンドで，ライブラリをインストールする．
```
$ platformio lib install feilipu/FreeRTOS
```
