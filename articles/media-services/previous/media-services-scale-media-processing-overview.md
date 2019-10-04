---
title: Az adathordozó-feldolgozás skálázása – áttekintés | Microsoft Docs
description: Ez a témakör áttekintést nyújt az adathordozók feldolgozásának Azure Media Servicessal való méretezéséről.
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
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102914"
---
# <a name="scaling-media-processing-overview"></a>A Media Processing méretezésének áttekintése 
Ezen az oldalon áttekintheti, hogyan és miért érdemes méretezni a médiafájlok feldolgozását. 

## <a name="overview"></a>Áttekintés
A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egységek típusai közül választhat: **S1**, **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. További információkért lásd a fenntartott [egységek típusát](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

A fenntartott egység típusának meghatározása mellett megadhatja, hogy a fiók a fenntartott egységekkel legyen kiépítve. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiókja öt fenntartott egységgel rendelkezik, akkor az öt adathordozó-feladat egyszerre fut, amíg a feladatok feldolgozására sor kerül. A hátralévő feladatok megvárhatják a várólistán, és a folyamatban lévő tevékenységek befejezését követően is felveszik a feldolgozást. Ha egy fiókhoz nincs kiépítve fenntartott egység, a rendszer szekvenciálisan felveszi a tevékenységeket. Ebben az esetben a várakozási idő egy feladat befejezése és a következő kezdés a rendszer erőforrásainak rendelkezésre állása alapján fog függeni.

## <a name="choosing-between-different-reserved-unit-types"></a>Különböző fenntartott egységek típusának kiválasztása
A következő táblázat segít dönteni a különböző kódolási sebességek kiválasztásakor. Emellett néhány teljesítményteszt-esetet is biztosít [egy videón, amelyet](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) a saját tesztek elvégzéséhez tölthet le:

|RU-típus|Forgatókönyv|Példa a [7 perces 1080p videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) eredményeire|
|---|---|---|
| **S1**|Egyetlen bitráta kódolása. <br/>A fájlok SD-vagy alacsonyabb felbontásban, nem érzékenyek, alacsonyak.|A "H264 Single bitráta SD 16x9" használatával az egyetlen sávszélességű SD-feloldási MP4-fájl kódolása körülbelül 7 percet vesz igénybe.|
| **S2**|Egyetlen bitráta és több bitráta-kódolás.<br/>Normál használat SD és HD kódoláshoz.|A "H264 Single bitráta 720p" beállítású kódolás körülbelül 6 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 720p" beállítású kódolás körülbelül 12 percet vesz igénybe.|
| **S3**|Egyetlen bitráta és több bitráta-kódolás.<br/>Teljes HD-és 4K-felbontású videók. Időérzékeny, gyorsabb átfutási idő kódolása.|A "H264 Single bitráta 1080p" beállítással rendelkező kódolás körülbelül 3 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 1080p" beállítással rendelkező kódolás körülbelül 8 percet vesz igénybe.|

## <a name="considerations"></a>Megfontolandó szempontok
> [!IMPORTANT]
> Tekintse át az ebben a szakaszban ismertetett szempontokat.  
> 
> 

* Az Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatokhoz az S3 egység típusa kifejezetten ajánlott.
* Ha a megosztott készletet használja, azaz a fenntartott egységek nélkül, akkor a kódolási feladatok ugyanolyan teljesítménnyel rendelkeznek, mint az S1 RUs. Azonban nincs felső korlát ahhoz az időponthoz, ameddig a tevékenységek várólistán lévő állapotba kerülnek, és egy adott időpontban egyetlen feladat is fut.

## <a name="billing"></a>Számlázás

A számlázás a fiókban a Media szolgáltatás számára fenntartott egységek száma alapján történik. Ez attól függetlenül történik, hogy van-e a fiókjában futó feladat. – Gyakori kérdések című szakaszában talál részletes magyarázatát, a [Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/) lapot.   

## <a name="quotas-and-limitations"></a>Kvóták és korlátozások
A kvótákkal és korlátozásokkal, valamint a támogatási jegyek megnyitásával kapcsolatos információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Következő lépés
A skálázási adathordozó feldolgozási feladatának elérése az alábbi technológiák egyikével: 

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

