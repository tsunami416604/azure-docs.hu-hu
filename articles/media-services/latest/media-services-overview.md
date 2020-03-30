---
title: Az Azure Media Services v3 áttekintése
titleSuffix: Azure Media Services
description: Az Azure Media Services v3 magas szintű áttekintése rövid útmutatókra, oktatóanyagokra és kódmintákra mutató hivatkozásokkal.
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: bd3890757377525cf9c178866a2a2fbc0791b9de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79461010"
---
# <a name="azure-media-services-v3-overview"></a>Az Azure Media Services v3 áttekintése

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Akár alkalmazásfejlesztő, akár call center, kormányzati ügynökség vagy szórakoztató vállalat, a Media Services segítségével olyan alkalmazásokat hozhat létre, amelyek kiemelkedő minőségű médiaélményeket biztosítanak a nagy közönségnek napjaink legnépszerűbb mobileszközein és böngészőiben.

A Media Services v3 SDK-k a [Media Services v3 OpenAPI Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk)szolgáltatáson alapulnak.

> [!NOTE]
> Jelenleg az Azure [Portal](https://portal.azure.com/) segítségével: kezelheti a Media Services v3 [Élő események](live-events-outputs-concept.md), megtekintése (nem kezeli) v3 [Assets](assets-concept.md), [az API-k elérésével kapcsolatos információk.](access-api-portal.md) Minden más felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos emlékeztetőként be kell tartania az Azure Media Services használatával kapcsolatos összes vonatkozó jogszabályt, és nem használhatja a Media Servicest vagy bármely Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy amely másokra nézve káros lehet.

Mielőtt bármilyen videót/képet feltöltene a Media Servicesszolgáltatásba, önnek rendelkeznie kell a videó/kép használatához szükséges összes joggal, beleértve – amennyiben a törvény előírja – az egyének (ha vannak ilyen) összes szükséges hozzájárulását a videóban/képen, adataik media servicesben és Azure-ban történő felhasználásához, feldolgozásához és tárolásához. Egyes joghatóságok különleges jogi követelményeket írhatnak elő bizonyos adatkategóriák, például biometrikus adatok gyűjtésére, online feldolgozására és tárolására vonatkozóan. Mielőtt a Media Servicest és az Azure-t speciális jogi követelmények hatálya alá tartozó adatok feldolgozására és tárolására használná, gondoskodnia kell az Önre vonatkozó ilyen jogi követelmények betartásáról.

A Media Services megfelelőségével, adatvédelmével és biztonságával kapcsolatos további tudnivalókért keresse fel a Microsoft [Adatvédelmi központot.](https://www.microsoft.com/trust-center/?rtc=1) A Microsoft adatvédelmi kötelezettségeivel, adatkezelési és adatmegőrzési gyakorlatával kapcsolatban, beleértve az adatok törlésének módját is, olvassa el a Microsoft [adatvédelmi nyilatkozatát,](https://privacy.microsoft.com/PrivacyStatement)az [online szolgáltatási feltételeket](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") és [az adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). A Media Services használatával Ön elfogadja, hogy magára nézve kötelezőnek fogadja el az OST, a DPA és az Adatvédelmi nyilatkozat.
 
## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

A Media Services lehetővé teszi, hogy különböző média-munkafolyamatokat hozzon létre a felhőben. Néhány példa a Media Services szolgáltatással való teendőkre:

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A különböző ügyfelek (mobileszközök, tv, pc és így tovább) irányuló, igény szerinti és élő közvetítés esetén a video- és hangtartalmat megfelelően kell kódolni és csomagolni. Az ilyen tartalmak továbbításával és streamelésével kapcsolatban tekintse meg a [fájlok kódolásával és streamelésével foglalkozó rövid útmutatót](stream-files-dotnet-quickstart.md).
* Élő sportesemények közvetítése nagy online közönségnek, például foci, baseball, főiskolai és középiskolai sportok stb.
* Nyilvános találkozók és rendezvények közvetítése, például városháza, városi tanács gyűlések és törvényhozó testületek.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Az Azure Media Services és az [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/?pivot=products&panel=ai) segítségével oktató e-learning videoplatformot valósítson meg a beszédfelismerési szövegfeliratozáshoz, a többnyelvű fordításhoz és így tovább.
* Az Azure Media Services és [az Azure Cognitive Services API-k](https://docs.microsoft.com/azure/?pivot=products&panel=ai) használatával feliratokat és feliratokat adhat a videókhoz, hogy szélesebb közönséget (például hallássérülteket vagy más nyelven olvasni szeretne).
* Engedélyezze az Azure CDN-t a nagy skálázás eléréséhez a pillanatnyi nagy terhelések jobb kezeléséhez (például egy termékindítási esemény elindításához).

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát? 

Ismerje meg, hogyan kódolhatja és csomagolhatja a tartalmakat, streamelhet videókat igény szerint, hogyan közvetíthet élőben, és hogyan elemezheti videóit a Media Services v3-as számával. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át a következőket:<br/>* [Alapvető fogalmak](concepts-overview.md) (incudes fontos fogalmak, mint a csomagolás, kódolás, és védelme)<br/>* [Fejlesztés a Media Services 3-as vAPI-jával](media-services-apis-overview.md) (információkat tartalmaz az API-k eléréséről, elnevezési konvenciókról és így tovább)

### <a name="sdks"></a>SDK-k

Kezdje el a fejlesztést az [Azure Media Services v3-as ügyfélsdk-jein.](media-services-apis-overview.md#sdks)

### <a name="quickstarts"></a>Rövid útmutatók  

A rövid útmutatók alapvető 1.

* [Videófájlok streamelése – .NET](stream-files-dotnet-quickstart.md)
* [Videófájlok streamelése - CLI](stream-files-cli-quickstart.md)
* [Videófájlok streamelése – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Oktatóanyagok

Az oktatóanyagok forgatókönyv-alapú eljárásokat mutatnak be a Media Services néhány legfontosabb feladatához.

* [Távoli fájl kódolása és videó streamelése – REST](stream-files-tutorial-with-rest.md)
* [Feltöltött fájl kódolása és videó streamelése – .NET](stream-files-tutorial-with-api.md)
* [Élő stream – .NET](stream-live-tutorial-with-api.md)
* [Videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)

### <a name="samples"></a>Példák

[Ezzel a mintaböngészővel](https://docs.microsoft.com/samples/browse/?products=azure-media-services) böngészhet az Azure Media Services-kódminták között.

### <a name="how-to-guides"></a>Útmutatók

Útmutatóútmutatók tartalmaznak kódmintákat, amelyek bemutatják, hogyan kell elvégezni egy feladatot. Ebben a szakaszban számos példát talál. Íme néhány közülük:

* [Fiók létrehozása – parancssori felület](create-account-cli-how-to.md)
* [Adatelérési API-k – parancssori felület](access-api-cli-how-to.md)
* [Kódolás HTTPS-lel feladatbemenetként - .NET](job-input-from-http-how-to.md)  
* [Események monitorozása – Portál](monitor-events-portal-how-to.md)
* [Dinamikus titkosítás több DRM-mel - .NET](protect-with-drm.md) 
* [Hogyan kódolunk egyéni átalakítással - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

[Ismerje meg az alapvető fogalmakat](concepts-overview.md)
