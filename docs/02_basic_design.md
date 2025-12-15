# 基本設計段階成果物

## システム構成図

![システム構成図4_finish.drawio.png](images/%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E6%A7%8B%E6%88%90%E5%9B%B34_finish.drawio.png)

## ソフトウェア構成図

![ソフトウェア構成図4_finish.drawio.png](images/%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E6%A7%8B%E6%88%90%E5%9B%B34_finish.drawio.png)

## DFD

DFDに記述する内容であるデータの流れは、ソフトウェア構成図の中のin/outの記述と、データ自体がすべて制御部を通ることと、外部エンティティは利用者と迷路のみであることから今回は作江精をしなかった。

## 通信プロトコル仕様書

### **通信プロトコル仕様書**

### **1. 概要**

本書は、災害救助ローバー実験システムにおける、以下の2つの通信インターフェースのプロトコル仕様を定義する。

1. Raspberry Pi - ESP32 間のUDP通信
2. 外部PC - Raspberry Pi 間のWeb API (HTTP/WebSocket)

### **2. a. Pi ↔ ESP32間 通信仕様 (UDP)**

### **データフォーマット**

| フィールド名 | データ型 | 説明 |
| --- | --- | --- |
| command | string | コマンド種別。"set_path" や "stop" など。 |
| path | array of object | 経路座標の配列。 |
| path.x | integer | 経路上の点のX座標。 |
| path.y | integer | 経路上の点のY座標。 |

### **JSONコード例**

JSON

{

"command": "set_path",

"path": [

{"x": 100, "y": 50},

{"x": 100, "y": 200},

{"x": 300, "y": 200}

]

}

### **3. b. PC ↔ Pi間 通信仕様 (HTTP/WebSocket)**

### **HTTP APIエンドポイント (リスト形式)**

- POST /api/control/start
- **説明:** ローバーの自律走行を開始する。
- **ボディ:** なし
- POST /api/control/stop
- **説明:** ローバーを緊急停止させる。
- **ボディ:** なし

### **WebSocket メッセージ**

PiからPCへ送るステータス情報のJSONフォーマット

JSON

{

"type": "status_update",

"rover_status": "走行中",

"current_position": {"x": 100, "y": 150},

"video_frame": "data:image/jpeg;base64,/9j/4AAQ..."

}