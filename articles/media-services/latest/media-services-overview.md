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
ms.date: 03/29/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c65c797612e179a9f3c02696d827131f5c830b3c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755908"
---
# <a name="what-is-azure-media-services-v3"></a>Az Azure Media Services v3 ismertetése

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Függetlenül attól, hogy Ön alkalmazásfejlesztő, vagy egy ügyfélszolgálat, kormányzati intézmény vagy szórakoztató vállalat munkatársa, a Media Services olyan alkalmazások létrehozásához nyújt segítséget, amelyek kiváló minőségű multimédiás élményt nyújtanak nagy célközönség számára napjaink legnépszerűbb mobileszközein és böngészőiben. 

> [!NOTE]
> Az Azure portal jelenleg nem használható v3-erőforrások kezeléséhez. Használja a [REST API-val](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), vagy a támogatott valamelyik [SDK-k](developers-guide.md).

## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

A Media Services lehetővé teszi különböző média-munkafolyamatok létrehozását a felhőben. Az alábbiakban néhány példa látható arra, hogy mit tehet a Media Services használatával.  

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A különböző ügyfeleknek (mobileszközökre, tévékészülékekre, számítógépekre stb.) biztosított igény szerinti és élő streamek esetén megfelelően kell kódolni és csomagolni a video- és hangtartalmakat. Továbbítására és adatfolyam-e a tartalom megtekintéséhez lásd: [a rövid útmutató: Kódolása és streamelése fájlok](stream-files-dotnet-quickstart.md).
* Élő sportesemények, például focimeccsek, baseballmeccsek, felsőoktatási és középiskolai sportesemények és egyebek streamelése nagy online közönség számára. 
* Nyilvános értekezletek és események, például polgármesteri, képviselőtestületi és törvényhozói testületi ülések közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Oktatási célú e-tanulási videoplatform megvalósítása az Azure Media Services és az [Azure Cognitive Services API-jai](https://docs.microsoft.com/azure/#pivot=products&panel=ai) segítségével a beszéd szövegként történő feliratozása, a több nyelvre való fordítás stb. érdekében. 
* Használja az Azure Media Services együtt [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/#pivot=products&panel=ai) a feliratok és akadálymentes feliratok hozzáadása videókhoz, egy szélesebb közönség számára (például fogyatékkal élők hallási vagy eltérő mentén olvasni kívánt személyek méretformátumok figyelembe vétele nyelv).
* Engedélyezi az Azure CDN nagy méretű méretezési lehetőségek érhetők el az azonnali nagy terhelés (például az események kezdetét) a hatékonyabb hibakezelést eléréséhez. 

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát? 

Ismerje meg, hogyan kódolása és csomagolása, streamelhet videókat igény szerinti, élő közvetítés, elemezheti videóit a Media Services v3. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

Fejlesztés megkezdése előtt tekintse át a [alapvető fogalmai](concepts-overview.md)<br/>

### <a name="quickstarts"></a>Gyors útmutatók  

A rövid útmutatók alapvető nap-1 utasítások megjelenítése az új ügyfelek gyorsan kipróbálhatja a Media Services.

* [Stream-videó fájlok – .NET](stream-files-dotnet-quickstart.md)
* [Stream-videó fájlok – CLI](stream-files-cli-quickstart.md)
* [Stream-videó fájlok – Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Oktatóanyagok 

Az oktatóanyagok bemutatják a forgatókönyv-alapú eljárások a Media Services leggyakoribb feladatokat.

* [Kódolás a távoli fájl- és video-adatfolyamok – REST](stream-files-tutorial-with-rest.md)
* [A feltöltött fájl- és video - adatfolyamot .NET kódolása](stream-files-tutorial-with-api.md)
* [Stream élő – .NET](stream-live-tutorial-with-api.md)
* [.NET - videó elemzése](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítást – .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Útmutatók

Cikkek tartalmaznak kódmintákkal, amelyek bemutatják, hogyan lehet egy adott feladat végrehajtásához. Ez a szakasz példákat talál, az alábbiakban ezek közül néhány:

* [Fiók létrehozása – CLI](create-account-cli-how-to.md)
* [Hozzáférés API-k – parancssori felület](access-api-cli-how-to.md)
* [A fejlesztés megkezdése az SDK-k](developers-guide.md)
* [Kódolás a HTTPS, a feladat bemeneti – .NET](job-input-from-http-how-to.md)  
* [A figyelő események – portál](monitor-events-portal-how-to.md)
* [A többplatformos DRM - .NET dinamikusan titkosítása](protect-with-drm.md) 
* [Az egyéni átalakítási – parancssori felület kódolása](custom-preset-cli-howto.md)

## <a name="next-steps"></a>További lépések

Hogyan kezdhetem meg a v3 használatát? 

> [!div class="nextstepaction"]
> [Alapvető fogalmak ismertetése](concepts-overview.md)<br/>
> [Fejlesztés SDK-k használatával, a Media Services v3 API-val](developers-guide.md) 

