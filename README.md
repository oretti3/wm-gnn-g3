# グラフニューラルネットワーク

## developer

- __olender
- AkiraH4shimoto
- taiki_astro

## ECRL 環境構築まで
ECRL を実行するための環境構築手順を説明します。

---

### 必要なコンポーネント

1. Dockerfile  
   このリポジトリに含まれています。  
   `isaac-gym` の `docker` フォルダへコピーして使用します。

2. Isaac Gym  
   外部からダウンロードする必要があります。  
   以下のリンクからダウンロードしてください：
   https://developer.nvidia.com/isaac-gym

3. ECRL  
   ECRLリポジトリを外部からクローンします。

4. ECRL 用の重みファイル  
   `latent_rep_chkpts` フォルダに必要な重みを格納します。

---
### ディレクトリ構成
isaac-gym/  
├── docker/                     # このフォルダに Dockerfile をコピー  
├── python/  
│   └── ECRL/                   # ECRL リポジトリをクローン  
│       ├── latent_rep_chkpts/  # 重みファイルを格納  
│       │   ├── dlp_push_5c/  
│       │   ├── dlp_push_6c/  
│       │   ├── dlp_push_T/  
│       │   ├── vae_push_123C/  
│       │  
...


---
### 構築手順

1. Isaac Gym のセットアップ
   - Isaac Gym をダウンロードし、任意の場所に展開します。
   - 展開したディレクトリに移動します：  
     cd isaac-gym
   - 本リポジトリのdockerファイルを上書き保存します。
   - 必要な権限を付与します：  
     chmod +x ./docker/build.sh
   - Docker イメージをビルドします：  
     ./docker/build.sh
   - コンテナを起動します：  
     ./docker/run.sh

2. ECRL のセットアップ
   - コンテナ内で ECRL リポジトリをクローンします：  
     git clone https://github.com/DanHrmti/ECRL.git /opt/isaacgym/python/ECRL
   - 必要なフォルダを作成し、フォントファイルをコピーします：  
     mkdir -p /opt/isaacgym/python/ECRL/dejavu  
     cp /home/gymuser/.local/lib/python3.8/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSansMono.ttf /opt/isaacgym/python/ECRL/dejavu/
   - コードを修正します：  
     sed -i 's/from scipy\.spatial\._qhull import QhullError/from scipy\.spatial\.qhull import QhullError/' /opt/isaacgym/python/ECRL/slot_attention/utils.py
   - 必要な重みファイルを格納します：  
     latent_rep_chkpts フォルダ内に必要なフォルダを作成し、対応する重みファイルを配置します。

3. 実行
   - コンテナ内で ECRL ディレクトリに移動します：  
     cd /opt/isaacgym/python/ECRL
   - プロジェクトを実行します：  
     python main.py -c config/n_cubes

---

### 注意事項

- GPU ドライバと NVIDIA Container Toolkit が正しくセットアップされていることを確認してください。
- 必要な環境変数（例: DISPLAY, VK_ICD_FILENAMES, VK_LAYER_PATH）が正しく設定されていることを確認してください。

---

以上の手順で、ECRL を実行する準備が整います。
