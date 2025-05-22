# dynamické řešení pro automatické generování sidebaru

 na základě existujících souborů v `/pages`. Zde je vylepšená implementace:

---

```html
<script>
async function loadSidebar() {
  const sidebarEl = document.getElementById('sidebar');
  
  // 1. Pokus o načtení vlastního sidebar.md
  try {
    const customSidebar = await fetch('./pages/_sidebar.md');
    if (customSidebar.ok) {
      sidebarEl.innerHTML = renderMarkdown(await customSidebar.text());
      initSidebarLinks();
      return;
    }
  } catch {}

  // 2. Fallback: Automatické generování z .md souborů
  const files = await fetch('./pages/')
    .then(r => r.text())
    .then(html => [...html.matchAll(/href="(.+?\.md)"/g)])
    .then(matches => [...new Set(matches.map(m => m[1]))]);

  const navItems = files.map(file => 
    `- [${file.replace('.md', '')}](#${file.replace('.md', '')})`
  ).join('\n');

  sidebarEl.innerHTML = renderMarkdown(`## Obsah\n${navItems}`);
  initSidebarLinks();
}
</script>
```

### Jak to funguje:
1. **Priorita vlastního sidebaru**  
   Pokud existuje `_sidebar.md`, použije se jeho obsah.

2. **Automatická detekce článků**  
   Když vlastní sidebar chybí, systém:
   - Získá seznam všech `.md` souborů v adresáři
   - Vygeneruje základní navigaci s odkazy

### Předpoklady pro automatické generování:
```bash
# Struktura serveru musí povolovat listing adresářů
# (Pro lokální testování spusťte server pomocí Pythonu)
python3 -m http.server 8000 --bind 127.0.0.1
```

### Výhody tohoto přístupu:
- **Zero Config**: Funguje ihned bez jakékoli konfigurace
- **Hybridní režim**: Kombinace ručního/automatického sidebaru
- **Responzivní detekce**: Okamžitá reakce na nové soubory

Pro produkční prostředí doporučuji přidat do adresáře `/pages` prázdný `index.html` soubor pro vypnutí listingu, pokud není žádoucí.