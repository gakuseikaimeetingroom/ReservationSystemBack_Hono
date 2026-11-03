# ReservationSystem 構成図

ソース確認日：2026-11-03。`stack-diagram` スキルで生成。

| 内容 | HTML | PNG（ライト） | PNG（ダーク） |
| --- | --- | --- | --- |
| システム全体・通信経路 | [index.html](index.html) | [overview.png](overview.png) | [overview-dark.png](overview-dark.png) |
| フォルダの役割・主要な参照関係 | [structure.html](structure.html) | [structure.png](structure.png) | [structure-dark.png](structure-dark.png) |

HTMLはロゴと欧文フォントを埋め込み済みで、オフラインで開けます。日本語はOSの日本語フォントを使用します。画面上部から図と配色を切り替えられます。各HTMLの下部に根拠と補足を記載しています。

## 調査範囲

- `ReservationSystem/`：React 19、TypeScript、Vite 6、Chakra UI 3、React Router 7。`src/app/`、`src/features/`、`src/shared/`、`src/lib/` を確認。
- `ReservationSystemBack_Hono/`：Node.js上のHono、Prisma 6、PostgreSQL、Supabase Storage、jose、bcrypt。`src/`、`prisma/`、`.github/workflows/keep_alive.yml` を確認。
- バージョンはpackage.jsonの宣言を参照。フロントエンドREADMEのReact表記も19に更新済みです。
- 実サービスへのアクセス、DB接続、メール送信、デプロイは行っていません。ホスト先、DB配置先、Supabase RPCと予約DBの同一性は未確認です。
- 図中の矢印は主要な呼び出し・参照を表し、全importや全HTTPリクエストの網羅図ではありません。

## 実装を読む際の補足

- 画面の主要な予約・管理処理は現在も `src/lib/functions.ts` を参照しています。`features/reservation/api/` と `shared/api/` にも別の通信実装があります。
- 管理画面のJWT送信は実装されていますが、バックエンド予約ルートの管理者認可はTODOです。JWT検証は `/api/auth/verify` で確認できます。
- 返却フォームのキーは `damageDetails / returnImages`、APIの受信キーは `damage_details / return_images` で一致していません。図はコード上の処理経路であり、結合動作の検証結果ではありません。
- メールはGmailまたはメールアプリの作成画面を開き、管理者が送信する方式です。Resendは依存関係にありますが使用箇所は確認できません。
- フロントのAPI接続先は `VITE_BACKEND_URL`（既定値のポート8000）、APIの待受は `PORT`（既定値3000）です。実行時には設定を合わせる必要があります。
- GitHub Actionsは毎日09:00 JSTのkeep_alive RPC呼び出しと空コミットのpushを設定しています。デプロイ用ワークフローは確認できません。

## 再生成

編集用HTMLは `index.source.html` と `structure.source.html` です。ロゴ・フォントの埋め込み前のため、閲覧には生成済みHTMLを使ってください。

Python 3.9以上、PlaywrightとChromium、ロゴ取得用のネット接続が必要です。スキルの場所が異なる場合は `DIAGRAM_SKILL` を変更してください。

```bash
DIAGRAM_SKILL="$HOME/.agents/skills/stack-diagram"
DIAGRAM_ICONS="$(mktemp -d /tmp/reservation-diagram-icons.XXXXXX)"
python3 "$DIAGRAM_SKILL/scripts/fetch_icons.py" "$DIAGRAM_ICONS" \
  react hono prisma postgresql supabase vite typescript github nodejs \
  browser:lucide/users folder:lucide/folder auth:lucide/key-round \
  api:lucide/network calendar:lucide/calendar-days mail:lucide/mail

python3 "$DIAGRAM_SKILL/scripts/build.py" index.source.html "$DIAGRAM_ICONS" index.html --fonts "$DIAGRAM_SKILL/assets/fonts"
python3 "$DIAGRAM_SKILL/scripts/build.py" structure.source.html "$DIAGRAM_ICONS" structure.html --fonts "$DIAGRAM_SKILL/assets/fonts"
python3 "$DIAGRAM_SKILL/scripts/render_png.py" index.html overview.png --scale 2
python3 "$DIAGRAM_SKILL/scripts/render_png.py" structure.html structure.png --scale 2
```

コマンドはこのディレクトリで実行してください。PNGはHTMLからChromiumで生成し、両テーマを目視確認します。ロゴ出典：svgl / Lucide。各ブランドの商標はそれぞれの所有者に帰属します。
