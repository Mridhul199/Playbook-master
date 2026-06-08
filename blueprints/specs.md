# Specs blueprint

For component spec docs (anatomy, states, variants, props, tokens).

---

## Section structure

```
1. Hero — what the component is, who uses it, what it's good for (one-liner)
2. Anatomy — labelled diagram (header / body / footer / actions / states)
3. Variants — visual grid of all variants (size, intent)
4. States — interactive demo of every state (default / hover / active / focus / disabled / loading / error)
5. Props / API table — every prop with type, default, description, example
6. Tokens used — design tokens referenced (color, spacing, type, radii) with name + raw value
7. Usage examples — 3–5 real composition examples with code
8. Anti-patterns — wrong usage with the fix
9. Footnote — links to Figma, Storybook, related components
```

---

## Reading TSX source for specs

When extracting specs from the source, read **only** what you need:
- Sizes / heights → `helper.ts` or `<Component>.types.ts`
- Default props → top of `<Component>.tsx` (props destructuring)
- Variants → story file `export const X = ...` declarations
- Tokens used → `className` strings in JSX (Tailwind utilities)

Don't read the whole component tree. Use `Read` with `limit`/`offset`.

---

## Variant grid recipe

```html
<div class="variant-grid">
  <article class="variant-tile">
    <div class="variant-meta">
      <span class="variant-name">Primary</span>
      <span class="variant-tag">Intent</span>
    </div>
    <div class="variant-demo">
      <!-- live component, all sizes side-by-side -->
      <button class="btn lg">Button</button>
      <button class="btn md">Button</button>
      <button class="btn sm">Button</button>
    </div>
    <div class="variant-spec">
      <code>variant="primary"</code>
    </div>
  </article>
  <!-- repeat per variant -->
</div>
```

---

## Props table recipe

```html
<table class="props-table">
  <thead>
    <tr><th>Prop</th><th>Type</th><th>Default</th><th>Description</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><code>variant</code></td>
      <td><span class="type-union">'primary' | 'secondary' | 'tertiary'</span></td>
      <td><code>'primary'</code></td>
      <td>Visual intent.</td>
    </tr>
  </tbody>
</table>
```

Color-code types:
- `.type-string` (green)
- `.type-number` (orange)
- `.type-boolean` (purple)
- `.type-union` (blue)
- `.type-fn` (grey)

---

## Tokens-used table

```html
<div class="token-table">
  <div class="token-row head">
    <span>Token</span><span>Raw value</span><span>Where</span>
  </div>
  <div class="token-row">
    <span class="label"><code>--orange</code></span>
    <span class="val">#F26522</span>
    <span>Primary button background</span>
  </div>
</div>
```

---

## Anatomy diagram

Two approaches:
1. **Inline SVG** with labels and callout lines (best for complex)
2. **HTML overlay** — component rendered + absolute-positioned label dots with lines (best for simple)

Don't screenshot. Keep editable.
