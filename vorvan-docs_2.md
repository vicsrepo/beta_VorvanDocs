Ano! Zde je **MVP (Minimální životaschopný produkt)** pro ultra-lehký nástroj dokumentace inspirovaný Docsify – **„MicroDocs“**. Zaměřuje se pouze na základní funkce s nulovou režií.

---

### 🎯 **MVP Scope (v0.1)**

**Jediný povinný soubor**: `index.html` + složka s Markdownem.  
**Funkce**:  

1. ⚡ **Instantní načítání Markdownu** (bez buildování)  
2. 🧭 **Hash-based router** pro navigaci mezi stránkami  
3. 🖌️ **Automatický dark/light režim** (podle systému)  
4. 📂 **Automatické zobrazení `README.md`** jako homepage  

---

### 🛠️ **Technologie**

- **Markdown → HTML**: `marked.js` (lehčí než WebAssembly pro MVP)  
- **Styling**: 20 řádků vlastního CSS (žádné frameworky)  
- **Routing**: Vanilla JS `hashchange` event  

---

### 📂 **Struktura projektu**

```bash
your-project/  
├── index.html          # Jediný konfigurační soubor  
└── docs/               # Složka s dokumentací  
     ├── README.md  
     ├── quickstart.md  
     └── api.md  
```

---

### 📜 **index.html** (kompletní kód)

```html
<!DOCTYPE html>
<html>
<head>
  <!-- MicroDocs MVP -->
  <meta charset="UTF-8">
  <title>MicroDocs</title>
  <style>
    :root { --text: #333; --bg: white; }
    @media (prefers-color-scheme: dark) {
      :root { --text: #eee; --bg: #1a1a1a; }
    }
    body { 
      max-width: 800px; 
      margin: 0 auto;
      padding: 2rem;
      color: var(--text);
      background: var(--bg);
    }
  </style>
</head>
<body>
  <div id="content"></div>

  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
  <script>
    // 1. Router
    async function loadPage() {
      const page = window.location.hash.slice(1) || 'README';
      const md = await fetch(`docs/${page}.md`).then(r => r.text());
      document.getElementById('content').innerHTML = marked.parse(md);
    }

    // 2. Init
    window.addEventListener('hashchange', loadPage);
    loadPage();
  </script>
</body>
</html>
```

---

### 🚀 **Jak spustit?**

1. Vytvořte složku `docs` s Markdown soubory  

2. Vložte výše uvedený `index.html` do kořenového adresáře  

3. Otevřete v prohlížeči:  
   
   ```bash
   python3 -m http.server 8000  # Lokální server
   ```
   
   Nebo deployněte na GitHub Pages/NETLIFY **bez buildu**.

---

### ⚖️ **Proč to stačí pro MVP?**

- **Velikost**: Celkově < 30 KB (včetně Markdown parseru)  
- **Kompatibilita**: Funguje všude (i na `file:///` protokolu)  
- **Rozšiřitelnost**: Později lze přidat pluginy přes `<script>` tagy  

**Výhoda oproti Docsify**: Žádné skryté závislosti – vše je explicitně vidět v 1 souboru.  

Chcete něco upravit nebo přidat? 😊
