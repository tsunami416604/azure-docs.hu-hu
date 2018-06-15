---
title: Skálázás Media feldolgozása áttekintése |} Microsoft Docs
description: Ez a témakör a méretezési Media feldolgozásának az Azure Media Services nyújt áttekintést.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: c80bddfe1896b0b99319ef007c25718b5a754005
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29803569"
---
# <a name="scaling-media-processing-overview"></a>Méretezési Media feldolgozása – áttekintés
Ezen a lapon lehetőséget nyújt az áttekintése és media feldolgozási méretezési ennek okát. 

## <a name="overview"></a>Áttekintés
A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő Fenntartott egység típusok közül választhat: **S1**, **S2** vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. További információkért lásd: a [fenntartott egység típusok](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Mellett fenntartott egységnek típusának megadásával adhatja meg a fiók ellátása fenntartott egységek. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Például ha a fiók nem rendelkezik öt fenntartott egységek, majd öt media feladatokat futtatni egyidejűleg hosszú, amennyi a feldolgozandó feladatok. A hátralévő műveletekkel a várólistában, és egymás után feldolgozásához, amikor egy futó feladat befejezése után fog beolvasása felvételre. Ha a fiók nem rendelkezik bármely fenntartott egységek kiosztása, majd feladatok fog felvételre egymás után. Ebben az esetben egy feladat befejeződik, és ezután egy kezdő között a várakozási idő függ a rendelkezésre álló erőforrások a rendszerben.

## <a name="choosing-between-different-reserved-unit-types"></a>Választás a különböző fenntartott egység
Az alábbi táblázat segítséget nyújt a különböző kódolási sebességű közötti kiválasztásakor a döntést. Emellett néhány teljesítményteszt esetekben biztosít, és SAS URL-címek, amelyek segítségével töltse le a videók, amelyen a saját teszteket hajthat végre itt:

| Forgatókönyvek | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Tervezett használati eset |Egyszeres sávszélességű kódolását. <br/>Fájlok SD vagy az alatti megoldások, nem idő-és nagybetűket, az alacsony költségű. |Egyszeres sávszélességű, és több sávszélességű kódolását.<br/>Normál használati SD és a HD kódolására. |Egyszeres sávszélességű, és több sávszélességű kódolását.<br/>Teljes HD és 4K feloldási videók. Idő-és nagybetűket, gyorsabb esetenként kódolást. |
| Benchmark |[A bemeneti fájl: 5 perc hosszú 640x360p: 29,97 keretek/másodperc](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Egy egyszeres sávszélességű MP4-fájlokat, az ugyanaz felbontásban kódolást körülbelül 11 percet vesz igénybe. |[A bemeneti fájl: 5 perc hosszú 1280x720p: 29,97 keretek/másodperc](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>"H264 egyféle sávszélességű 720p" kódolás előre beállított vesz körülbelül 5 percet.<br/><br/>Kódolás "H264 Multiple Bitrate 720p" beállításkészletet körülbelül 11,5 percet vesz igénybe. |[A bemeneti fájl: 5 perc hosszú 1920x1080p: 29,97 keretek/másodperc](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>"H264 egyetlen Bitrate 1080p" kódolás előre beállított vesz körülbelül 2.7 percet.<br/><br/>Kódolás "H264 Multiple Bitrate 1080p" beállításkészletet körülbelül 5.7 percet vesz igénybe. |

## <a name="considerations"></a>Megfontolandó szempontok
> [!IMPORTANT]
> Tekintse át a jelen szakaszban ismertetett szempontok.  
> 
> 

* Fenntartott egységek működnek az összes adathordozó feldolgozási, beleértve az Azure Media Indexer használó feladatok indexelő parallelizing.  De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.
* Ha megosztott készletét használja, ez azt jelenti, hogy nélkül bármely fenntartott egységek, majd a encode feladat rendelkezik ugyanaz a teljesítmény, mint a S1 RUs. Azonban nem felső határa az idő, a feladatok képes költeni várakozó állapotban van, és egy adott időpontban legfeljebb csak egy feladat fog futni.

## <a name="billing"></a>Számlázás

A számlázás a Media szolgáltatás számára fenntartott egységek tényleges percalapú használatán alapul. Részletes leírását, tekintse meg a feltett a [Media Services díjszabása](https://azure.microsoft.com/pricing/details/media-services/) lap.   

## <a name="quotas-and-limitations"></a>Kvóták és korlátozások
További információ a kvóták és korlátozások és a támogatási jegy megnyitása: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Következő lépés
Ezek a technológiák egyike méretezési media feldolgozási feladata elérése: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portál](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> A Java SDK legújabb verziójának beszerzéséhez és a Java-fejlesztés megkezdéséhez tekintse meg [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) című cikket. <br/>
> A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft Azure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

