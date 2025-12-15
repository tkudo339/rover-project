# 詳細設計成果物

## タスク構成図

- ローバー側

![タスク構成図-本当のローバー部.drawio.png](images/%E3%82%BF%E3%82%B9%E3%82%AF%E6%A7%8B%E6%88%90%E5%9B%B3-%E6%9C%AC%E5%BD%93%E3%81%AE%E3%83%AD%E3%83%BC%E3%83%90%E3%83%BC%E9%83%A8.drawio.png)

- 疑似上空カメラ側

※本来は静的な図であるが、今回は流れのわかりやすさを考慮して、一回目の処理と二回目以降のループを併記している。

![タスク構成図-ローバー側.drawio.png](images/%E3%82%BF%E3%82%B9%E3%82%AF%E6%A7%8B%E6%88%90%E5%9B%B3-%E3%83%AD%E3%83%BC%E3%83%90%E3%83%BC%E5%81%B4.drawio.png)

## 詳細設計記述書

- カメラ映像・画像取得モジュール
    - 概要
    
    カメラで直下に位置する迷路を撮影してその画像・映像を取得、他のモジュールに送信する。
    
    - 入力
    
    システム制御部からの撮影開始コマンド
    
    - 出力
    
    撮影した画像・映像データ
    
    - 内部処理ロジック
        1. システム制御部から撮影開始コマンドを受け取る。
        2. setup_camera()を呼び出し、カメラを初期化する。
        3. ループ処理を開始し、一定間隔でcapture_image()を呼び出して映像を更新し続ける。
        
    - 主要な関数・変数
    
    関数：setup_camera(),capture_image(),get_latest_image()
    
    変数：latest_frame（Mat形式）
    
    - 備考
    
    仕様予定ライブラリ：picamera2
    
- 画像認識・画像処理モジュー
    - 概要
    
    カメラ映像・画像を経路計算が行えるように処理して出力する
    
    - 入力
    
    カメラ映像・画像
    
    - 出力
    
    maze_array（二次元配列）、webApp上で確認するログ用：gray_iamge、binary_iamge、thin_image
    
    - 内部処理ロジック
        1. simplify_image()で画像をグレースケール化・平滑化、gray_iamgeを生成
        2. binarize_image()で画像を二値化、binary_iamgeを生成
        3. thin_image()で迷路の細線化、thin_imageを生成
        4. build_maze_array()で細線化画像から二次元配列を生成
        
    - 主要な関数・変数
    
    関数：simplify_image(),binarize_image(),thin_image(),build_maze_array()
    
    変数：maze_array（二次元配列）、webApp上で確認するログ用：gray_iamge、binary_iamge、thin_image
    
    - 備考
    
    仕様予定ライブラリ：OpenCV
    
- 経路計算モジュール
    - 概要
    - path_waypoints（ウェイポイント座標リスト）と現在位置座標を受け取り、ローバーへの走行命令を出力する。
        - 入力
    - 数値型二組のリスト：path_waypoints{x座標、y座標}
    - 数値型；の組：current_point{x座標、y座標}
        - 出力
    - 数値型：走行命令（停止命令：０、前進命令：１、右旋回：２、左旋回：３）
        - 内部処理ロジック
            1. pathpointsのcount番目の組を取得。countは初期値０
            2. pahtpoints[count].x-current.x == 0
    
    1.pathpoints.y -  currentpoint.y  > 0 ：order = 2
    
    1. reconstruct_path()で、探索結果からゴールからスタートまで遡り、最終的な経路の座標リスト（path_waypoints）を生成する。
    - 主な関数・変数
        
        関数：find_start_goal(), execute_astar(), reconstruct_path()
        
    
    変数：path_waypoints（座標リスト）、open_list、closed_list
    
    - 備考
    - 使用予定ライブラリ：なし（A*アルゴリズムは自前で実装）
    - path_waypointsは旋回が必要な座標までのリストである。迷路で言えば曲がり角や分岐を指す。
- 走行指示モジュール
    
    概要
    path_waypoints（ウェイポイント座標リスト）と現在位置座標を受け取り、ローバーへの走行命令を出力する。
    入力
    数値型の組のリスト：path_waypoints{x座標、y座標}
    数値型の組：current_point{x座標、y座標}
    数値型：current_direction
    出力
    数値型：走行命令（停止命令：０、前進命令：１、右旋回：２、左旋回：３）
    内部処理ロジック
    
    ![走行指示モジュールフローチャート-ページ3.drawio.png](%E8%B5%B0%E8%A1%8C%E6%8C%87%E7%A4%BA%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%83%95%E3%83%AD%E3%83%BC%E3%83%81%E3%83%A3%E3%83%BC%E3%83%88-%E3%83%9A%E3%83%BC%E3%82%B83.drawio.png)
    
    備考
    path_waypointsは旋回が必要な座標までのリストである。迷路で言えば曲がり角や分岐を指す。
    
    概要
    path_waypoints（ウェイポイント座標リスト）と現在位置座標を受け取り、ローバーへの走行命令を出力する。
    入力
    数値型の組のリスト：path_waypoints{x座標、y座標}
    数値型の組：current_point{x座標、y座標}
    数値型：current_direction
    出力
    数値型：走行命令（停止命令：０、前進命令：１、右旋回：２、左旋回：３）
    内部処理ロジック
    
    備考
    path_waypointsは旋回が必要な座標までのリストである。迷路で言えば曲がり角や分岐を指す。
    
