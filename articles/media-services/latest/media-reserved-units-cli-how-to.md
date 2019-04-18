---
title: Skálázhatja a Media szolgáltatás számára fenntartott egység – Azure CLI használatával |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan méretezhető médiafeldolgozás a Media Services az Azure CLI használatával.
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
ms.date: 03/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0dcfa4e7cd792f61d1620a57330f87c5c86e6c9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58915691"
---
# <a name="scaling-media-processing"></a>Skálázás médiafeldolgozás

Az Azure Media Services a fenntartott médiaegységek (MRU-k) kezelésével lehetővé teszi a médiafeldolgozás skálázását a fiókjában. Helyet határozza meg, hogy a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2**, vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. 

A szolgáltatás számára fenntartott egység típusának meghatározása, mellett megadhatja fiókja ellátása szolgáltatás számára fenntartott egységek. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Például ha a fiókja rendelkezik öt fenntartott egységet, majd öt feldolgozható médiafeladatok fog futni, amíg egy időben, amennyi a feldolgozandó feladatok. A hátralévő feladatok a várólistában várakozik, és feldolgozására egymás után futtatott feladat végeztével lesznek első mértékének. Ha a fiók nem rendelkezik minden üzembe helyezett szolgáltatás számára fenntartott egységeket, majd feladatok fog felvenni, egymás után. Ebben az esetben egy feladat befejeződik, és a következő egy kezdő között a várakozási idő függ az erőforrások rendelkezésre állása a rendszerben.

## <a name="choosing-between-different-reserved-unit-types"></a>Más szolgáltatás számára fenntartott egység típusok közötti választáshoz

Az alábbi táblázat segít a különböző kódolási sebességű közötti kiválasztásakor döntéseket. Teljesítményteszt néhány esetben is kínál, a [letöltheti a videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) saját teszteket végrehajtásához:

|RU típusának|Forgatókönyv|A példa eredménye az [7 perc 1080 p videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Egyszeres átviteli sebességű kódolás. <br/>SD vagy az alatti megoldások, a fájlok nem idő-és nagybetűket, alacsony költségű.|Az egyszeres sávszélességű SD feloldási MP4-fájlt "H264 egyetlen sávszélességű SD 16 x 9" kódolás 10 percet vesz igénybe.|
| **S2**|Egyféle sávszélességű, és több átviteli sebességű kódolás.<br/>Normál használati SD és a HD Encoding.|Idő szükséges kódolásának a "H264 egyféle sávszélességű 720p" előbeállítást körülbelül 8 perc.<br/><br/>Kódolás az "H264 Multiple Bitrate 720p" beállításkészletet körülbelül 16.8 percig tart.|
| **S3**|Egyféle sávszélességű, és több átviteli sebességű kódolás.<br/>Teljes HD és 4K felbontása videókat. Idő-és nagybetűket, a gyorsabb ügyintézés kódolást.|Idő szükséges kódolásának a "H264 egyetlen Bitrate 1080p" előbeállítást megközelítőleg 4 perc.<br/><br/>Kódolás a "H264 Multiple Bitrate 1080p" előbeállítás körülbelül 8 percet vesz igénybe.|

## <a name="considerations"></a>Megfontolandó szempontok

* A hangelemzés Videóelemzés feladatok és a Media Services v3-as vagy a Video Indexer által aktivált, az S3-egység típusa erősen ajánlott.
* Megosztott tárolókészletet használja, azt jelenti, anélkül, hogy bármilyen szolgáltatás számára fenntartott egységeket, majd encode feladatait van-e ugyanazokat a méretezhetőségi, az S1 szintű fenntartott egységek. Azonban nincs felső korlátja, az idő a tevékenységek várólistára Bajos dolgaival, és a egy adott időpontban legfeljebb csak egy tevékenység fog futni.

A cikk bemutatja, hogyan [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) méretezése a helyet.

> [!NOTE]
> A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).
>
> Az Azure portal jelenleg nem használható más v3-erőforrások kezeléséhez. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](developers-guide.md).

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Méretezési csoport fenntartott Médiaegységek CLI-vel

Futtassa a következő parancsot: `mru`.

A következő [az ams-fiók legutóbbi](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) parancsot a Media szolgáltatás számára fenntartott egységek a "amsaccount" fiók használatával beállítja a **száma** és **típus** paraméterek.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Számlázás

A számlázás a alapján a Media szolgáltatás számára fenntartott egységek felhasznált percek számát a fiókjában. Ez akkor fordulhat elő, hogy e független bármely a fiókban futó feladatok. – Gyakori kérdések című szakaszában talál részletes magyarázatát, a [Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/) lapot.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

* [Kvóták és korlátozások](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
