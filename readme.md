# Automatisation de production de contenu svg et export au format png 

Ce projet permet de **rendre du texte dans des slots SVG** avec une gestion fine des mots, espaces et styles (taille, police, couleur, gras).  
Il combine **PIL (Pillow)** pour mesurer la largeur des mots avec précision et **CairoSVG** pour générer un rendu final cohérent.

---

## 🚀 Fonctionnalités

- Découpage du texte en **tokens** (`WORD`, `SPACE`, `FORMULA`, etc.) via regex
- Gestion explicite des **espaces** comme entités indépendantes
- Calcul de largeur des mots avec `ImageFont.getlength()`
- Placement automatique dans des slots avec gestion des retours à la ligne
- Application de styles SVG :
  - `font-family`
  - `font-size`
  - `font-weight` (normal/bold)
  - `fill` (couleur du texte)
- Outils de **debug visuel** (rectangles autour des mots/espaces)

---

## 📂 Organisation

- `render_text_in_slot(...)` : fonction principale pour insérer du texte dans un slot SVG
- `split_text_into_tokens(...)` : découpe le texte en mots/espaces/formules
- `get_text_width(text, font)` : calcule la largeur d’un token avec PIL
- `apply_text_style(...)` : applique les styles (`font-size`, `font-family`, `font-weight`, `fill`)
- Debug : ajout de `<rect>` autour des tokens pour visualiser leur largeur

---

## ⚙️ Dépendances

- [Pillow](https://pillow.readthedocs.io/) (PIL) pour la mesure des textes
- [lxml](https://lxml.de/) pour manipuler le XML/SVG
- [CairoSVG](https://cairosvg.org/) pour convertir le SVG en PNG

Installation rapide :

```bash
pip install pillow lxml cairosvg
