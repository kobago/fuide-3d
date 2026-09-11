# fuide-3d

[FUIDE](https://github.com/kobago/fuide) アプリ向けの 3D ビューポート (wgpu 30、egui 0.36)。[FUIDE CAD](https://github.com/kobago/fuide-cad) と [FUIDE EDA](https://github.com/kobago/fuide-eda) が使う。

```
src/math.rs        Vec3 / 列優先 Mat4 (外部の線形代数クレート無し)
src/camera.rs      Z-up オービットカメラ (yaw / pitch / distance)、プリセット、fit、射影
src/scene.rs       CPU 側のメッシュと線バッチ、GPU 再アップロード用のバージョン番号
src/renderer.rs    wgpu パイプライン: ホログラム塗り (fresnel + 走査線) / 自身の色で陰影 (`Style::solid`)、グローするスクリーン空間の線。MSAA 4x + 自前デプスでオフスクリーンに描き、egui がテクスチャとして貼る
src/viewport.rs    egui ウィジェット: 入力 (オービット / パン / ズーム)、描画呼び出し、HUD の三軸トライアド
src/shaders/       holo.wgsl / lines.wgsl
tests/             egui_kittest のスナップショット (SHADED / WIRE / X-RAY)
```

透明クリアの上に premultiplied alpha で描くので、パネルの地色が透ける: 3D は別世界ではなく FUI の中の 1 レイヤー。

## 使い方

```toml
[dependencies]
fuide = { git = "ssh://git@github.com/kobago/fuide" }
fuide-3d = { git = "ssh://git@github.com/kobago/fuide-3d" }
# fuide-3d は Metal バックエンドだけを有効にする。Linux 向けの Vulkan はアプリ側で足す:
# wgpu = { version = "30", default-features = false, features = ["vulkan", "wgsl"] }
```

`fuide` / `fuide-3d` は git 依存 (private なので SSH)。隣の checkout (`../fuide`, `../fuide-3d`) で直しながら動かすときは、アプリの `Cargo.toml` 末尾のコメントの `[patch]` を外す。`fuide` の egui の版と揃えること。

## テスト

```sh
cargo test              # スナップショットは tests/snapshots/ (kittest.toml の閾値)
UPDATE_SNAPSHOTS=1 cargo test   # 基準画像の更新
```
