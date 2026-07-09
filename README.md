# La Melania & Ayan — Snackbar, Videle

Pagină HTML5 autonomă, fără framework și fără pași de build. Se deschide direct în browser
și se poate muta ca atare pe un server cu PHP.

## Fișiere

| Fișier | Ce e | Când îl folosești |
|---|---|---|
| `index.html` | 178 KB, pozele în `assets/` | **Pe server.** Pozele se pot memora în cache separat. |
| `index.standalone.html` | 2,3 MB, tot inline (poze + video) | Un singur fișier, de trimis pe e-mail sau de deschis offline. |
| `assets/` | 14 imagini, 837 KB | Folosite de `index.html`. |
| `hero-video.mp4` | 640×360, 5 s | Fundalul secțiunii de sus. |
| `logo.png` | sigla originală | Nefolosită direct; varianta decupată e `assets/logo.png`. |

## Cum treci pe PHP

Tot ce ține de date stă într-un singur loc, la începutul primului `<script>`:

```js
const CATEGORII = [ ... ];   // 11 categorii
const PRODUSE   = [ ... ];   // 42 de produse
```

Înlocuiește-le cu ce scoate PHP-ul din baza de date:

```php
<script>
const CATEGORII = <?= json_encode($categorii, JSON_UNESCAPED_UNICODE) ?>;
const PRODUSE   = <?= json_encode($produse,   JSON_UNESCAPED_UNICODE) ?>;
</script>
```

Restul paginii se redesenează singură din aceste două tablouri: grila, filtrele,
numărătoarea, modalul, bonul, meniul zilei.

Cele două formulare (`#reserveForm` și butonul `#checkout`) nu trimit nimic nicăieri —
doar arată o confirmare. Leagă-le de un `fetch()` către endpoint-urile tale.

## Ce trebuie schimbat înainte de publicare

- **Datele firmei** din subsol (`ROBERT SNACK` a fost înlocuit cu `MELANIA & AYAN SNACK S.R.L.`,
  dar CUI-ul, numărul de la Registrul Comerțului, adresa și telefonul sunt **inventate**, marcate ca exemplu).
- **Adresa și coordonatele**: `Strada Republicii 18, Videle` și `44.2833, 25.5333` sunt plauzibile, nu reale.
  Apar în: `<script type="application/ld+json">`, secțiunea Contact, linkurile Google/Waze/Apple.
- **Recenziile** sunt scrise de noi, cu portrete generate. Înlocuiește-le cu recenzii reale
  sau scoate secțiunea — altfel e publicitate înșelătoare.
- **Codul QR** din secțiunea de sus duce la `https://melaniasiayan.ro/masa/12`. E un cod real,
  scanabil. Regenerează-l când ai domeniul.
- **Sigla** are două greșeli de tipar în desen: `RACORITDARE` (corect: *RĂCORITOARE*) și
  `POPCORN.` cu punct în loc de virgulă. La dimensiunea din antet nu se citesc; în subsol, da.

## Detalii de implementare

- **Temă**: `data-theme` pe `<html>`, pus înainte de prima pictare de un script mic din `<head>`
  (fără clipire). Comutatorul bate `prefers-color-scheme`, în ambele direcții.
- **Bonul** (coșul) e ținut în `localStorage`, cheia `may.cos`; e curățat și limitat la 99 bucăți pe linie.
- **Programul** e calculat în `stare()`, inclusiv trecerea peste miezul nopții (vineri și sâmbătă până la 01:00).
- **Happy Hour**: fereastra de seară se prelungește până la 22:00 vinerea și sâmbăta, exact cum scrie pe card.
- **Harta Google** se încarcă doar după consimțământ (`may.cookies === 'all'`) sau la apăsarea butonului.
  Până atunci se vede harta desenată în SVG. Dacă un CSP strict blochează iframe-ul, se cade tot pe SVG.
- **Rețeaua de pe fundal** (`#weave`) e un `<canvas>`: iconițele stau pe loc, lumina curge pe linii.
  Se oprește la `prefers-reduced-motion` și când fila nu e vizibilă.
- **Intro-ul** de 2 s rulează o singură dată pe sesiune (`sessionStorage`), nu la fiecare reîncărcare.
