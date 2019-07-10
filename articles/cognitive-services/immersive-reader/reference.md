---
title: Ragadó olvasó SDK-referencia
titleSuffix: Azure Cognitive Services
description: A ragadó olvasó SDK-referencia
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718388"
---
# <a name="immersive-reader-sdk-reference"></a>Ragadó olvasó SDK-referencia

Az olvasó ragadó SDK egy JavaScript-függvénytárat, amely lehetővé teszi, hogy a ragadó olvasó integrálható a webes alkalmazások.

## <a name="functions"></a>Functions

Az SDK-t tesz elérhetővé egyetlen függvényben `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Elindítja a ragadó olvasó belül egy `iframe` webalkalmazásban.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
| ---- | ---- |------------ |
| `token` | Karakterlánc | A hozzáférési jogkivonatot szerezni a hívást a `issueToken` végpont. |
| `resourceName` | sztring | Fenntartva. Meg kell `null`. |
| `content` | [Tartalom](#content) | A ragadó olvasó jelennek meg a tartalmat tartalmazó objektumot. |
| `options` | [Beállítások](#options) | Beállítások konfigurálása a ragadó olvasó bizonyos viselkedéseinek. Választható. |

#### <a name="returns"></a>adja vissza

Értéket ad vissza egy `Promise<HTMLDivElement>` amely szünteti meg, amikor a ragadó olvasó be van töltve. A `Promise` feloldja egy `div` elem, amelynek egyetlen alárendelt van egy `iframe` elem, amely tartalmazza a ragadó olvasó lapot.

#### <a name="exceptions"></a>Kivételek

A visszaadott `Promise` a rendszer elutasítja az [ `Error` ](#error) objektumot, ha a ragadó olvasó nem sikerül betölteni. További információkért lásd: a [hibakódok](#error-codes).

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A tartalma a ragadó olvasó jelennek meg.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Leírás |
| --------- | ----------- |
| egyszerű szöveg | Egyszerű szöveg. |
| alkalmazás/mathml + xml | Matematikai Markup Language (MathML). [További információk](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Beállítások

A ragadó olvasó bizonyos viselkedéseinek konfiguráló tulajdonságait tartalmazza.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
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
| BadArgument | A megadott argumentum érvénytelen, lásd: `message` részleteiről. |
| Időtúllépés | A ragadó olvasó nem sikerült betölteni a megadott időkorláton belül. |
| TokenExpired| A megadott jogkivonat lejárt. |

## <a name="launching-the-immersive-reader"></a>A ragadó olvasó indítása

Az SDK alapértelmezett stílusának indításakor a ragadó olvasó gomb biztosít. Használja a `immersive-reader-button` osztály attribútumot a Stílusszerkesztő engedélyezéséhez.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Nem kötelező attribútumok

A következő attribútumok segítségével konfigurálhatja a gomb megjelenését és működését.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. Lehet `icon`, `text`, vagy `iconAndText`. Alapértelmezés szerint a `icon`. |
| `data-locale` | A területi beállítása például `en-US`, `fr-FR`. Az alapértelmezett angol nyelven. |
| `data-icon-px-size` | Beállítja az ikon méretét (képpontban). Az alapértelmezett 20px. |

## <a name="browser-support"></a>Böngésző támogatása

Az alábbi böngészők legújabb verzióját használja a legjobb élményt a ragadó Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>További lépések

* Fedezze fel a [ragadó olvasó SDK a Githubon](https://github.com/Microsoft/immersive-reader-sdk)
* [Rövid útmutató: Hozzon létre egy webalkalmazást, amely elindítja a ragadó Reader (C#)](./quickstart.md)