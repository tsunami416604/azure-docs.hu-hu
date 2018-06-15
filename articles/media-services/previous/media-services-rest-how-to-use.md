---
title: A Media Services REST API áttekintése |} Microsoft Docs
description: Media Services REST API – áttekintés
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: af83e876802b176d4e097535d45df91e8a986dfb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790453"
---
# <a name="media-services-operations-rest-api-overview"></a>A Media Services REST API áttekintése
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

A **Media Services Operations REST** API feladatok, eszközök, élő csatornák és egyéb erőforrások a Media Services-fiók létrehozására használt. További információkért lásd: [Media Services Operations REST API-referenciában](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services a REST API-t, amely támogatja a JSON vagy a atom + XML-formátuma pub biztosít. Media Services REST API-t igényel minden egyes ügyfélnek kell küldenie a Media Services, valamint a nem kötelező fejléc készlettel történő csatlakozás során meghatározott HTTP-fejlécek. A következő szakaszok ismertetik a fejlécek és használhatja, ha HTTP-műveletek létrehozása a kérelmeket, és érkezik válasz, a Media Services.

A Media Serivces REST API-hitelesítés szemlélteti a cikk az Azure Active Directory-hitelesítésen keresztül történik [hitelesítés használata az Azure AD az Azure Media Services API REST eléréséhez](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Megfontolandó szempontok

A következők érvényesek REST használatakor.

* Entitások lekérdezésekor korlátozás van adja vissza egy időben, mert a nyilvános REST v2 korlátozza a lekérdezési eredmények 1000 eredmények 1000 entitások. Kell használnia **kihagyása** és **érvénybe** (.NET) / **felső** (REST) leírtak szerint [.NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ha JSON használatával és használandó megadásával a **__metadata** kulcsszó (például, hogy a hivatkozás csatolt objektum) a kérésben meg kell adni a **elfogadás** fejlécének [részletes JSON formátumban](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(lásd a következő példát). OData nem értelmezi a **__metadata** tulajdonság a kérelem, kivéve, ha beállította azt részletes.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-kérelem fejlécek
Minden hívás elvégezte a Media Services a szükséges, meg kell adni a kérelem fejléc készlettel van, és is a választható fejléc készlettel érdemes lehet felvenni. Az alábbi táblázat felsorolja a szükséges fejlécek:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Engedélyezés |Tulajdonosi |Tulajdonosi a csak elfogadott engedélyezési mechanizmus. Az értéknek tartalmaznia kell az Azure Active Directory által biztosított jogkivonat. |
| x-ms-version |Decimális |2.17 (vagy az alkalmazás legújabb verziójára)|
| DataServiceVersion |Decimális |3.0 |
| MaxDataServiceVersion |Decimális |3.0 |

> [!NOTE]
> Mivel a Media Services OData teszi közzé a REST API-kat használ, a DataServiceVersion és MaxDataServiceVersion fejlécek fel kell venni összes kérelem; azonban ha nem, majd jelenleg Media Services azt feltételezi, hogy a használati DataServiceVersion érték 3.0.
> 
> 

A következő olyan opcionális fejlécek:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Dátum |RFC 1123 dátuma |A kérés időbélyege |
| Elfogadás |Tartalomtípus |A kért content-type a választ, mint az alábbiak:<p> -az application/json; odata részletes =<p> -application/atom + xml<p> Válaszok lehet egy másik tartalomtípus egy blob adatlehívás, például ha a sikeres válasz tartalmazza a blob adatfolyam tartalmaként. |
| Fogadja el-kódolás |A gzip, deflate |A GZIP és a DEFLATE kódolását, ha alkalmazható. Megjegyzés: Nagy erőforrások Media Services előfordulhat, hogy figyelmen kívül hagyja ezt a fejlécet, és vissza noncompressed adatokat. |
| Fogadja el nyelv |"hu", "es", és így tovább. |Adja meg a kívánt nyelvet, a válasz. |
| Fogadja el Charset |Például a "UTF-8" Charset típusa |Alapértelmezés az UTF-8. |
| X-HTTP-módszer |HTTP-metódus |Lehetővé teszi az ügyfelek vagy például ezen módszerek keresztül GET hívást bújtatott PUT vagy DELETE HTTP-metódus nem támogató tűzfalak. |
| Content-Type |Tartalomtípus |A kérelem törzsében PUT vagy POST kérelem tartalomtípus. |
| ügyfél-azonosító |Karakterlánc |A hívó által megadott érték, amely azonosítja az adott kérelem. Ha meg van adva, ennek az értéknek szerepelni fog a válaszüzenetben kérések módja. <p><p>**Fontos**<p>Értékeket kell felső határa 2096b (2-k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-válaszfejléceket
A következő olyan attól függően, hogy az volt a kért erőforrás és a művelet végrehajtására szolgál, a visszaadott fejlécek.

| Fejléc | Típus | Érték |
| --- | --- | --- |
| -azonosító |Karakterlánc |Az aktuális művelet, a szolgáltatás generált egyedi azonosítója. |
| ügyfél-azonosító |Karakterlánc |Ha az eredeti kérés a hívó által megadott azonosító. |
| Dátum |RFC 1123 dátuma |A dátum/idő, amely a kérelem feldolgozása. |
| Content-Type |Változó |Az adott válasz törzsének tartalomtípusa. |
| Tartalom kódolása |Változó |A gzip és a deflate szükség szerint. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Media Services által támogatott szabványos HTTP-műveletek
A következő egy HTTP-műveleteket tartalmazó is használható, ha így HTTP-kérelmek teljes listáját:

| Művelet | Leírás |
| --- | --- |
| GET |Az objektum aktuális értékét adja vissza. |
| POST |Egy objektum a megadott adatok alapján hoz létre, vagy elküldi a parancsot. |
| A PUT |A felváltja egy objektumot, vagy létrehoz egy elnevezett objektum (ha alkalmazható). |
| DELETE |Törli az objektumot. |
| EGYESÍTÉS |Frissíti a meglévő objektum elnevezett tulajdonság módosításokkal. |
| HEAD |Az objektum GET választ metaadatokat adja vissza. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Fedezze fel, és keresse meg a Media Services entitásmodell
Ahhoz, hogy a Media Services entitások felfedezését, a $metadata művelet használható. Lehetővé teszi az összes érvényes entitástípusok, entitás tulajdonságai, társítások, Funkciók, műveletek, és így tovább. Adja hozzá a $metadata műveletet, a Media Services REST API-végpont végén, a keresőszolgáltatás végezheti el.

 /API/$ metaadatok.

Kell hozzáfűzése "? api-version=2.x", ha egy böngészőben a metaadatok megtekintéséhez, vagy ne adja meg az x-ms-version fejlécnek a kérés URI végén.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Media Services REST az Azure Active Directoryval végzett hitelesítéshez

A REST API-hitelesítés Azure Active Directory(AAD) segítségével történik.
További részletek a Media Services-fiók, az Azure portálról beszerzésére szükséges hitelesítés részletei: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md).

A cikk részleteket, amely kapcsolódik a REST API használatával az Azure AD hitelesítési kód írása a [hitelesítés használata az Azure AD az Azure Media Services API REST eléréséhez](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>További lépések
Az Azure AD-alapú hitelesítés használata a Media Services REST API-t a további tudnivalókért lásd: [hitelesítés használata az Azure AD az Azure Media Services API REST eléréséhez](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

