---
title: Azure Media Services v3 – áttekintés
titleSuffix: Azure Media Services
description: A Azure Media Services v3 magas szintű áttekintése, amely rövid útmutatókra, oktatóanyagokra és kódokra mutató hivatkozásokat tartalmaz.
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
ms.openlocfilehash: 61ffd4857ca9a330a4cb0eeace89791fc0973f70
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575093"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 – áttekintés

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Akár egy app Developer, egy Call Center, egy kormányzati szerv vagy egy szórakoztató cég, Media Services segítségével olyan alkalmazásokat hozhat létre, amelyek kiváló minőségű multimédiás élményt biztosítanak a nagy közönségnek a mai legnépszerűbb mobileszközök és böngészők számára.

A Media Services v3 SDK [-k Media Services v3 OpenAPI-specifikáción (henceg)](https://aka.ms/ams-v3-rest-sdk)alapulnak.

> [!NOTE]
> Jelenleg nem használhatja a Azure Portalt a v3-erőforrások kezeléséhez. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

Media Services a felhőben különböző adathordozó-munkafolyamatokat hozhat létre. Néhány példa arra, hogy mit tehet a Media Services a következők:

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A videó-és hangtartalmat a különböző ügyfelek (mobileszközök, televízió, számítógépek stb.) esetében igény szerinti és élő közvetítéssel is el kell kódolni, és megfelelően be kell csomagolni. Az ilyen tartalmak továbbításával és streamelésével kapcsolatban tekintse meg a [fájlok kódolásával és streamelésével foglalkozó rövid útmutatót](stream-files-dotnet-quickstart.md).
* Élő sporteseményeket közvetít egy nagy online közönség, például a futball, a baseball, a Kollégium és a középiskolás sportok terén.
* Nyilvános találkozók és események, például a városháza, a városi tanács ülései és a törvényhozó szervek közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Oktatási e-learning platformot hozhat létre a Azure Media Services és az [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/#pivot=products&panel=ai) a beszéd-szöveges feliratozáshoz, a több nyelvre való fordításhoz és így tovább.
* A Azure Media Services és az [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/#pivot=products&panel=ai) együttes használatával feliratokat és feliratokat adhat hozzá a videókhoz, hogy a szélesebb közönség számára is elérhető legyen (például a fogyatékkal élők vagy más nyelveken elolvasni kívánt személyek).
* Lehetővé teszi, hogy a Azure CDN nagyobb skálázást érjenek el a pillanatnyi magas terhelések jobb kezelése érdekében (például egy termék indítási eseményének megkezdése).

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát? 

Megtudhatja, hogyan kódolhat és csomagolhat tartalmakat, közvetítheti a videókat igény szerint, hogyan közvetítheti élőben, és hogyan elemezheti videóit a Media Services v3 használatával. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át a következőt:<br/>* [alapvető fogalmakat](concepts-overview.md) (incudes a fontos fogalmakat, például a csomagolást, a kódolást és a védelmet)<br/>* [fejlesztés Media Services V3 API](media-services-apis-overview.md) -kkal (az API-k elérésére, elnevezési konvenciók stb.)

### <a name="quickstarts"></a>Gyors útmutatók  

A gyors üzembe helyezési útmutatóban az új ügyfelek gyorsan kipróbálhatják a Media Services.

* [Videofájlok streamje – .NET](stream-files-dotnet-quickstart.md)
* [Videofájlok továbbítása – parancssori felület](stream-files-cli-quickstart.md)
* [Stream video Files – Node. js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>oktatóanyagokat

Az oktatóanyagok a leggyakoribb Media Services feladatokhoz tartozó forgatókönyv-alapú eljárásokat mutatják be.

* [Távoli fájl és stream-videó kódolása – REST](stream-files-tutorial-with-rest.md)
* [A feltöltött fájl és a Stream video-.NET kódolása](stream-files-tutorial-with-api.md)
* [Stream Live – .NET](stream-live-tutorial-with-api.md)
* [A videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)

### <a name="samples"></a>Példák

Használja [ezt a minta böngészőt](https://docs.microsoft.com/samples/browse/?products=azure-media-services) Azure Media Services kódok tallózásához.

### <a name="how-to-guides"></a>Útmutatók

A útmutatók olyan mintakód-mintákat tartalmaznak, amelyek bemutatják a feladatok végrehajtásának módját. Ebben a szakaszban számos példát talál. Íme néhány közülük:

* [Fiók létrehozása – parancssori felület](create-account-cli-how-to.md)
* [API-k elérése – parancssori felület](access-api-cli-how-to.md)
* [Kódolás HTTPS-sel, a feladathoz megadott bemenetként – .NET](job-input-from-http-how-to.md)  
* [Események figyelése – portál](monitor-events-portal-how-to.md)
* [Dinamikus titkosítás a multi-DRM-.NET használatával](protect-with-drm.md) 
* [Kódolás egyéni átalakítással – parancssori felület](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

[Tudnivalók az alapvető fogalmakról](concepts-overview.md)

