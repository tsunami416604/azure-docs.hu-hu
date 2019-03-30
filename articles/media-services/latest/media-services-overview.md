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
ms.openlocfilehash: 19e94fc65ddc1719c601397adfe77f8f9445e4fa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662125"
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

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

Azure Resource Manager elnevezésével kapcsolatos további információkért lásd: [Elnevezési követelményeknek](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>V3 API-tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. A v3 API-k nem adnak vissza titkos kulcsokat vagy hitelesítő adatokat a **lekérési** vagy **listázási** művelet során. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. Egy különálló műveleti metódust kell meghívnia a titkos kulcsok vagy hitelesítő adatok lekéréséhez. A különálló műveletekkel különböző RBAC biztonsági engedélyeket állíthat be, ha esetleg valamely API mégis lekér/megjelenít titkos kulcsokat, míg más API-k nem. A hozzáférés RBAC használatával való kezeléséről további információt a [hozzáférés RBAC használatával való kezeléséről](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) szóló szakaszban talál.

Erre a következők szolgálnak példaként: 

* a rendszer nem ad vissza ContentKey értékeket a StreamingLocator lekérésében, 
* a rendszer nem ad vissza korlátozó kulcsokat a ContentKeyPolicy lekérésében, 
* a rendszer nem adja vissza az URL lekérdezési sztring részét (az aláírás eltávolításához) a feladat HTTP bemeneti URL-jei esetében.

Tekintse meg a [beolvasni a tartalom a fő házirend - .NET](get-content-key-policy-dotnet-howto.md) példa.


## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát? 

Ismerje meg, hogyan kódolása és csomagolása, streamelhet videókat igény szerinti, élő közvetítés, elemezheti videóit a Media Services v3. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

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
* [Az egyéni átalakítási kódolása a parancssori felület használatával](custom-preset-cli-howto.md)

## <a name="next-steps"></a>További lépések

Hogyan kezdhetem meg a v3 használatát? 

> [!div class="nextstepaction"]
> [Alapvető fogalmak ismertetése](concepts-overview.md)<br/>
> [Fejlesztés SDK-k használatával, a Media Services v3 API-val](developers-guide.md) 

