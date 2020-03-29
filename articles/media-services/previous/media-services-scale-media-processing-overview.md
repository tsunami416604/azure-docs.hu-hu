---
title: Médiafeldolgozás méretezése – áttekintés | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt a Media Processing azure Media Services használatával történő méretezéséről.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102914"
---
# <a name="scaling-media-processing-overview"></a>Médiafeldolgozás skálázása – áttekintés 
Ez az oldal áttekintést nyújt arról, hogyan és miért kell méretezni az adathordozó-feldolgozást. 

## <a name="overview"></a>Áttekintés
A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egységtípusok közül választhat: **S1,** **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. További információt a [Fenntartott egységtípusok című témakörben talál.](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)

A fenntartott egység típusának megadása mellett megadhatja, hogy a fiók fenntartott egységekkel is kiépítse.In additioning the reserved unit, you can specify to provision your account with reserved units. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiók öt fenntartott egységgel rendelkezik, akkor öt médiafeladat fog egyidejűleg futni, amíg vannak feldolgozandó feladatok. A fennmaradó feladatok a várólistában várnak, és a futó feladat befejezésekor egymás után kerülnek feldolgozásra. Ha egy fióknem rendelkezik kiépített fenntartott egységekkel, akkor a feladatok at egymás után veszi késve. Ebben az esetben az egyik tevékenység befejezése és a következő indítás közötti várakozási idő a rendszerben lévő erőforrások rendelkezésre állásától függ.

## <a name="choosing-between-different-reserved-unit-types"></a>Választás a különböző fenntartott egységtípusok között
Az alábbi táblázat segítségével döntést hozad, amikor a különböző kódolási sebességek között választ. Azt is előírja, néhány benchmark esetben [egy videót, hogy lehet letölteni,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) hogy végezze el a saját teszteket:

|RU-típus|Forgatókönyv|Példa eredmények a [7 min 1080p videó](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Egyátviteli kódolás. <br/>Fájlok SD vagy az alábbi felbontások, nem időérzékeny, alacsony költségű.|Kódolás egy bitráta Mp4 felbontású MP4 fájl segítségével "H264 Single Bitrate SD 16x9" körülbelül 7 percet vesz igénybe.|
| **S2**|Egy-és többszörös bitráta kódolás.<br/>Normál használat mind Az SD és a HD kódoláshoz.|A "H264 Single Bitrate 720p" készlettel való kódolás körülbelül 6 percet vesz igénybe.<br/><br/>A "H264 Multiple Bitrate 720p" készlettel való kódolás körülbelül 12 percet vesz igénybe.|
| **S3**|Egy-és többszörös bitráta kódolás.<br/>Full HD és 4K felbontású videók. Időérzékeny, gyorsabb átfutási idő kódolás.|A "H264 Single Bitrate 1080p" készlettel való kódolás körülbelül 3 percet vesz igénybe.<br/><br/>A "H264 Multiple Bitrate 1080p" készlettel rendelkező kódolás körülbelül 8 percet vesz igénybe.|

## <a name="considerations"></a>Megfontolandó szempontok
> [!IMPORTANT]
> Tekintse át az ebben a szakaszban ismertetett szempontokat.  
> 
> 

* A Media Services v3 vagy Video Indexer által indított hangelemzési és videoelemzési feladatok esetében az S3 egységtípusa erősen ajánlott.
* Ha a megosztott készlet, azaz fenntartott egységek nélkül, majd a kódolási feladatok ugyanolyan teljesítményt, mint az S1 fenntartott egységek. Azonban nincs felső határa annak az időnek, amelyet a feladatok várólistás állapotban tölthetnek, és egy adott időpontban legfeljebb egy feladat fog futni.

## <a name="billing"></a>Számlázás

A média számára fenntartott egységek fiókban való kiépítésének percszáma alapján számítunk fel díjat. Ez attól függetlenül fordul elő, hogy vannak-e feladatok a fiókjában. Részletes magyarázatot a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) lapjának GYIK szakaszában talál.   

## <a name="quotas-and-limitations"></a>Kvóták és korlátozások
A kvótákról és korlátozásokról, valamint a támogatási jegy megnyitásáról a [Kvóták és korlátozások](media-services-quotas-and-limitations.md)című témakörben talál további információt.

## <a name="next-step"></a>Következő lépés
Érje el a méretezési adathordozó-feldolgozási feladatot az alábbi technológiák egyikével: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portál](media-services-portal-scale-media-processing.md)
> * [Többi](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> A Java SDK legújabb verziójának beszerzéséhez és a Java-fejlesztés megkezdéséhez tekintse meg [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) című cikket. <br/>
> A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft Azure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

