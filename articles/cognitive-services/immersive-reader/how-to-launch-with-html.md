---
title: A Modern olvasó indítása HTML-tartalommal
titleSuffix: Azure Cognitive Services
description: Ez a cikk megmutatja, hogyan indíthatja el a Magával ragadó olvasóhtml tartalommal.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946242"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>A Magával ragadó olvasó HTML-tartalommal való elindítása

Ez a cikk bemutatja, hogyan indíthatja el a Magával ragadó olvasó HTML-tartalommal.

## <a name="prepare-the-html-content"></a>A HTML-tartalom előkészítése

Helyezze a megjeleníteni kívánt tartalmat a Magával ragadó olvasóba egy tárolóelembelsejében. Győződjön meg arról, `id`hogy a tárolóelem egyedi . A Magával ragadó olvasó támogatja az alapvető HTML-elemeket, további információt a [hivatkozásban](./reference.md#html-support) talál.

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

## <a name="get-the-html-content-in-javascript"></a>A HTML-tartalom beszereznie JavaScriptben

Használja `id` a tároló elem, hogy a HTML-tartalom a JavaScript-kódot.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>A Magával ragadó olvasó elindítása a HTML-tartalommal

Híváskor `ImmersiveReader.launchAsync`állítsa be az `mimeType` adattömb tulajdonságát úgy, hogy `text/html` engedélyezze a HTML megjelenítését.

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

* Fedezze fel a [magával ragadó Reader SDK-referenciát](./reference.md)