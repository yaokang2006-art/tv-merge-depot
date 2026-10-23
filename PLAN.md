# PLAN.md — TV Merge Depot (Multi-depot JSON)

## 1. File Layout

```
repo root/
├── depot.json          # FongMi / 影视仓 format  {"urls":[...]}
├── storeHouse.json     # TVBox-classic format    {"storeHouse":[...]}
└── README.md           # Brief description + raw URLs for copy-paste
```

GitHub Pages serves the repo root directly. Both JSON files are reachable at:
- `https://<user>.github.io/<repo>/depot.json`
- `https://<user>.github.io/<repo>/storeHouse.json`

---

## 2. Exact JSON Entries (ordered by reliability)

### depot.json

```json
{
  "urls": [
    {
      "name": "饭太硬 (primary)",
      "url": "http://fty.888484.xyz/tv"
    },
    {
      "name": "饭太硬 (mirror .net)",
      "url": "http://www.饭太硬.net/tv"
    },
    {
      "name": "饭太硬 (mirror .cc)",
      "url": "http://www.饭太硬.cc/tv"
    },
    {
      "name": "饭太硬 (mirror .art)",
      "url": "http://www.饭太硬.art/tv"
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
      "name": "香雅情 (ghproxy mirror)",
      "url": "https://ghproxy.net/https://raw.githubusercontent.com/xyq254245/xyqonlinerule/main/XYQTVBox.json"
    }
  ]
}
```

### storeHouse.json

```json
{
  "storeHouse": [
    {
      "sourceName": "饭太硬 (primary)",
      "sourceUrl": "http://fty.888484.xyz/tv"
    },
    {
      "sourceName": "饭太硬 (mirror .net)",
      "sourceUrl": "http://www.饭太硬.net/tv"
    },
    {
      "sourceName": "饭太硬 (mirror .cc)",
      "sourceUrl": "http://www.饭太硬.cc/tv"
    },
    {
      "sourceName": "饭太硬 (mirror .art)",
      "sourceUrl": "http://www.饭太硬.art/tv"
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
      "sourceName": "香雅情 (ghproxy mirror)",
      "sourceUrl": "https://ghproxy.net/https://raw.githubusercontent.com/xyq254245/xyqonlinerule/main/XYQTVBox.json"
    }
  ]
}
```

---

## 3. Hosting Recommendation: GitHub Pages

**Recommended: GitHub Pages on a new free public repo.**

Reasons:
- Free HTTPS with no config. The Oracle VPS only speaks plain HTTP unless nginx + Let's Encrypt is configured; GitHub Pages gives HTTPS out of the box.
- FongMi and 影视仓 both handle HTTPS 配置地址 better than plain HTTP (some builds block cleartext).
- Zero server admin: no nginx, no renewal cron, no VPS downtime risk.
- Raw `githubusercontent.com` URLs also work as fallback if Pages is temporarily stale (CDN propagation).
- The Oracle VPS is appropriate as a *fallback* only — serve the same static files there via nginx if GitHub Pages ever has outages. Not primary because Let's Encrypt renewal adds maintenance burden.

**Setup steps for the implementing agent:**
1. Create a new public GitHub repo (e.g. `tv-merge-depot`).
2. Push `depot.json`, `storeHouse.json`, `README.md` to `main`.
3. Enable GitHub Pages: Settings → Pages → Source: Deploy from branch `main`, folder `/` (root).
4. Wait ~1 min; verify `https://<user>.github.io/tv-merge-depot/depot.json` returns 200.

---

## 4. Acceptance Criteria Checklist

- [ ] **HTTPS reachable**: `curl -I https://<user>.github.io/tv-merge-depot/depot.json` → `HTTP/2 200`
- [ ] **Content-Type**: response header includes `application/json` or `text/plain` (GitHub Pages serves JSON as `application/json`)
- [ ] **depot.json parses**: `curl -s .../depot.json | python3 -m json.tool` exits 0 with no errors
- [ ] **storeHouse.json parses**: same check for storeHouse.json
- [ ] **Entry count**: `depot.json` has exactly 7 `urls` entries; `storeHouse.json` has exactly 7 `storeHouse` entries
- [ ] **FongMi smoke test**: paste `https://<user>.github.io/tv-merge-depot/depot.json` as 配置地址 in FongMi on the onn box; repo list loads and all 7 lines appear
- [ ] **影视仓 smoke test**: same URL in 影视仓; repo list loads
- [ ] **Source spot-check**: tap 饭太硬 primary in-app; it connects (encrypted JPEG/BMP is normal — app handles decode)
- [ ] **UTF-8 no BOM**: `file depot.json` → `UTF-8 Unicode text` (not `UTF-8 Unicode (with BOM)`)

---

## 5. Risks

| Risk | Likelihood | Mitigation |
|------|-----------|------------|
| 饭太硬 primary (`fty.888484.xyz`) goes down | Medium — domain DNS could lapse | 3 IDN mirror URLs listed as fallback; user switches in-app |
| IDN mirrors (`饭太硬.net/.cc/.art`) all fail | Low — independent domains | If all 4 die, remove from JSON; 香雅情 still works |
| 香雅情 GitHub repo deleted/renamed | Low | jsDelivr CDN + raw + ghproxy = 3 independent paths; unlikely all fail simultaneously |
| jsDelivr CDN throttling / ban in CN | Medium (known CDN blocks) | ghproxy.net mirror + raw GitHub as fallbacks |
| 肥猫 / 欧歌 / nxog.top currently dead | **Confirmed** | Not included. Document in README as "previously popular, currently unavailable" |
| GitHub Pages propagation delay after push | Low / transient | Wait 2–5 min after push before testing |
| onn box HTTP-only restriction | Possible on old firmware | Oracle VPS plain-HTTP mirror can serve as fallback if HTTPS fails on device |
| ghproxy.net domain changes | Medium — proxy sites rotate | Listed third; jsDelivr is primary CDN for 香雅情 |
