---
title: "A Media Services REST API áttekintése |} Microsoft Docs"
description: "Media Services REST API – áttekintés"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: eada8f2bcd2488d5ed36b0c24aa3d1b917815517
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="media-services-operations-rest-api-overview"></a>A Media Services REST API áttekintése
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

A **Media Services Operations REST** API feladatok, eszközök, hozzáférési házirendek és más műveletek olyan objektumokon, a Media Services-fiók létrehozására használt. További információkért lásd: [Media Services Operations REST API-referenciában](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Microsoft Azure Media Services egy olyan szolgáltatás, amely támogatja az OData-alapú HTTP-kérelmekre, és képes válaszolni részletes JSON vagy atom + pub. Media Services megfelel-e az Azure tervezési útmutató, mivel nincs a szükséges minden egyes ügyfélnek kell használni, amikor csatlakozik a Media Services HTTP-fejléc készlettel, valamint a nem kötelező használható fejléc készlettel. A következő szakaszok ismertetik a fejlécek és használhatja, ha HTTP-műveletek létrehozása a kérelmeket, és érkezik válasz, a Media Services.

Ez a témakör áttekintést nyújt a Media Services REST v2 használatára.

## <a name="considerations"></a>Megfontolandó szempontok

A következők érvényesek REST használatakor.

* Entitások lekérdezésekor korlátozás van adja vissza egy időben, mert a nyilvános REST v2 korlátozza a lekérdezési eredmények 1000 eredmények 1000 entitások. Kell használnia **kihagyása** és **érvénybe** (.NET) / **felső** (REST) leírtak szerint [.NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ha JSON használatával és használandó megadásával a **__metadata** kulcsszó a kérelem (például, hogy objektumra hivatkozik, csatolt) meg kell adni a **elfogadás** fejlécének [részletes JSON formátumban](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (lásd az alábbi példában). OData nem értelmezi a **__metadata** tulajdonság a kérelem, kivéve, ha beállította azt részletes.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-kérelem fejlécek
Minden hívás elvégezte a Media Services a szükséges, meg kell adni a kérelem fejléc készlettel van, és is a választható fejléc készlettel érdemes lehet felvenni. A következő táblázat felsorolja a szükséges fejlécek:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Engedélyezés |Tulajdonosi |Tulajdonosi a csak elfogadott engedélyezési mechanizmus. Az érték az ACS által biztosított jogkivonat is magában kell foglalnia. |
| x-ms-version |Decimális |2.11 |
| DataServiceVersion |Decimális |3.0 |
| MaxDataServiceVersion |Decimális |3.0 |

> [!NOTE]
> Mivel a Media Services OData használja az alapul szolgáló eszköz metaadatok tárház REST API-k segítségével teszi közzé, szerepeljenek-e a DataServiceVersion és MaxDataServiceVersion fejlécek kérésének; azonban ha nem, majd jelenleg Media Services azt feltételezi, hogy a használati DataServiceVersion érték 3.0.
> 
> 

A következő olyan opcionális fejlécek:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Dátum |RFC 1123 dátuma |A kérés időbélyege |
| Fogadja el |Tartalom típusa |A kért content-type a választ, mint az alábbiak:<p> -az application/json; odata részletes =<p> -application/atom + xml<p> Válaszok lehet egy másik tartalomtípus egy blob adatlehívás, például ha a sikeres válasz tartalmazza a blob adatfolyam tartalmaként. |
| Fogadja el-kódolás |A gzip, deflate |A GZIP és a DEFLATE kódolását, ha alkalmazható. Megjegyzés: Nagy erőforrások Media Services előfordulhat, hogy figyelmen kívül hagyja ezt a fejlécet, és vissza noncompressed adatokat. |
| Fogadja el nyelv |"hu", "es", és így tovább. |Adja meg a kívánt nyelvet, a válasz. |
| Fogadja el Charset |Például a "UTF-8" Charset típusa |Alapértelmezés az UTF-8. |
| X-HTTP-módszer |HTTP-metódus |Lehetővé teszi az ügyfelek vagy például ezen módszerek keresztül GET hívást bújtatott PUT vagy DELETE HTTP-metódus nem támogató tűzfalak. |
| Tartalomtípus |Tartalom típusa |A kérelem törzsében PUT vagy POST kérelem tartalomtípus. |
| ügyfél-azonosító |Karakterlánc |A hívó által megadott érték, amely azonosítja az adott kérelem. Ha meg van adva, ennek az értéknek szerepelni fog a válaszüzenetben kérések módja. <p><p>**Fontos**<p>Értékeket kell felső határa 2096b (2-k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-válaszfejléceket
A következő olyan attól függően, hogy az volt a kért erőforrás és a művelet végrehajtására szolgál, a visszaadott fejlécek.

| Fejléc | Típus | Érték |
| --- | --- | --- |
| -azonosító |Karakterlánc |Az aktuális művelet, a szolgáltatás generált egyedi azonosítója. |
| ügyfél-azonosító |Karakterlánc |Ha az eredeti kérés a hívó által megadott azonosító. |
| Dátum |RFC 1123 dátuma |A kérelem feldolgozása dátuma. |
| Tartalomtípus |Változó |Az adott válasz törzsének tartalomtípusa. |
| Tartalom kódolása |Változó |A gzip és a deflate szükség szerint. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-műveletek
A következő egy HTTP-műveleteket tartalmazó is használható, ha így HTTP-kérelmek teljes listáját:

| Művelet | Leírás |
| --- | --- |
| GET |Az objektum aktuális értékét adja vissza. |
| POST |Egy objektum a megadott adatok alapján hoz létre, vagy elküldi a parancsot. |
| A PUT |A felváltja egy objektumot, vagy létrehoz egy elnevezett objektum (ha alkalmazható). |
| TÖRLÉSE |Törli az objektumot. |
| EGYESÍTÉS |Frissíti a meglévő objektum elnevezett tulajdonság módosításokkal. |
| HEAD |Az objektum GET választ metaadatokat adja vissza. |

## <a name="discover-media-services-model"></a>Media Services-modell felderítése
Ahhoz, hogy a Media Services entitások felfedezését, a $metadata művelet használható. Lehetővé teszi az összes érvényes entitástípusok, entitás tulajdonságai, társítások, Funkciók, műveletek, és így tovább. A következő példa bemutatja, hogyan hozható létre URI: https://media.windows.net/API/$ metaadatok.

Kell hozzáfűzése "? api-version=2.x", ha egy böngészőben a metaadatok megtekintéséhez, vagy ne adja meg az x-ms-version fejlécnek a kérés URI végén.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). Sikeresen csatlakoztassa a https://media.windows.net, adja meg egy másik Media Services URI 301 átirányítást fog kapni. Meg kell nyitnia az új URI későbbi hívásokat.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a többi AMS API-k elérésére [hitelesítés használata az Azure AD az Azure Media Services API REST eléréséhez](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

