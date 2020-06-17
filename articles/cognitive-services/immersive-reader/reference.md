---
title: A részletes olvasó SDK-referenciája
titleSuffix: Azure Cognitive Services
description: A lebilincselő olvasó SDK egy JavaScript-függvénytárat tartalmaz, amely lehetővé teszi a magával ragadó olvasó integrálását az alkalmazásba.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 5b1471cc43fc506ca798e81ac8e35a5051278ee0
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907380"
---
# <a name="immersive-reader-sdk-reference-guide"></a>A részletes olvasó SDK útmutatója

A lebilincselő olvasó SDK egy JavaScript-függvénytárat tartalmaz, amely lehetővé teszi a magával ragadó olvasó integrálását az alkalmazásba.

## <a name="functions"></a>Functions

Az SDK a függvényeket teszi elérhetővé:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Elindítja az olvasót a `iframe` webalkalmazáson belül.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Paraméterek

| Name | Típus | Description |
| ---- | ---- |------------ |
| `token` | sztring | Az Azure AD hitelesítési jogkivonata. |
| `subdomain` | sztring | Az Azure-beli magától elolvasó erőforrás egyedi altartománya. |
| `content` | [Tartalom](#content) | Egy objektum, amely a magába foglaló olvasóban megjelenítendő tartalmat tartalmazza. |
| `options` | [Beállítások](#options) | Beállítások a magával ragadó olvasó bizonyos viselkedésének konfigurálásához. Választható. |

### <a name="returns"></a>Válaszok

A egy értéket ad vissza `Promise<LaunchResponse>` , amely feloldja a magával ragadó olvasó betöltését. A `Promise` feloldás egy [`LaunchResponse`](#launchresponse) objektumra.

### <a name="exceptions"></a>Kivételek

A visszaadott objektum akkor kerül `Promise` elutasításra, [`Error`](#error) Ha a magával ragadó olvasó nem töltődik be. További információ: [hibakódok](#error-codes).

## <a name="close"></a>bezárás

A magával ragadó olvasó bezárása.

Példa erre a függvényre, ha a kilépési gomb el van rejtve ```hideExitButton: true``` a [Beállítások](#options)beállításban. Ezután egy másik gomb (például a mobil fejléc vissza nyíl) hívhatja ezt a ```close``` függvényt, amikor rákattint.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Ez a függvény stílusokat és frissítéseket frissít a dokumentum az olvasó gombjának elemeivel. Ha meg ```options.elements``` van adni, a függvény megjeleníti a gombokat a következőn belül: ```options.elements``` . Ellenkező esetben a gombok a dokumentum azon elemein belül jelennek meg, amelyeknek az osztálya van ```immersive-reader-button``` .

Az SDK automatikusan ezt a függvényt hívja meg, amikor az ablak betöltődik.

További megjelenítési beállításokért lásd: [opcionális attribútumok](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Paraméterek

| Name | Típus | Leírás |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | A renderButtons függvény bizonyos viselkedésének konfigurálására szolgáló beállítások. Választható. |

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A lebilincselő olvasóban megjelenítendő tartalmat tartalmazza.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Adattömb

Egyetlen adathalmaz, amely a magára az olvasóba kerül át a tartalomba.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

A hívásának válaszát tartalmazza `ImmersiveReader.launchAsync` . Vegye figyelembe, hogy a `iframe` magával ragadó olvasót tartalmazó hivatkozás a használatával érhető el `container.firstChild` .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy enumerálása

Az olvasó cookie-k használatára vonatkozó szabályzat beállítására szolgáló enumerálás. Lásd: [Beállítások](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Leírás |
| --------- | ----------- |
| szöveg/egyszerű | Egyszerű szöveg. |
| szöveg/html | HTML-tartalom. [További információ](#html-support)|
| Application/MathML + XML | Matematikai Markup Language (MathML). [További információ](./how-to/display-math.md).
| alkalmazás/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word. docx formátumú dokumentum.

### <a name="html-support"></a>HTML-támogatás

| HTML | Támogatott tartalom |
| --------- | ----------- |
| Betűstílusok | Félkövér, dőlt, aláhúzás, kód, áthúzott, felső, alsó index |
| Rendezetlen listák | Lemez, kör, négyzet |
| Rendezett felsorolások | Decimális, felső-alfa, alsó-alfa, felső-római, alsó-római |

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
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
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

| Code | Leírás |
| ---- | ----------- |
| BadArgument | A megadott argumentum érvénytelen `message` . a részletekért tekintse meg a következőt:. |
| Időtúllépés | Nem sikerült betölteni a magával ragadó olvasót a megadott időkorláton belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Szabályozott | Túllépte a hívási sebesség korlátját. |

## <a name="launching-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Az SDK alapértelmezett stílust biztosít a magával ragadó olvasó indítására szolgáló gombhoz. A `immersive-reader-button` Class attribútum használatával engedélyezze ezt a stílust. További részletekért tekintse meg [ezt a cikket](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Nem kötelező attribútumok

A gomb megjelenésének és működésének konfigurálásához használja a következő attribútumokat.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. Lehet `icon` , `text` , vagy `iconAndText` . Alapértelmezett értéke `icon` . |
| `data-locale` | Beállítja a területi beállítást. Például `en-US` vagy `fr-FR`. Az alapértelmezett érték az angol `en` . |
| `data-icon-px-size` | Beállítja az ikon méretét képpontban megadva. Az alapértelmezett érték a 20px. |

## <a name="browser-support"></a>Böngészőtámogatás

Használja az alábbi böngészők legújabb verzióit a legjobb élmény érdekében a teljes olvasóval.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>További lépések

* Ismerje [meg az olvasót a githubon](https://github.com/microsoft/immersive-reader-sdk)
* [Rövid útmutató: hozzon létre egy webalkalmazást, amely elindítja a részletes olvasót (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
