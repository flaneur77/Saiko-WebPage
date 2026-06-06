# Moe Counter – Setup Docs

Embed a pixel-art visitor counter on your site using two free APIs:

- **[countapi.mileshilliard.com](https://countapi.mileshilliard.com)** — tracks the real count (no signup, no keys)
- **[api.sefinek.net](https://api.sefinek.net)** — renders the number as a pixel-art image

---

## Full copy-paste snippet

Drop this wherever you want the counter to appear in your HTML.

### HTML

```html
<div class="counter-section">
  <span class="counter-label">visitors</span>
  <div class="counter-img-wrap loading" id="counter-wrap">
    <img id="moe-counter" alt="visitor counter" />
  </div>
</div>
```

### CSS

```css
.counter-section {
  width: 100%;
  padding: 48px 20px 64px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.counter-label {
  font-size: 0.72rem;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: #6b6b9a;
}

.counter-img-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 40px;
}

#moe-counter {
  image-rendering: pixelated; /* keeps digits crisp — don't remove */
  max-width: 100%;
  opacity: 0;
  transition: opacity 0.4s ease;
}

#moe-counter.loaded { opacity: 1; }

.counter-img-wrap.loading::after {
  content: '...';
  font-size: 0.8rem;
  color: #6b6b9a;
  letter-spacing: 0.3em;
  animation: pulse 1.2s ease-in-out infinite;
}

@keyframes pulse { 0%,100%{opacity:0.3} 50%{opacity:1} }
```

### JavaScript

```html
<script>
  (async () => {
    const img  = document.getElementById('moe-counter');
    const wrap = document.getElementById('counter-wrap');

    const COUNT_URL = 'https://countapi.mileshilliard.com/api/v1/hit/my-awesome-site-visits';

    try {
      const res = await fetch(COUNT_URL);
      if (!res.ok) throw new Error('CountAPI error');
      const { value } = await res.json();

      const moeSrc = `https://api.sefinek.net/api/v2/moecounter`
        + `?number=${Number(value)}`
        + `&length=10`
        + `&theme=default`
        + `&pixelated=true`;

      img.onload = () => {
        wrap.classList.remove('loading');
        img.classList.add('loaded');
      };
      img.src = moeSrc;

    } catch (err) {
      console.warn('Visitor count unavailable:', err);
      wrap.classList.remove('loading');
      img.src = `https://api.sefinek.net/api/v2/moecounter?number=0&length=10&theme=default&pixelated=true`;
      img.classList.add('loaded');
    }
  })();
</script>
```

---

## What to change

### 1. Your counter key `⬅ most important`

```js
const COUNT_URL = 'https://countapi.mileshilliard.com/api/v1/hit/my-awesome-site-visits';
//                                                                 ^^^^^^^^^^^^^^^^^^^^^^
//                                                                 change this to something unique
```

Keys are **public and shared** — if someone else picks the same name, you share counts. Be specific:

| ❌ Too generic | ✅ Good |
|---|---|
| `visits` | `natsukis-blog-mainpage-2026` |
| `counter` | `coolzone-xyz-hits` |
| `mysite` | `junes-portfolio-pageviews` |

No registration needed — just pick a name and start using it. The key is created automatically on first hit.

### 2. Number of digits (`length`)

```
&length=10
```

Controls how many digit slots are shown, padded with leading zeros.

| `length` | Looks like |
|---|---|
| `5` | `00042` |
| `7` | `0000042` |
| `10` | `0000000042` |

### 3. Theme

```
&theme=default
```

> ⚠️ **Use only the exact names below.** Anything else and the image will disappear with no error — the API silently fails on unknown themes.

**All 46 verified working themes:**

| Theme name | Notes |
|---|---|
| `default` | classic dark pixel art |
| `default-big` | same as default but natively bigger digits |
| `default2` | alternate dark style |
| `default3` | alternate dark style |
| `default3-big` | bigger version of default3 |
| `default4` | alternate style |
| `default5-green` | green-tinted |
| `normal-1` | clean minimal |
| `normal-2` | clean minimal variant |
| `morden-num` | modern numerals |
| `3d-num` | 3D-looking digits |
| `nixietube-1` | retro nixie tube style |
| `nixietube-2` | nixie tube variant |
| `sketch-1` | hand-drawn look |
| `sketch-2` | hand-drawn variant |
| `ai-1` | AI-generated aesthetic |
| `miku` | Hatsune Miku themed |
| `minecraft` | Minecraft font |
| `food` | food themed |
| `capoo-1` | Capoo (bug cat) |
| `capoo-2` | Capoo variant |
| `love-and-deepspace` | game themed |
| `yousa-ling` | character themed |
| `shimmie2` | shimmie2 board style |
| `e621` | e621 board style |
| `booru-lisu` | booru style |
| `booru-touhoulat` | Touhou themed |
| `booru-smtg` | booru style |
| `booru-vp` | booru style |
| `booru-jaypee` | booru style |
| `booru-helltaker` | Helltaker themed |
| `booru-huggboo` | booru style |
| `booru-koe` | booru style |
| `booru-mof` | booru style |
| `booru-nandroid` | Nandroid themed |
| `booru-r6gdrawfriends` | booru style |
| `booru-snyde` | booru style |
| `booru-the-collection` | booru style |
| `booru-townofgravityfalls` | Gravity Falls themed |
| `booru-twifanartsfw` | booru style |
| `booru-ve` | booru style |
| `booru-vivi` | booru style |
| `booru-yuyuyui` | booru style |
| `kasuterura-1` | kasuterura style |
| `kasuterura-2` | kasuterura variant |
| `kasuterura-3` | kasuterura variant |
| `kasuterura-4` | kasuterura variant |

Browse them all visually at: https://api.sefinek.net/docs/v2/moecounter/examples

### 4. Label text

```html
<span class="counter-label">visitors</span>
```

Change `visitors` to anything — `views`, `people were here`, `hits` — or delete the `<span>` entirely.

---

## Sizing — bigger or smaller

The counter is an image, so sizing is done with CSS on the `<img>` tag.

> **Always keep `image-rendering: pixelated`** — without it the browser blurs the pixel art when scaling.

### Use a natively big theme (easiest)

Two themes have larger digits built in — no CSS needed:

```
&theme=default-big
&theme=default3-big
```

### Make it bigger with CSS

```css
#moe-counter {
  width: 320px;
  image-rendering: pixelated;
}
```

For exact pixel doubling (no interpolation at all):

```css
#moe-counter {
  transform: scale(2);
  transform-origin: center;
  image-rendering: pixelated;
}
```

### Make it smaller

```css
#moe-counter {
  width: 120px;
  image-rendering: pixelated;
}
```

### Responsive

```css
#moe-counter {
  width: 100%;
  max-width: 280px;
  image-rendering: pixelated;
}
```

---

## Multiple counters on one page

Use different `id`s and different keys per counter:

```html
<!-- Counter 1 -->
<div class="counter-section">
  <span class="counter-label">total visits</span>
  <div class="counter-img-wrap loading" id="wrap-total">
    <img id="counter-total" alt="total visitor counter" />
  </div>
</div>

<!-- Counter 2 -->
<div class="counter-section">
  <span class="counter-label">page views</span>
  <div class="counter-img-wrap loading" id="wrap-page">
    <img id="counter-page" alt="page view counter" />
  </div>
</div>

<script>
  async function loadMoeCounter({ imgId, wrapId, key, theme = 'default', length = 10 }) {
    const img  = document.getElementById(imgId);
    const wrap = document.getElementById(wrapId);
    const url  = `https://countapi.mileshilliard.com/api/v1/hit/${key}`;

    try {
      const res = await fetch(url);
      if (!res.ok) throw new Error('CountAPI error');
      const { value } = await res.json();

      const moeSrc = `https://api.sefinek.net/api/v2/moecounter`
        + `?number=${Number(value)}`
        + `&length=${length}`
        + `&theme=${theme}`
        + `&pixelated=true`;

      img.onload = () => {
        wrap.classList.remove('loading');
        img.classList.add('loaded');
      };
      img.src = moeSrc;

    } catch (err) {
      console.warn('Counter failed:', err);
      wrap.classList.remove('loading');
      img.src = `https://api.sefinek.net/api/v2/moecounter?number=0&length=${length}&theme=${theme}&pixelated=true`;
      img.classList.add('loaded');
    }
  }

  loadMoeCounter({
    imgId:  'counter-total',
    wrapId: 'wrap-total',
    key:    'mysite-total-visits-2026',   // ← change this
    theme:  'default',
    length: 10,
  });

  loadMoeCounter({
    imgId:  'counter-page',
    wrapId: 'wrap-page',
    key:    'mysite-gallery-pageviews',   // ← different key = separate count
    theme:  'miku',
    length: 7,
  });
</script>
```

Each key is tracked independently.

---

## Quick reference

| Thing to change | Where |
|---|---|
| Counter key (unique name) | `COUNT_URL` in `<script>` |
| Number of digit slots | `&length=` in the moe URL |
| Visual theme | `&theme=` — must be from the list above |
| Bigger natively | use `default-big` or `default3-big` theme |
| Size via CSS | `width` on `#moe-counter` |
| Label text | `<span class="counter-label">` |
| Multiple counters | use the `loadMoeCounter()` function above |
