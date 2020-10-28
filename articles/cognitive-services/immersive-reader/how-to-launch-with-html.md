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
ms.author: metang
ms.custom: devx-track-js
ms.openlocfilehash: d028e9ef2087b866b3c168483d55556e38f9a7cd
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636561"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>A lebilincselő olvasó HTML-tartalommal való elindítása

Ez a cikk bemutatja, hogyan indíthatja el a teljes olvasót HTML-tartalommal.

## <a name="prepare-the-html-content"></a>A HTML-tartalom előkészítése

Helyezze el a megjelenő tartalmat a tároló elemen belül a magára az olvasóba. Ügyeljen arra, hogy a Container elem egyedi legyen `id` . Az egyszerű HTML-elemek támogatását a magától megtekintő olvasó nyújtja, további információért lásd a [referenciát](./reference.md#html-support) .

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

A `id` tároló elem használatával lekérheti a HTML-tartalmat a JavaScript-kódjában.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>A részletes olvasó elindítása a HTML-tartalommal

A híváskor `ImmersiveReader.launchAsync` az adathalmaz tulajdonságát állítsa be a `mimeType` `text/html` HTML megjelenítésének engedélyezéséhez.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)