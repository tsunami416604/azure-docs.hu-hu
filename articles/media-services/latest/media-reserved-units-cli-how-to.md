---
title: Media szolgáltatás számára fenntartott egységek méretezése a CLI-vel – Azure | Microsoft Docs
description: Ez a témakör bemutatja, hogyan méretezheti a CLI-t a médiafájlok feldolgozásának méretezésére Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 79f2df0f94b212ea45c01c825b23f1dbb4cc40db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582292"
---
# <a name="scaling-media-processing"></a>Médiafeldolgozás skálázása

Az Azure Media Services a fenntartott médiaegységek (MRU-k) kezelésével lehetővé teszi a médiafeldolgozás skálázását a fiókjában. A MRUs határozzák meg, hogy milyen sebességgel dolgozza fel a rendszer a média feldolgozási feladatait. A következő fenntartott egység típusok közül választhat: **S1**, **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. 

A fenntartott egység típusának meghatározása mellett megadhatja, hogy a fiók a fenntartott egységekkel legyen kiépítve. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiókja öt fenntartott egységgel rendelkezik, akkor az öt adathordozó-feladat egyszerre fut, amíg a feladatok feldolgozására sor kerül. A hátralévő feladatok megvárhatják a várólistán, és a folyamatban lévő tevékenységek befejezését követően is felveszik a feldolgozást. Ha egy fiókhoz nincs kiépítve fenntartott egység, a rendszer szekvenciálisan felveszi a tevékenységeket. Ebben az esetben a várakozási idő egy feladat befejezése és a következő kezdés a rendszer erőforrásainak rendelkezésre állása alapján fog függeni.

## <a name="choosing-between-different-reserved-unit-types"></a>Különböző fenntartott egységek típusának kiválasztása

A következő táblázat segít dönteni a különböző kódolási sebességek kiválasztásakor. Emellett néhány teljesítményteszt-esetet is biztosít [egy videón, amelyet](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) a saját tesztek elvégzéséhez tölthet le:

|RU-típus|Forgatókönyv|Példa a [7 perces 1080p videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) eredményeire|
|---|---|---|
| **S1**|Egyetlen bitráta kódolása. <br/>A fájlok SD-vagy alacsonyabb felbontásban, nem érzékenyek, alacsonyak.|A "H264 Single bitráta SD 16x9" használatával az egyetlen sávszélességű SD-feloldási MP4-fájl kódolása körülbelül 7 percet vesz igénybe.|
| **S2**|Egyetlen bitráta és több bitráta-kódolás.<br/>Normál használat SD és HD kódoláshoz.|A "H264 Single bitráta 720p" beállítású kódolás körülbelül 6 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 720p" beállítású kódolás körülbelül 12 percet vesz igénybe.|
| **S3**|Egyetlen bitráta és több bitráta-kódolás.<br/>Teljes HD-és 4K-felbontású videók. Időérzékeny, gyorsabb átfutási idő kódolása.|A "H264 Single bitráta 1080p" beállítással rendelkező kódolás körülbelül 3 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 1080p" beállítással rendelkező kódolás körülbelül 8 percet vesz igénybe.|

## <a name="considerations"></a>Megfontolandó szempontok

* Az Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatokhoz az S3 egység típusa kifejezetten ajánlott.
* Ha a megosztott készletet használja, azaz a fenntartott egységek nélkül, akkor a kódolási feladatok ugyanolyan teljesítménnyel rendelkeznek, mint az S1 RUs. Azonban nincs felső korlát ahhoz az időponthoz, ameddig a tevékenységek várólistán lévő állapotba kerülnek, és egy adott időpontban egyetlen feladat is fut.

A cikk további részében bemutatjuk, hogyan méretezhető a MRUs a [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) használatával.

> [!NOTE]
> A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>A Media szolgáltatás számára fenntartott egységek méretezése a parancssori felülettel

Futtassa a következő parancsot: `mru`.

A következő az [AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) parancs beállítja a Media szolgáltatás számára fenntartott egységeket a "amsaccount" fiókban a **Count** és a **Type** paraméterek használatával.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Számlázás

A számlázás a fiókban a Media szolgáltatás számára fenntartott egységek száma alapján történik. Ez attól függetlenül történik, hogy van-e a fiókjában futó feladat. Részletes magyarázatért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

* [Kvóták és korlátok](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
