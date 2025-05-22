MicroVorvanDocs

Zaměříme se na **ultralehkou základní verzi** bez AI, playgroundů a kolaborace. Pojďme vytvořit **„MicroVorvanDocs“** – minimalistickou alternativu k Docsify s rychlostí jako priorita.  

---

### 🎯 **Minimální jádro (v0.1)**

1. **Instant Markdown → HTML**  
   
   - Načítání `.md` souborů přímo z adresáře (žádný build, žádné konfigurace).  
   - Rendering pomocí **WebAssembly Markdown parseru** (3x rychlejší než klasické JS řešení).  

2. **Zero-Dependency Router**  
   
   - Navigace pomocí hash (#) bez reloadu stránky.  
   - Automatické detekce `README.md` jako výchozí stránky.  

3. **CSS Atomic Injection**  
   
   - Vestavěné minimalistické CSS (5 KB) s tmavým/světlým režimem.  
   - Žádné externí závislosti – vše v jednom HTML souboru.  

---

### 🛠️ **Struktura projektu**

```bash
/docs  
  ├── index.html      # 1 soubor se vším potřebným  
  ├── styles.wasm     # Kompilované CSS pro rychlé načítání  
  └── /pages          # Vaše markdown dokumentace  
       ├── README.md  
       └── api.md  
```

---

### 📜 **index.html** (kompletní nastavení)

```html
<!DOCTYPE html>
<html>
<head>
  <!-- WASM Markdown parser + atomic CSS -->
  <script type="module">
    import init from './microdocs.wasm.js'; // WebAssembly modul
    init().then(() => {
      window.renderMarkdown = (md) => window.mdWasm.parse(md);
    });
  </script>
</head>
<body>
  <div id="content"></div>

  <script>
    // Router
    async function loadPage() {
      const page = window.location.hash.slice(1) || 'README';
      const md = await fetch(`./pages/${page}.md`).then(r => r.text());
      document.getElementById('content').innerHTML = renderMarkdown(md);
    }

    window.addEventListener('hashchange', loadPage);
    loadPage();
  </script>
</body>
</html>
```

---

### 🚀 **Jak začít?**

1. Přidejte své `.md` soubory do složky `/pages`  
2. Deployněte kamkoli (GitHub Pages, Netlify…) – **žádný build potřebný!**

---

### ⚡ Výhody oproti Docsify

- **Rychlost**: 15 KB vs. Docsify's 30 KB (+ WebAssembly výhody).  
- **Transparentnost**: Žádné skryté konfigurace – vše je v 1 HTML souboru.  
- **Portabilita**: Funguje i offline z lokálního disku (file:// protocol).  
