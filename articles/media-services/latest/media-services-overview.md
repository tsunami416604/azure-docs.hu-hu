---
title: Az Azure Media Services v3 áttekintése | Microsoft Docs
description: Ez a cikk általános áttekintését nyújt a Media Services szolgáltatásról, és további információkat biztosító cikkekre mutató hivatkozásokat tartalmaz.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c6359cf2401ff198b0242243dbf6dfdf2e35ce47
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244026"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 – áttekintés

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Függetlenül attól, hogy Ön alkalmazásfejlesztő, vagy egy ügyfélszolgálat, kormányzati intézmény vagy szórakoztató vállalat munkatársa, a Media Services olyan alkalmazások létrehozásához nyújt segítséget, amelyek kiváló minőségű multimédiás élményt nyújtanak nagy célközönség számára napjaink legnépszerűbb mobileszközein és böngészőiben. 

A Media Services v3 SDK [-k Media Services v3 OpenAPI-specifikáción (henceg)](https://aka.ms/ams-v3-rest-sdk)alapulnak.

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

A Media Services lehetővé teszi különböző média-munkafolyamatok létrehozását a felhőben. Az alábbiakban néhány példa látható arra, hogy mit tehet a Media Services használatával.  

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A különböző ügyfeleknek (mobileszközökre, tévékészülékekre, számítógépekre stb.) biztosított igény szerinti és élő streamek esetén megfelelően kell kódolni és csomagolni a video- és hangtartalmakat. Az ilyen tartalmak továbbításával és streamelésével kapcsolatban tekintse meg a [fájlok kódolásával és streamelésével foglalkozó rövid útmutatót](stream-files-dotnet-quickstart.md).
* Élő sportesemények, például focimeccsek, baseballmeccsek, felsőoktatási és középiskolai sportesemények és egyebek streamelése nagy online közönség számára. 
* Nyilvános értekezletek és események, például polgármesteri, képviselőtestületi és törvényhozói testületi ülések közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Oktatási célú e-tanulási videoplatform megvalósítása az Azure Media Services és az [Azure Cognitive Services API-jai](https://docs.microsoft.com/azure/#pivot=products&panel=ai) segítségével a beszéd szövegként történő feliratozása, a több nyelvre való fordítás stb. érdekében. 
* A Azure Media Services és az [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/#pivot=products&panel=ai) együttes használatával feliratokat és feliratokat adhat hozzá a videókhoz, hogy a szélesebb közönség számára is elérhető legyen (például a fogyatékkal élők vagy más nyelveken elolvasni kívánt személyek).
* Lehetővé teszi, hogy a Azure CDN nagyobb skálázást érjenek el a pillanatnyi magas terhelések jobb kezelése érdekében (például egy termék indítási eseményének megkezdése). 

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát? 

Ismerje meg, hogyan kódolhat és csomagolhat be tartalmakat, streamelhet videókat igény szerint, közvetíthet élőben, illetve hogyan elemezheti videóit a Media Services 3-as verziójával. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át a következőt:<br/>@no__t – 0[alapfogalmak](concepts-overview.md) (incudes fontos fogalmak: csomagolás, kódolás, védelem stb.)<br/>@no__t – 0[Media Services V3 API](media-services-apis-overview.md) -kkal (az API-k elérésére, elnevezési konvenciók stb.) vonatkozó információkat tartalmaz.

### <a name="quickstarts"></a>Gyorsútmutatók  

A gyors üzembe helyezési útmutatóban az új ügyfelek gyorsan kipróbálhatják a Media Services.

* [Videofájlok streamje – .NET](stream-files-dotnet-quickstart.md)
* [Videofájlok továbbítása – parancssori felület](stream-files-cli-quickstart.md)
* [Stream video Files – Node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Oktatóanyagok 

Az oktatóanyagok a leggyakoribb Media Services feladatokhoz tartozó forgatókönyv-alapú eljárásokat mutatják be.

* [Távoli fájl és stream-videó kódolása – REST](stream-files-tutorial-with-rest.md)
* [A feltöltött fájl és a Stream video-.NET kódolása](stream-files-tutorial-with-api.md)
* [Stream Live – .NET](stream-live-tutorial-with-api.md)
* [A videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)
    
### <a name="samples"></a>Minták

Használja [ezt a minta böngészőt](https://docs.microsoft.com/samples/browse/?products=azure-media-services) Azure Media Services kódok tallózásához.

### <a name="how-to-guides"></a>Útmutatók

A cikkek olyan kódrészleteket tartalmaznak, amelyek bemutatják a feladatok végrehajtásának módját. Ebben a szakaszban több példát is talál, íme néhány példa:

* [Fiók létrehozása – parancssori felület](create-account-cli-how-to.md)
* [API-k elérése – parancssori felület](access-api-cli-how-to.md)
* [Kódolás HTTPS-sel, a feladathoz megadott bemenetként – .NET](job-input-from-http-how-to.md)  
* [Események figyelése – portál](monitor-events-portal-how-to.md)
* [Dinamikus titkosítás a multi-DRM-.NET használatával](protect-with-drm.md) 
* [Kódolás egyéni átalakítással – parancssori felület](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók az alapvető fogalmakról](concepts-overview.md)

