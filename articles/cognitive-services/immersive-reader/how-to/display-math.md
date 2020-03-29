---
title: Matematikai megjelenítés a magával ragadó olvasóban
titleSuffix: Azure Cognitive Services
description: Ez a cikk megmutatja, hogyan jelenítheti meg a matematikát a Magával ragadó olvasóban.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946121"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Hogyan jeleníthető meg a matematika a magával ragadó olvasó

A Magával ragadó olvasó matematikai jelölőnyelv[(MathML)](https://developer.mozilla.org/docs/Web/MathML)formájában jeleníti meg a matematikát.
A MIME típus a Immersive Reader [adattömbön](../reference.md#chunk)keresztül állítható be. További információt a [támogatott MIME-típusokban](../reference.md#supported-mime-types) talál.

## <a name="send-math-to-the-immersive-reader"></a>Matematikai küldés a magával ragadó olvasónak
Annak érdekében, hogy a matematikát elküldhesse a Magával ragadó ```application/mathml+xml```olvasónak, adja meg a MathML-et tartalmazó adattömböt, és állítsa a MIME típust a következőre:

Ha például a tartalom a következő:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Ezután a következő JavaScript használatával jelenítheti meg a tartalmat.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Amikor elindítja a Magával ragadó olvasót, látnia kell a következőket:

![Matematika magával ragadó olvasó](../media/how-tos/1-math.png)

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](../reference.md)