## マインクラフト リアル鉄道MOD（RTM）dockerサーバ 起動手順

### 動作環境
Docker Desktop for Windows がインストールされているPC、Windows10、11で試しました。  
Windowsのファイル領域（ファイルマウント）されていると遅くなる現象があるため、WSL内にdockerを配置してWSLからdocker起動することをおすすめします。  
Forge 1.12.2 の設定をしてRTM2系利用しています。  

### 設定済みとしてスキップしているもの
* RTMのローカルPC設定
* Docker Desktop for Windowsのインストール・設定

### 手順
* Ubuntuより、docker-compose.ymlがあるフォルダに入り、docker起動
```
# WSL Ubuntuなどから
docker-compose up -d
```

* Powershell、コマンドプロンプトなどでdocker起動したWindows PCのIPを確認
```
# Powershellの例
(Get-WmiObject Win32_NetworkAdapterConfiguration).IPAddress
```

* マイクラマルチプレイで「Docker マイクラRTMサーバー」に接続
上記で調べたIP:Portでサーバログイン
```
# 例
192.168.50.215:25565
```

* 各ユーザーでマルチプレイログインするとdocker-compose.ymlと同じフォルダ内に以下uuid, nameが分かるファイルが出来るので./data/ops.jsonでユーザーに管理者権限を付ける
```
# WSL配下で以下コマンドを実行することでops.jsonの管理者権限設定をする
cat ./data/usercache.json | jq '. | del(.[].expiresOn) | .[].level=4 | .[].bypassesPlayerLimit=false' > ./data/ops.json
```

こんな感じでops.jsonが書き込まれていればOK
```
[
  {
    "name": "musuko",
    "uuid": "0c96926b-e5af-3e53-a2aa-095c541bc747",
    "level": 4,
    "bypassesPlayerLimit": false
  },
  {
    "name": "oreore",
    "uuid": "c5efee69-a0cc-3b43-974a-484e432fcacb",
    "level": 4,
    "bypassesPlayerLimit": false
  }
]
```
### ops.json 補足
RTMオブジェクトを配置するためにはops.jsonを設定しないと、警告がでてレールなどが配置できない。  

### 動機
鉄オタ息子と一緒にマイクラRTM（リアル鉄道MOD）で遊ぶため、お手軽な「ダイレクト接続」で息子のPCのマイクラにアクセスしたところ、息子が作った電車がどうしても見えない現象に頭を悩まされました。  
そこで次に無料マイクラサーバのAternosで表示されない現象が解決されないか試してみたところ、電車が表示され見れるようになりました。  
ただ、AternosはRealTrainMod、NGTLibはmod設定できますが、電車アイテムmodがAternosにアップロード出来ないため、他の車輌を試すことができません。  
ということで、ローカルPCのDockerでさくっとマイクラサーバー立てるため、このリポジトリを作成しました。  
Dockerのマイクラサーバでは特に問題なく、RTMマルチプレイできています。  

### ネットワーク設定について
Wifi LAN内のネットワーク経由で接続できればよかったので、インターネット経由の設定になっていません。  
