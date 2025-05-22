Pojďme provést kontrolu kódu krok po kroku. Zde jsou **klíčové body** a **doporučené úpravy**:

---

### 🔍 **Kritické problémy**

1. **Chybějící ošetření XSS útoku**  
   
   - `marked.parse()` bez sanitizace umožňuje vložení škodlivého kódu do Markdownu.  
     **Řešení**: Přidat DOMPurify.  
     
     ```html
     <script src="https://cdnjs.cloudflare.com/ajax/libs/dompurify/3.0.6/purify.min.js"></script>
     ```
     
     ```js
     document.getElementById('content').innerHTML = DOMPurify.sanitize(marked.parse(md));
     ```

2. **Chyba v cestě k souborům**  
   
   - Pokud je `index.html` v kořeni projektu, cesta `docs/${page}.md` selže na GitHub Pages (složka se často jmenuje `/docs`).  
     **Řešení**: Upravit cestu na `./docs/${page}.md`.

---

### ⚠️ **Varování**

3. **Case-sensitive názvy souborů**  
   
   - `'README.md'` nebude fungovat na Linux serverech, pokud je soubor pojmenován `readme.md`.  
     **Doporučení**: Dodržovat konvenci lowercase (např. `home.md` místo `README.md`).

4. **Chybějící error handling**  
   
   - Pokud soubor neexistuje, uživatel uvidí prázdnou stránku.  
     **Řešení**: Přidat `.catch()` blok:  
     
     ```js
     fetch(`./docs/${page}.md`)
     .then(r => {
       if (!r.ok) throw new Error('Stránka nenalezena');
       return r.text();
     })
     .then(md => {/* render */})
     .catch(err => {
       document.getElementById('content').innerHTML = `<h1>404</h1><p>${err.message}</p>`;
     });
     ```

---

### ✨ **Vylepšení pro UX**

5. **Loading stav**  
   
   - Při načítání stránky není indikátor progresu.  
     **Doporučení**: Přidat CSS spinner před načtením obsahu.

6. **SEO a přístupnost**  
   
   - Chybí meta tagy a ARIA role.  
     **Doporučení**:  
     
     ```html
     <meta name="viewport" content="width=device-width, initial-scale=1">
     <meta name="description" content="Dokumentace projektu">
     <div id="content" role="main"></div>
     ```

---

### ✅ **Opravený kód (index.html)**

```html
<!DOCTYPE html>
<html lang="cs">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>MicroDocs</title>
  <style>
    /* Přidán loading spinner */
    #loading {
      display: none;
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
    body[data-loading="true"] #loading { display: block; }
  </style>
</head>
<body>
  <div id="loading">⏳ Načítám...</div>
  <div id="content" role="main"></div>

  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/dompurify/3.0.6/purify.min.js"></script>
  <script>
    async function loadPage() {
      document.body.setAttribute('data-loading', 'true');
      const page = window.location.hash.slice(1) || 'home'; // lowercase default
      try {
        const response = await fetch(`./docs/${page}.md`);
        if (!response.ok) throw new Error('Stránka neexistuje');
        const md = await response.text();
        document.getElementById('content').innerHTML = DOMPurify.sanitize(marked.parse(md));
      } catch (err) {
        document.getElementById('content').innerHTML = `<h1>Chyba 404</h1><p>${err.message}</p>`;
      } finally {
        document.body.removeAttribute('data-loading');
      }
    }

    window.addEventListener('hashchange', loadPage);
    loadPage();
  </script>
</body>
</html>
```

---

### 📌 **Testovací scénáře**

1. **Neexistující stránka**:  
   Zadejte `#neexistuje` → mělo by se zobrazit "Chyba 404".  
2. **XSS test**:  
   Vložte do Markdownu `<script>alert('hack')</script>` → kód by se neměl spustit.  
3. **Offline test**:  
   Spusťte přes `file:///` protokol → vše by mělo fungovat (kromě externích CDN).  

Pokud projdou všechny testy, MVP je připraveno k nasazení! 🚀
