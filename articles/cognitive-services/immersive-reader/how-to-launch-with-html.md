---
title: A Modern olvasó indítása HTML-tartalommal
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan indíthatja el a teljes olvasót HTML-tartalommal.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946242"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>A lebilincselő olvasó HTML-tartalommal való elindítása

Ez a cikk bemutatja, hogyan indíthatja el a teljes olvasót HTML-tartalommal.

## <a name="prepare-the-html-content"></a>A HTML-tartalom előkészítése

Helyezze el a megjelenő tartalmat a tároló elemen belül a magára az olvasóba. Ügyeljen arra, hogy a Container elem egyedi `id`legyen. Az egyszerű HTML-elemek támogatását a magától megtekintő olvasó nyújtja, további információért lásd a [referenciát](./reference.md#html-support) .

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>HTML-tartalom beolvasása a JavaScriptben

`id` A tároló elem használatával lekérheti a HTML-tartalmat a JavaScript-kódjában.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>A részletes olvasó elindítása a HTML-tartalommal

A híváskor `ImmersiveReader.launchAsync`az adathalmaz `mimeType` tulajdonságát `text/html` állítsa be a HTML megjelenítésének engedélyezéséhez.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>További lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)