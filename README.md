# TV Merge Depot / 多仓配置

A tiny public multi-depot (多仓) list for TVBox shells **影视仓** and **FongMi** on Android TV.

面向 Android TV 上 **影视仓** / **FongMi** 的精简多仓配置：一个 HTTPS 地址贴进「配置地址」，即可在应用内切换多条单仓线路。

## What this is / 这是什么

`depot.json` is the FongMi / 影视仓 format (`{"urls":[{"name","url"}]}`).
`storeHouse.json` is the TVBox-classic twin (`{"storeHouse":[{"sourceName","sourceUrl"}]}`).

Both files list the **same 7 entries**, in reliability order:

1. 饭太硬 (primary) — `http://fty.888484.xyz/tv`
2. 饭太硬 (mirror .net)
3. 饭太硬 (mirror .cc)
4. 饭太硬 (mirror .art)
5. 香雅情 (jsDelivr CDN)
6. 香雅情 (GitHub raw)
7. 香雅情 (ghproxy mirror)

No extra source URLs were added.

## How to use / 怎么用

1. Copy one of the **public URLs** below.
2. Open **影视仓** or **FongMi** → 设置 / 配置 → **配置地址**.
3. Paste the URL and confirm. The app should show all 7 lines.
4. If one line fails, switch to another line in-app. You do not need to edit JSON by hand.

影视仓 / FongMi 多数版本把本仓库的 `depot.json` 当作多仓入口。少数只认经典格式的壳，改贴 `storeHouse.json`。

## Public URLs / 公开地址

GitHub Pages is enabled from branch `main`, folder `/` (root).

### GitHub Pages (preferred HTTPS)

- Depot (影视仓 / FongMi): `https://yaokang2006-art.github.io/tv-merge-depot/depot.json`
- Classic twin: `https://yaokang2006-art.github.io/tv-merge-depot/storeHouse.json`

### Backup raw / jsDelivr URLs (same files)

- `https://raw.githubusercontent.com/yaokang2006-art/tv-merge-depot/main/depot.json`
- `https://raw.githubusercontent.com/yaokang2006-art/tv-merge-depot/main/storeHouse.json`
- `https://cdn.jsdelivr.net/gh/yaokang2006-art/tv-merge-depot@main/depot.json`
- `https://cdn.jsdelivr.net/gh/yaokang2006-art/tv-merge-depot@main/storeHouse.json`

## Notes / 说明

- **肥猫 / 欧歌 are currently down and excluded.** `肥猫.com` is a for-sale page; `我不是.肥猫.live` is an HTML stub / 404; `tv.nxog.top` returns 403 or a captcha gate. Do not paste those as 配置地址.
- **饭太硬 returns encrypted image bytes (JPEG/BMP).** That is normal. TVBox shells decode the payload; `curl` will look like binary, not JSON.
- Files are UTF-8 **without BOM**, pretty-printed JSON.

## Files / 文件

| File | App | Shape |
|------|-----|--------|
| `depot.json` | FongMi, 影视仓 | `urls[]` with `name` + `url` |
| `storeHouse.json` | TVBox classic | `storeHouse[]` with `sourceName` + `sourceUrl` |

## Local check / 本地校验

```bash
python3 -m json.tool depot.json >/dev/null
python3 -m json.tool storeHouse.json >/dev/null
file depot.json storeHouse.json
```
