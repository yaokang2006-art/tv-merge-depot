# PLAN.md — TV Merge Depot Optimization (v2)

> Updated 2026-10-22 based on live probes from Oracle VPS (2026-10-23 UTC).

## 1. Summary of Changes from v1

**饭太硬: fully dead — all 4 entries removed.**
- `fty.888484.xyz/tv` → HTTP 200 but returns HTML landing page, not JSON config.
- `饭太硬.net/tv`, `饭太硬.cc/tv` → same HTML landing page.
- `饭太硬.com/tv` → DNS resolution failure.
- These are not transient errors; the domains have pivoted away from serving TVBox configs.

**肥猫: still dead** (domain parked/for sale). Remains excluded.

**New additions: 王二小 (4K/盘源 focus)** — verified working, replaces 饭太硬's slot.

**香雅情: trimmed from 3 → 2 mirrors** (drop unverified ghproxy).

**欧歌: added as optional single-line entry** (large config, many sites).

---

## 2. File Layout (unchanged)

```
repo root/
├── depot.json          # FongMi / 影视仓 format  {"urls":[...]}
├── storeHouse.json     # TVBox-classic format    {"storeHouse":[...]}
└── README.md
```

Public URL: `https://yaokang2006-art.github.io/tv-merge-depot/depot.json`

---

## 3. Proposed Entries (ordered by category, then reliability)

### Rationale for ordering
1. **王二小** first — 4K/网盘 oriented, fills the gap 饭太硬 left, verified fresh.
2. **香雅情** — stable GitHub-hosted, good for 海外剧/动漫 细分.
3. **欧歌** — large single-line config (many sites aggregated), HTTP-only so listed last.

### depot.json

```json
{
  "urls": [
    {
      "name": "王二小 4K (主线)",
      "url": "https://9280.kstore.vip/aiwex.json"
    },
    {
      "name": "王二小 4K (备线)",
      "url": "https://9280.kstore.space/aiwex.json"
    },
    {
      "name": "香雅情 (jsDelivr CDN)",
      "url": "https://cdn.jsdelivr.net/gh/xyq254245/xyqonlinerule@main/XYQTVBox.json"
    },
    {
      "name": "香雅情 (GitHub raw)",
      "url": "https://raw.githubusercontent.com/xyq254245/xyqonlinerule/main/XYQTVBox.json"
    },
    {
      "name": "欧歌 (聚合线路)",
      "url": "http://tv.nxog.top/m/111.php?ou=http://tv.nxog.top&out=json"
    }
  ]
}
```

### storeHouse.json

```json
{
  "storeHouse": [
    {
      "sourceName": "王二小 4K (主线)",
      "sourceUrl": "https://9280.kstore.vip/aiwex.json"
    },
    {
      "sourceName": "王二小 4K (备线)",
      "sourceUrl": "https://9280.kstore.space/aiwex.json"
    },
    {
      "sourceName": "香雅情 (jsDelivr CDN)",
      "sourceUrl": "https://cdn.jsdelivr.net/gh/xyq254245/xyqonlinerule@main/XYQTVBox.json"
    },
    {
      "sourceName": "香雅情 (GitHub raw)",
      "sourceUrl": "https://raw.githubusercontent.com/xyq254245/xyqonlinerule/main/XYQTVBox.json"
    },
    {
      "sourceName": "欧歌 (聚合线路)",
      "sourceUrl": "http://tv.nxog.top/m/111.php?ou=http://tv.nxog.top&out=json"
    }
  ]
}
```

**Entry count: 5** (below the 8–12 target, but only 5 URLs are probe-verified working).
Do not pad with dead/unverified URLs for the sake of count.

---

## 4. Entries NOT Included (and why)

| Source | Status (2026-10-23) | Reason excluded |
|--------|---------------------|-----------------|
| 饭太硬 ×4 mirrors | HTML landing page / DNS fail | No longer serves TVBox config |
| 肥猫.com | Domain parked (for sale) | Dead |
| 肥猫.live | 404 | Dead |
| 巧技 | DNS fail | Dead |
| OK影视 | DNS fail | Dead |
| 南风 agit | HTML redirect, not JSON | Broken |
| 多多 | DNS fail | Dead |
| 小盒子 | 404 | Dead |
| 月光宝盒 jihulab | 429 rate-limited | Unreliable |
| 毒盒 | Connection fail | Dead |
| 飞哥 | Connection fail | Dead |
| 玩偶 gitee | 423 repo blocked | Blocked by platform |
| 欧歌 root/m | HTML portal / 403 | Only the PHP endpoint works |
| 香雅情 ghproxy | Not probed this round | Unverified; ghproxy domains rotate frequently |

---

## 5. Notes on 欧歌 Entry

The URL `http://tv.nxog.top/m/111.php?ou=http://tv.nxog.top&out=json` returns a **single-line TVBox config** (with `sites`, `parses`, etc.), NOT a nested multi-depot (`{"urls":[...]}`).

This is valid as one selectable line in our multi-depot — the user taps "欧歌 (聚合线路)" and gets a full config with many sites. However:
- It is **HTTP-only** (no HTTPS) — may be blocked on some FongMi builds.
- The PHP endpoint could change without notice.
- If it stops working, simply remove the entry.

**Decision: include it as the last entry.** It adds diversity (aggregator with many sites) without risk to the other entries.

---

## 6. 4K / 阿里云盘 / 夸克 Note

True 4K playback from 阿里云盘 or 夸克网盘 sources requires **in-app account binding**:
- 影视仓 / FongMi: 设置 → 播放器 → 阿里Token / 夸克Cookie
- This is a per-device setting, not something our depot JSON can configure.
- 王二小's config includes 4K/盘源 site definitions, but they only work if the user has bound their account.

**Action for README:** mention that 4K/网盘 sources require in-app login setup.

---

## 7. Future Additions

When new sources are discovered or old ones revive, re-probe before adding:

```bash
# Quick probe template (from any VPS or local machine)
curl -sS -o /dev/null -w "%{http_code} %{content_type} %{size_download}B\n" "URL_HERE"
# Then: curl -sS "URL_HERE" | python3 -m json.tool > /dev/null
```

Potential candidates to re-check periodically:
- 饭太硬 — if domains start serving JSON again
- 月光宝盒 — if jihulab rate-limiting resolves
- Any new sources from TG groups / TVBox forums

Target: grow to 8–10 entries over time as verified sources appear.

---

## 8. Acceptance Criteria Checklist

### JSON validity
- [ ] `depot.json` is valid UTF-8 (no BOM), parses with `python3 -m json.tool`
- [ ] `storeHouse.json` is valid UTF-8 (no BOM), parses with `python3 -m json.tool`
- [ ] `depot.json` has exactly 5 `urls` entries
- [ ] `storeHouse.json` has exactly 5 `storeHouse` entries
- [ ] Both files have matching entries (same URLs, same order)

### Liveness (re-probe before deploy)
- [ ] 王二小 主线 (`kstore.vip`) → HTTP 200, valid TVBox JSON
- [ ] 王二小 备线 (`kstore.space`) → HTTP 200, valid TVBox JSON
- [ ] 香雅情 jsDelivr → HTTP 200, valid JSON
- [ ] 香雅情 GitHub raw → HTTP 200, valid JSON
- [ ] 欧歌 PHP endpoint → HTTP 200, valid TVBox JSON (single-line config)

### Dead entries removed
- [ ] No 饭太硬 URLs remain in either JSON file
- [ ] No 肥猫 URLs remain
- [ ] No other probe-failed URLs included

### In-app smoke test
- [ ] FongMi: paste `https://yaokang2006-art.github.io/tv-merge-depot/depot.json` as 配置地址 → all 5 lines appear in selector
- [ ] 影视仓: same URL → all 5 lines appear
- [ ] Tap 王二小 4K (主线) → config loads, sites populate
- [ ] Tap 香雅情 (jsDelivr CDN) → config loads, sites populate
- [ ] Tap 欧歌 (聚合线路) → config loads (HTTP; may fail on strict-HTTPS builds — acceptable)

### GitHub Pages
- [ ] `curl -I https://yaokang2006-art.github.io/tv-merge-depot/depot.json` → HTTP 200
- [ ] Response is `application/json` or `text/plain`

---

## 9. Risks

| Risk | Likelihood | Mitigation |
|------|-----------|------------|
| 王二小 kstore domains lapse | Medium | Two mirrors (.vip + .space); if both die, remove |
| 香雅情 GitHub repo deleted | Low | jsDelivr CDN caches independently from raw; 2 paths |
| jsDelivr CDN blocked in certain regions | Medium | GitHub raw fallback |
| 欧歌 PHP endpoint changes/dies | Medium-High | Listed last; removable without affecting other entries |
| All sources die simultaneously | Very Low | Re-probe and find new sources; this is an ongoing maintenance task |
| FongMi build rejects HTTP URLs | Medium | 王二小 + 香雅情 are all HTTPS; only 欧歌 is HTTP |
| GitHub Pages propagation delay | Low/transient | Wait 2–5 min after push |
