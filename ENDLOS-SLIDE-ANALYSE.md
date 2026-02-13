# Endlose Slide / Infinite Marquee – nur mit CSS

## Kernidee

Der Inhalt wird **dupliziert**. Eine Kopie scrollt aus dem Sichtbereich, die andere rückt nach – beim Zurückspringen der Animation sieht es identisch aus, daher wirkt die Schleife endlos.

---

## 1. Bewährte Methode (z.B. Ryan Mulligan)

### HTML
- **Genau 2 Kopien** des gleichen Inhalts im gleichen Container.
- Die zweite Kopie mit `aria-hidden="true"` für Barrierefreiheit.

```html
<div class="marquee">
  <ul class="marquee__content">
    <li>◆</li><li>★</li><li>●</li>
  </ul>
  <ul class="marquee__content" aria-hidden="true">
    <li>◆</li><li>★</li><li>●</li>
  </ul>
</div>
```

### CSS – Container
- **Übergeordneter Container:** `display: flex`, `overflow: hidden`, `gap: var(--gap)`.
- **Inhalt-Blöcke (jede Kopie):** `flex-shrink: 0`, `min-width: 100%`, `display: flex`, `gap: var(--gap)`.

Wichtig: **`min-width: 100%`** macht jede Kopie mindestens so breit wie der sichtbare Bereich. So ist die Schleife bei wenigen Elementen (sie füllen die Breite) und bei vielen (sie quillen über) stabil.

### CSS – Animation
- Verschiebung **genau um eine Kopie + Abstand:**  
  `transform: translateX(calc(-100% - var(--gap)));`
- **Ohne** den Gap in der Berechnung springt die Schleife beim Loop sichtbar.

```css
.marquee {
  --gap: 1rem;
  display: flex;
  overflow: hidden;
  gap: var(--gap);
}
.marquee__content {
  flex-shrink: 0;
  min-width: 100%;
  display: flex;
  gap: var(--gap);
  justify-content: space-around; /* optional: wenige Items gleichmäßig verteilen */
}
@keyframes scroll {
  to { transform: translateX(calc(-100% - var(--gap))); }
}
.marquee__content {
  animation: scroll 30s linear infinite;
}
```

### Warum es endlos wirkt
- Bei `0%`: erste Kopie sichtbar, zweite rechts im Overflow.
- Bei `100%`: erste Kopie ist komplett nach links raus, zweite Kopie steht genau an der Stelle, an der die erste angefangen hat.
- Beim Reset von 100 % auf 0 % sieht man wieder dieselbe Anordnung → nahtloser Loop.

---

## 2. Alternative: Mehrere Kopien + Prozent

- Mehrere identische Blöcke (z.B. 4), jeder mit denselben Inhalten.
- Track: `display: flex`, `flex-wrap: nowrap`, `width: max-content`.
- Animation: genau **eine** Blockbreite verschieben, z.B. bei 4 Blöcken `translateX(-25%)`.

Vorteil: immer „viel“ Inhalt im Track. Nachteil: Prozent bezieht sich auf die Gesamtbreite des Tracks; bei `min-width: 100%` pro Block ist die Rechnung klarer mit der Methode oben.

---

## 3. Wichtige Punkte

| Punkt | Zweck |
|--------|--------|
| `overflow: hidden` | Versteckt den Sprung, wenn die Animation von 100 % auf 0 % zurücksetzt. |
| `flex-shrink: 0` | Verhindert, dass die Kopien schrumpfen und sich überlappen. |
| `min-width: 100%` (pro Kopie) | Jede Kopie = mindestens Viewport-Breite → Loop passt bei jeder Bildschirmgröße. |
| `calc(-100% - var(--gap))` | Verschiebung = eine Kopie + Lücke, damit kein Versatz beim Loop entsteht. |
| `linear infinite` | Gleichmäßige Geschwindigkeit, Endlosschleife. |

---

## 4. Kurzfassung

- **Zwei Kopien** des Inhalts.
- Container: Flex, `overflow: hidden`, gemeinsamer `gap`.
- Pro Kopie: `flex-shrink: 0`, `min-width: 100%`.
- Animation: von `0` nach `translateX(calc(-100% - var(--gap)))`.

Damit ist eine **endlose Slide nur mit CSS** möglich, ohne JavaScript.
