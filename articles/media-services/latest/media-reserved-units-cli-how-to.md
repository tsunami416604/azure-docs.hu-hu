---
title: A CLI használata a media által fenntartott egységek méretezéséhez - Azure | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja a CLI-t az Azure Media Services használatával a médiafeldolgozás méretezéséhez.
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
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582292"
---
# <a name="scaling-media-processing"></a>Médiafeldolgozás skálázása

Az Azure Media Services a fenntartott médiaegységek (MRU-k) kezelésével lehetővé teszi a médiafeldolgozás skálázását a fiókjában. Az MRUs határozza meg a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egységtípusok közül választhat: **S1,** **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. 

A fenntartott egység típusának megadása mellett megadhatja, hogy a fiók fenntartott egységekkel is kiépítse.In additioning the reserved unit, you can specify to provision your account with reserved units. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiók öt fenntartott egységgel rendelkezik, akkor öt médiafeladat fog egyidejűleg futni, amíg vannak feldolgozandó feladatok. A fennmaradó feladatok a várólistában várnak, és a futó feladat befejezésekor egymás után kerülnek feldolgozásra. Ha egy fióknem rendelkezik kiépített fenntartott egységekkel, akkor a feladatok at egymás után veszi késve. Ebben az esetben az egyik tevékenység befejezése és a következő indítás közötti várakozási idő a rendszerben lévő erőforrások rendelkezésre állásától függ.

## <a name="choosing-between-different-reserved-unit-types"></a>Választás a különböző fenntartott egységtípusok között

Az alábbi táblázat segítségével döntést hozad, amikor a különböző kódolási sebességek között választ. Azt is előírja, néhány benchmark esetben [egy videót, hogy lehet letölteni,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) hogy végezze el a saját teszteket:

|RU-típus|Forgatókönyv|Példa eredmények a [7 min 1080p videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Egyátviteli kódolás. <br/>Fájlok SD vagy az alábbi felbontások, nem időérzékeny, alacsony költségű.|Kódolás egy bitráta Mp4 felbontású MP4 fájl segítségével "H264 Single Bitrate SD 16x9" körülbelül 7 percet vesz igénybe.|
| **S2**|Egy-és többszörös bitráta kódolás.<br/>Normál használat mind Az SD és a HD kódoláshoz.|A "H264 Single Bitrate 720p" készlettel való kódolás körülbelül 6 percet vesz igénybe.<br/><br/>A "H264 Multiple Bitrate 720p" készlettel való kódolás körülbelül 12 percet vesz igénybe.|
| **S3**|Egy-és többszörös bitráta kódolás.<br/>Full HD és 4K felbontású videók. Időérzékeny, gyorsabb átfutási idő kódolás.|A "H264 Single Bitrate 1080p" készlettel való kódolás körülbelül 3 percet vesz igénybe.<br/><br/>A "H264 Multiple Bitrate 1080p" készlettel rendelkező kódolás körülbelül 8 percet vesz igénybe.|

## <a name="considerations"></a>Megfontolandó szempontok

* A Media Services v3 vagy Video Indexer által indított hangelemzési és videoelemzési feladatok esetében az S3 egységtípusa erősen ajánlott.
* Ha a megosztott készlet, azaz fenntartott egységek nélkül, majd a kódolási feladatok ugyanolyan teljesítményt, mint az S1 fenntartott egységek. Azonban nincs felső határa annak az időnek, amelyet a feladatok várólistás állapotban tölthetnek, és egy adott időpontban legfeljebb egy feladat fog futni.

A cikk további részében bemutatja, hogyan használhatja a [Media Services v3 CLI-t](https://aka.ms/ams-v3-cli-ref) az MRUs-ok méretezéséhez.

> [!NOTE]
> A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha 10-nél több S3 MRUs-ra van szüksége, nyisson meg egy támogatási jegyet az [Azure Portalon.](https://portal.azure.com/)

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Media számára fenntartott egységek méretezése CLI-vel

Futtassa a következő parancsot: `mru`.

A következő [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) parancs beállítja a Media Reserved Units-t az "amsaccount" fiókban a **count** és **type** paraméterek használatával.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Számlázás

A média számára fenntartott egységek fiókban való kiépítésének percszáma alapján számítunk fel díjat. Ez attól függetlenül fordul elő, hogy vannak-e feladatok a fiókjában. Részletes magyarázatot a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) lapjának GYIK szakaszában talál.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

* [Kvóták és korlátozások](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
