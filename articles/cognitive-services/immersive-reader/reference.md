---
title: A részletes olvasó SDK-referenciája
titleSuffix: Azure Cognitive Services
description: A alámerülés olvasó SDK-ra vonatkozó referenciák
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b25a002cb1e2563ab97a2081c6b6a05362b66779
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338515"
---
# <a name="immersive-reader-sdk-reference"></a>A részletes olvasó SDK-referenciája

A lebilincselő olvasó SDK egy JavaScript-kódtár, amely lehetővé teszi a magával ragadó olvasó integrálását a webalkalmazásba.

# <a name="functions"></a>Funkciók

Az SDK a függvényeket teszi elérhetővé:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

A webalkalmazásban a `iframe` értéken belül elindítja a lebilincselő olvasót.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
| ---- | ---- |------------ |
| `token` | Karakterlánc | Az Azure AD hitelesítési jogkivonata. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `subdomain` | Karakterlánc | Az Azure-beli magától elolvasó erőforrás egyedi altartománya. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `content` | [Tartalom](#content) | Egy objektum, amely a magába foglaló olvasóban megjelenítendő tartalmat tartalmazza. |
| `options` | [Beállítások](#options) | Beállítások a magával ragadó olvasó bizonyos viselkedésének konfigurálásához. Nem kötelező. |

### <a name="returns"></a>Visszatérési érték

Egy `Promise<HTMLDivElement>` értéket ad vissza, amely feloldja a magával ragadó olvasó betöltését. A `Promise` olyan `div` elemre oldódik fel, amelynek csak a gyermeke egy `iframe` elem, amely tartalmazza az olvasói oldalt.

### <a name="exceptions"></a>Kivételek

A visszaadott `Promise` egy [`Error`](#error) objektummal lesz elutasítva, ha a magával ragadó olvasó nem töltődik be. További információ: [hibakódok](#error-codes).

## <a name="close"></a>bezárás

A magával ragadó olvasó bezárása.

Példa erre a függvényre, ha a kilépési gomb el van rejtve ```hideExitButton: true``` beállításával a [Beállítások](#options)között. Ezután egy másik gomb (például egy mobil fejléc vissza nyíl) hívhatja ezt a ```close``` függvényt, amikor rákattint.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Ez a függvény stílusokat és frissítéseket frissít a dokumentum az olvasó gombjának elemeivel. Ha ```options.elements``` van megadva, akkor ez a függvény a ```options.elements``` értéken belül jeleníti meg a gombokat. Ellenkező esetben a gombok a dokumentum azon elemein belül jelennek meg, amelyeknek ```immersive-reader-button``` osztálya van.

Az SDK automatikusan ezt a függvényt hívja meg, amikor az ablak betöltődik.

További megjelenítési beállításokért lásd: [opcionális attribútumok](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | A renderButtons függvény bizonyos viselkedésének konfigurálására szolgáló beállítások. Nem kötelező. |

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A lebilincselő olvasóban megjelenítendő tartalmat tartalmazza.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Darab

Egyetlen adathalmaz, amely a magára az olvasóba kerül át a tartalomba.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Leírás |
| --------- | ----------- |
| szöveg/egyszerű | Egyszerű szöveg. |
| szöveg/html | HTML-tartalom. [További információ](#html-support)|
| Application/MathML + XML | Matematikai Markup Language (MathML). [További információk](https://developer.mozilla.org/en-US/docs/Web/MathML).
| Application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Microsoft Word. docx formátumú dokumentum.

### <a name="html-support"></a>HTML-támogatás
| HTML | Támogatott tartalom |
| --------- | ----------- |
| Betűstílusok | Félkövér, dőlt, aláhúzás, kód, áthúzott, felső, alsó index |
| Rendezetlen felsorolások | Lemez, kör, négyzet |
| Rendezett felsorolások | Decimális, felső-alfa, alsó-alfa, felső-római, alsó-római |
| Hivatkozások | Hamarosan |

A nem támogatott címkék hasonlóan lesznek megjelenítve. A képek és a táblázatok jelenleg nem támogatottak.

### <a name="options"></a>Beállítások

Azokat a tulajdonságokat tartalmazza, amelyek a magába foglaló olvasó bizonyos viselkedéseit konfigurálják.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

A magával ragadó olvasó gombok megjelenítésének lehetőségei.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Hiba

A hibával kapcsolatos információkat tartalmaz.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Hibakódok

| Kód | Leírás |
| ---- | ----------- |
| BadArgument | A megadott argumentum érvénytelen. a részletekért tekintse meg a `message` értéket. |
| Időtúllépés | Nem sikerült betölteni a magával ragadó olvasót a megadott időkorláton belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Szabályozott | Túllépte a hívási sebesség korlátját. |

## <a name="launching-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Az SDK alapértelmezett stílust biztosít a magával ragadó olvasó indítására szolgáló gombhoz. Ezt a stílust a `immersive-reader-button` Class attribútum használatával engedélyezheti.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Nem kötelező attribútumok

A gomb megjelenésének és működésének konfigurálásához használja a következő attribútumokat.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. `icon`Lehet, `text`, vagy `iconAndText`. Alapértelmezés szerint a `icon`. |
| `data-locale` | Beállítja a területi beállítást. Ha például `en-US` vagy `fr-FR`. Az alapértelmezett érték az angol `en`. |
| `data-icon-px-size` | Beállítja az ikon méretét képpontban megadva. Az alapértelmezett érték a 20px. |

## <a name="browser-support"></a>Böngésző-támogatás

Használja az alábbi böngészők legújabb verzióit a legjobb élmény érdekében a teljes olvasóval.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>További lépések

* Ismerje [meg az olvasót a githubon](https://github.com/microsoft/immersive-reader-sdk)
* [Rövid útmutató: Hozzon létre egy webalkalmazást, amely elindítja aC#lebilincselő olvasót () ](./quickstart.md)