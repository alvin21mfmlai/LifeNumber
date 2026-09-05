# Life Number Worksheet

Interactive SHUYI 生命信息 chart. Enter a birth date; the seed cells, the A–J pyramid, both
wing strips (K L M / N O P) and the 缺失数字 missing-number panel all recompute.

Static site — one HTML file, no build step, no dependencies. The only network request is the
Google Fonts stylesheet; everything else is inline.

## The algorithm

Every cell is one addition followed by a **digital root** (sum the digits until one figure remains).

```
dr(n) = 1 + (n - 1) % 9

A = dr(day)      B = dr(month)     D = dr(century)   E = dr(year mod 100)
C = dr(A + B)    F = dr(D + E)     G = dr(C + F)     crown = dr(A + E + G)
H = dr(C + G)    I = dr(F + G)     J = dr(H + I)
K = dr(A + C)    L = dr(B + C)     M = dr(K + L)     # left strip, read outward
N = dr(D + F)    O = dr(E + F)     P = dr(N + O)     # right strip, read outward

missing = {1..9} minus every value above
```

## Deploy to Vercel

Pick whichever route suits you — all three produce the same static deployment.

### 1. Vercel CLI (fastest)

```bash
npm i -g vercel
cd life-number-worksheet
vercel            # preview URL
vercel --prod     # production URL
```

First run asks you to log in and confirm the project name. Accept the defaults: framework
preset **Other**, build command **none**, output directory **./**.

### 2. Drag and drop

Zip the contents of this folder (`index.html` + `vercel.json`, not the folder itself) and drop
it on <https://vercel.com/new> → *Deploy without Git*.

### 3. GitHub import (best if you want auto-deploys on push)

```bash
cd life-number-worksheet
git init && git add . && git commit -m "Life number worksheet"
gh repo create life-number-worksheet --public --source=. --push
```

Then <https://vercel.com/new> → import the repo → Deploy. Framework preset **Other**; leave the
build and output settings empty. Every push to `main` redeploys.

## Custom domain

Vercel dashboard → project → **Settings → Domains** → add your domain, then point a `CNAME` at
`cname.vercel-dns.com` (or the `A` record Vercel shows for an apex domain).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: markup, CSS custom-property theme (light + dark), and the calculator script |
| `vercel.json` | Clean URLs, no trailing slash, basic security headers, no-cache on the HTML |

## Notes

- Reverse-engineered from a completed SHUYI worksheet for 7 June 1961, which the page loads as
  its default state.
- That scan's left strip reads `2 1 1` (K = 1). By the rule K = A + C = 7 + 4 = 11 → 2, giving
  `3 1 2`. The same 11 is reduced correctly to 2 for cell **I** elsewhere on the sheet, so the
  strip looks like a slip. The missing number is 5 either way.
- Edge case: a year ending `00` (2000, 1900) gives E = 0, since the digital root of 0 is 0. The
  scanned sheet doesn't cover this. If you'd rather treat `00` as 9, change `reduce` in
  `index.html` to return 9 for 0.
- Numerology, not arithmetic with a claim on anything.
