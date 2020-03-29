---
title: Magával ragadó olvasó SDK referencia
titleSuffix: Azure Cognitive Services
description: A magával ragadó Reader SDK tartalmaz egy JavaScript könyvtár, amely lehetővé teszi, hogy integrálja a magával ragadó reader az alkalmazásba.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156403"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Magával ragadó olvasó SDK referencia útmutató

A magával ragadó Reader SDK tartalmaz egy JavaScript könyvtár, amely lehetővé teszi, hogy integrálja a magával ragadó reader az alkalmazásba.

## <a name="functions"></a>Functions

Az SDK a következő funkciókat teszi elérhetővé:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Elindítja a magával `iframe` ragadó olvasó belül egy a webes alkalmazás.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Paraméterek

| Név | Típus | Leírás |
| ---- | ---- |------------ |
| `token` | sztring | Az Azure AD hitelesítési jogkivonat. |
| `subdomain` | sztring | A Magával ragadó olvasó erőforrás egyéni altartománya az Azure-ban. |
| `content` | [Tartalom](#content) | A Magával ragadó olvasóban megjelenítendő tartalmat tartalmazó objektum. |
| `options` | [Beállítások](#options) | A magával ragadó olvasó bizonyos viselkedésének konfigurálása. Választható. |

### <a name="returns"></a>Visszatérési érték

A `Promise<LaunchResponse>`értéket ad vissza, amely a Immersive Reader betöltésekor megszűnik. A `Promise` határozat egy [`LaunchResponse`](#launchresponse) objektummá.

### <a name="exceptions"></a>Kivételek

Ha `Promise` a Magával ragadó [`Error`](#error) olvasó nem töltődik be, a visszaküldött objektum ot elutasítja a program egy objektummal. További információt a [hibakódokban](#error-codes)talál.

## <a name="close"></a>bezárás

Bezárja a magával ragadó olvasót.

Ennek a funkciónak az egyik használati esete, ```hideExitButton: true``` ha a kilépési gomb rejtett a [beállítások](#options)beállításával. Ezután egy másik gomb (például egy mobil fejléc ```close``` visszanyila) hívhatja ezt a funkciót, amikor rákattintanak.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderelésgombok

Ez a funkció stílusok és frissíti a dokumentum magával ragadó Olvasó gomb elemeket. Ha ```options.elements``` van megadva, akkor ez ```options.elements```a funkció a gombokat rendereli a területen belül. Ellenkező esetben a gombok a dokumentum azon elemeiben ```immersive-reader-button```jelennek meg, amelyek osztályt tartalmaznak.

Ezt a funkciót az ablak betöltésekekén automatikusan megnevezi az SDK.

További [megjelenítési](#optional-attributes) beállításokért lásd: Választható attribútumok.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Paraméterek

| Név | Típus | Leírás |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | A renderButtons függvény bizonyos viselkedésének konfigurálása. Választható. |

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A Magával ragadó olvasóban megjelenendő tartalmat tartalmazza.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Adattömb

Egyetlen adattömb, amely átkerül a Immersive Reader tartalmába.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse (LaunchResponse)

A hívás ból érkező `ImmersiveReader.launchAsync`választ tartalmazza.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy felsorak

A Immersive Reader cookie-használatának irányelveit beállító felsorítás. Lásd a [beállításokat.](#options)

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Leírás |
| --------- | ----------- |
| szöveg/egyszerű | Egyszerű szöveg. |
| szöveg/html | HTML-tartalom. [További információ](#html-support)|
| alkalmazás/mathml+xml | Matematikai korrektúranyelv (MathML). [További információ](./how-to/display-math.md).
| alkalmazás/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word .docx formátumú dokumentum.

### <a name="html-support"></a>HTML-támogatás

| HTML | Támogatott tartalom |
| --------- | ----------- |
| Betűstílusok | Félkövér, dőlt, Aláhúzott, Kód, Áthúzott, Felső index, Alsó index |
| Rendezetlen listák | Lemez, Kör, Négyzet |
| Rendezett listák | Decimális, Felső-Alfa, Alsó-Alfa, Felső-Római, Alsó-Római |
| Hivatkozások | Hamarosan |

A nem támogatott címkék összehasonlítva jelennek meg. A képek és a táblázatok jelenleg nem támogatottak.

### <a name="options"></a>Beállítások

Olyan tulajdonságokat tartalmaz, amelyek a magával ragadó olvasó bizonyos viselkedését konfigurálják.

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

A Magával ragadó olvasó gombjainak renderelésének lehetőségei.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Hiba

A hibával kapcsolatos információkat tartalmazza.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Hibakódok

| Kód | Leírás |
| ---- | ----------- |
| BadArgument (Rossz argumentum) | A megadott argumentum `message` érvénytelen, további részletek. |
| Időkorlát | A Immersive Reader nem tudta betölteni a megadott időtúlidőn belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Gázadagoló | A hívássebesség-korlát túllépve. |

## <a name="launching-the-immersive-reader"></a>A magával ragadó olvasó indítása

Az SDK biztosítja az alapértelmezett stílust a bemerített olvasó indításához. A `immersive-reader-button` stílus engedélyezéséhez használja az osztályattribútumot. Lásd ezt a [cikket](./how-to-customize-launch-button.md) további részletekért.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Választható attribútumok

A gomb megjelenésének és tapintásának konfigurálásához használja az alábbi attribútumokat.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. Lehet `icon`, `text`vagy `iconAndText`. Alapértelmezés szerint `icon`a. |
| `data-locale` | Beállítja a területi beállításokat. Például `en-US` vagy `fr-FR`. Alapértelmezés szerint `en`az angol . |
| `data-icon-px-size` | Az ikon méretét képpontban állítja be. Alapértelmezés szerint 20px. |

## <a name="browser-support"></a>Böngészőtámogatás

Használja a következő böngészők legújabb verzióit a magával ragadó olvasóval való legjobb élmény érdekében.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t a GitHubon](https://github.com/microsoft/immersive-reader-sdk)
* [Rövid útmutató: Hozzon létre egy webes alkalmazást, amely elindítja a Magával ragadó olvasót (C#)](./quickstart.md)
