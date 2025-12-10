# Calculadora BTU — HVAC

Calculadora sencilla para estimar BTU requeridos de un espacio e **inferir combinaciones de aires acondicionados** que se acerquen al total. UI oscura con Ionic (CDN) y exportación a **PDF** (y compartir en móvil cuando es posible).

> **Demo:** https://juliorodriguezs.github.io/-Calculadora-BTU-HVAC/

---

## ✨ Características

- Cálculo total BTU a partir de:
  - Área del local (m²)
  - Personas (650 BTU/persona)
  - Área de ventanas (450 BTU/m²)
  - Luminarias (200 BTU/unidad)
- Conversión a toneladas (≈ 12 KBTU = 1 ton).
- Sugerencias de **combinación de equipos** (capacidades por defecto: 12, 18, 24, 36, 46 KBTU) con opción de **permitir sobrecapacidad**.
- **PDF** con datos del cliente + entradas + resultado + top combinaciones.
- **Compartir** (Web Share API) cuando el navegador lo soporta.

---

## 🔢 Fórmulas

- Base de área: `30 m² → 28,800 BTU`
- Personas: `650 BTU × #personas`
- Ventanas: `450 BTU × m² ventana`
- Luminarias: `200 BTU × #luminarias`

**Total BTU** = `BTU_area + BTU_personas + BTU_ventanas + BTU_luminarias`  
**Toneladas** ≈ `Total BTU / 12,000`

**Combinaciones**: se exploran combinaciones repetibles de capacidades `[12,18,24,36,46] KBTU` con límite de equipos **auto** (según objetivo) y ordenadas por cercanía al objetivo, #equipos y preferencia por no quedar cortos (si se permite sobrecapacidad).

---

## 🧭 Uso rápido

### Web
1) Abre la **demo**: https://juliorodriguezs.github.io/-Calculadora-BTU-HVAC/  
2) Llena datos (cliente opcional), presiona **Calcular**.  
3) Revisa **Resultado** y **Combinaciones**.  
4) Exporta con **Descargar PDF**. En móviles, usa **Compartir** si aparece.

### Local (dev)
```bash
# Clonar
git clone https://github.com/JulioRodriguezS/-Calculadora-BTU-HVAC.git
cd -Calculadora-BTU-HVAC

# Instalar herramientas de build (Vite + gh-pages)
npm i

# Modo desarrollo
npm run dev

# Build producción a /dist
npm run build

# Publicar la web (compila y empuja a gh-pages)
npm run deploy

```js
-Calculadora-BTU-HVAC/
├─ index.html          # App (Ionic + lógica + PDF)
├─ vite.config.js      # base para GitHub Pages (subcarpeta del repo)
├─ package.json        # scripts: dev, build, deploy (gh-pages)
└─ public/
   └─ logo.png         # opcional; si no existe, el PDF usa texto

```

🚀 Deploy a GitHub Pages (rama gh-pages)

Asegura scripts en package.json:
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "predeploy": "vite build",
    "deploy": "gh-pages -d dist"
  }
}
```

Configura Vite para servir en subcarpeta del repo:

```js
// vite.config.js
import { defineConfig } from "vite";
export default defineConfig({
  base: "/-Calculadora-BTU-HVAC/",
});
```

Publica:

```js
npm run deploy
```

En GitHub: Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: gh-pages / Folder: / (root).

🤖 Alternativa: Deploy automático con GitHub Actions

En Settings → Pages selecciona Source: GitHub Actions.

Crea .github/workflows/gh-pages.yml:

```yml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Con Actions, basta con git push a main y se publica.

📱 Nota sobre móviles (PDF/Compartir)

Algunos navegadores móviles (especialmente iOS/Safari) bloquean descargas directas de blobs.

La app intenta descargar y, si falla, abre el PDF en nueva pestaña (desde ahí puedes guardar/compartir).

El botón Compartir solo aparece si navigator.share/navigator.canShare está soportado.

🛠️ Troubleshooting

No veo cambios en la web: usa npm run deploy (si publicas con gh-pages), verifica vite.config.js (base correcto) y fuerza recarga con Ctrl/Cmd+Shift+R o usa ?v=timestamp al final de la URL.

404 o estilos rotos: revisa el base de Vite y que la Pages apunte a gh-pages (o a Actions).

Logo no se muestra en PDF: coloca public/logo.png o ignóralo (el PDF funciona sin logo).

Compartir no aparece: tu navegador no soporta Web Share API; usa Descargar PDF (o abrir en nueva pestaña).

📄 Licencia

MIT.