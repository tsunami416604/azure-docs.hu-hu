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
ms.openlocfilehash: 1d9fc20055fe3adb571b5a77330cc6537998cb5f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534469"
---
# <a name="immersive-reader-sdk-reference"></a>A részletes olvasó SDK-referenciája

A lebilincselő olvasó SDK egy JavaScript-kódtár, amely lehetővé teszi a magával ragadó olvasó integrálását a webalkalmazásba.

## <a name="functions"></a>Funkciók

Az SDK egyetlen függvényt `ImmersiveReader.launchAsync(token, subdomain, content, options)`tesz elérhetővé.

### <a name="launchasync"></a>launchAsync

Elindítja az olvasót a webalkalmazáson belül `iframe` .

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
| ---- | ---- |------------ |
| `token` | Karakterlánc | Az Azure AD hitelesítési jogkivonata. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `subdomain` | Karakterlánc | Az Azure-beli magától elolvasó erőforrás egyedi altartománya. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `content` | [Tartalom](#content) | Egy objektum, amely a magába foglaló olvasóban megjelenítendő tartalmat tartalmazza. |
| `options` | [Beállítások](#options) | Beállítások a magával ragadó olvasó bizonyos viselkedésének konfigurálásához. Nem kötelező. |

#### <a name="returns"></a>Visszatérési érték

Egy olyan `Promise<HTMLDivElement>` értéket ad vissza, amely feloldja a magával ragadó olvasó betöltését. A `Promise` megoldás egy `div` olyan elemre lesz feloldva, amelynek csak `iframe` a gyermeke egy olyan elem, amely tartalmazza az olvasó oldalát.

#### <a name="exceptions"></a>Kivételek

A visszaadott `Promise` [`Error`](#error) objektum akkor kerül elutasításra, ha a magával ragadó olvasó nem töltődik be. További információ: hibakódok. [](#error-codes)

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A lebilincselő olvasóban megjelenítendő tartalmat tartalmazza.

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
| szöveg/egyszerű | Egyszerű szöveg. |
| Application/MathML + XML | Matematikai Markup Language (MathML). [További információk](https://developer.mozilla.org/en-US/docs/Web/MathML).
| Application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Microsoft Word. docx formátumú dokumentum.

### <a name="options"></a>Beállítások

Azokat a tulajdonságokat tartalmazza, amelyek a magába foglaló olvasó bizonyos viselkedéseit konfigurálják.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
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
| BadArgument | A megadott argumentum érvénytelen. a `message` részletekért tekintse meg a következőt:. |
| Időtúllépés | Nem sikerült betölteni a magával ragadó olvasót a megadott időkorláton belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Szabályozott | Túllépte a hívási sebesség korlátját. |

## <a name="launching-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Az SDK alapértelmezett stílust biztosít a magával ragadó olvasó indítására szolgáló gombhoz. A `immersive-reader-button` Class attribútum használatával engedélyezze ezt a stílust.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Nem kötelező attribútumok

A gomb megjelenésének és működésének konfigurálásához használja a következő attribútumokat.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. `icon`Lehet, `text`, vagy `iconAndText`. Alapértelmezés szerint a `icon`. |
| `data-locale` | A területi beállítás, például `en-US` `fr-FR`:. Az alapértelmezett érték az angol. |
| `data-icon-px-size` | Beállítja az ikon méretét képpontban megadva. Az alapértelmezett érték a 20px. |

## <a name="browser-support"></a>Böngésző-támogatás

Használja az alábbi böngészők legújabb verzióit a legjobb élmény érdekében a teljes olvasóval.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>További lépések

* Ismerje [meg](https://github.com/microsoft/immersive-reader-sdk) az olvasót a githubon
* [Rövid útmutató: Hozzon létre egy webalkalmazást, amely elindítja aC#lebilincselő olvasót ()](./quickstart.md)