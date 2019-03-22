---
title: A Media Services REST API áttekintése |} A Microsoft Docs
description: Media Services REST API áttekintése
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;johndeu
ms.openlocfilehash: 549554521570d1d2f27b2da2b36ca1dfde25562f
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293614"
---
# <a name="media-services-operations-rest-api-overview"></a>A Media Services REST API áttekintése 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

A **Media Services Operations REST** API létrehozása a projektek, eszközök, élő csatornák és egyéb erőforrások a Media Services-fiók szolgál. További információkért lásd: [Media Services Operations REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services JSON vagy a atom + XML formátumú pub elfogadó REST API-t biztosít. A Media Services REST API-t adott HTTP-fejléceket, amely minden egyes ügyfélnek el kell küldenie a Media Services, valamint egy nem kötelező fejlécek készletét való csatlakozáskor szükséges. A következő szakaszok ismertetik a fejlécek és használhatja, amikor HTTP-műveletek kérések létrehozásához és a válaszfogadás a Media Services szolgáltatásból.

A Media Services REST API-hitelesítést az Azure Active Directory-hitelesítést, amelyet a cikk a fügvényekkel [eléréséhez az Azure Media Services API REST-tel az Azure AD-hitelesítés](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe REST használata esetén.

* Entitások lekérdezésekor korlátozva van az 1000 entitások adja vissza egy adott időpontban, mert a nyilvános REST v2 korlátozza az 1000 eredmények lekérdezési eredményeket. Kell használnia **kihagyása** és **igénybe** (.NET) / **felső** (REST) leírtak szerint [ebben a példában .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [ebben a REST API Példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ha JSON használatával, és adja meg a használandó a **__metadata** kulcsszó a kérelem (például, hogy a csatolt objektum-referencia) be kell a **elfogadás** fejlécet [a részletes JSON formátumban](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(lásd az alábbi példát). OData nem tudja értelmezni a **__metadata** tulajdonság a kérelemben, hacsak azt részletes.  
  
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

## <a name="standard-http-request-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-kérelem fejlécek
Minden hívás választja ki a Media Services szolgáltatásba szerepelnie kell a kérés szükséges fejlécek egy halmaza, és is választható fejlécek készletét érdemes felvenni. Az alábbi táblázat felsorolja a szükséges fejlécek:

| Fejléc | Typo | Érték |
| --- | --- | --- |
| Engedélyezés |Tulajdonosi |Tulajdonosi az egyetlen elfogadott engedélyezési mechanizmusa. Az értéket is tartalmaznia kell az Azure Active Directory által biztosított jogkivonat. |
| x-ms-version |Decimal |2.17 (vagy a legújabb verzió)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> A Media Services OData használatával teszi közzé a REST API-k, mert a dataserviceversion s és MaxDataServiceVersion fejlécek szerepelnie kell az összes kérelem; azonban ha nem, majd jelenleg a Media Services feltételezi, hogy a használati dataserviceversion s érték 3.0.
> 
> 

A következő olyan opcionális fejlécek:

| Fejléc | Typo | Érték |
| --- | --- | --- |
| Dátum |RFC 1123 dátuma |A kérelem időbélyege |
| Elfogadás |Tartalom típusa |A kért tartalom típusa a válaszhoz, például a következő:<p> -application/json;odata=verbose<p> - application/atom+xml<p> Válaszok különböző tartalom típusa, például egy blob fetch előfordulhat, ahol a sikeres válasz tartalmazza a blob adatfolyamot, a hasznos. |
| Accept-Encoding |Gzip, deflate |A GZIP- és a DEFLATE kódolást, ha alkalmazható. Megjegyzés: A nagyméretű erőforrásokra a Media Services figyelmen kívül hagyhatja ezt a fejlécet és noncompressed adatokat adja vissza. |
| Accept-Language |"en", "es" és így tovább. |Adja meg a kívánt nyelvet a válaszhoz. |
| Fogadja el karakterkészlet |Például a "UTF-8" karakterkészlet típusa |Alapértelmezett érték az UTF-8. |
| X-HTTP-Method |HTTP-metódus |Lehetővé teszi az ügyfelek vagy a tűzfalakat, amelyek nem támogatják a HTTP-metódusok például PUT vagy DELETE ezen módszerek bújtatott keresztül egy GET hívást kell. |
| Content-Type |Tartalom típusa |Tartalom típusa, a PUT vagy a POST kérelmeket a kérelem törzsében. |
| client-request-id |String |A hívó által megadott érték, amely azonosítja az adott kérelem. Ha meg van adva, ez az érték fog szerepelni a válaszüzenet arra, hogy a kérelem leképezése. <p><p>**Fontos**<p>Értékeket kell maximumon 2096b (2-k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-válaszfejléceket
A következő olyan készlete, az erőforrás is kér, és a végrehajtani kívánt művelet attól függően, hogy visszaadott fejlécek.

| Fejléc | Typo | Érték |
| --- | --- | --- |
| kérelem azonosítója |String |Az aktuális művelethez létrehozott szolgáltatás egyedi azonosítója. |
| client-request-id |String |Ha az eredeti kérés esetén a hívó által megadott azonosítója. |
| Dátum |RFC 1123 dátuma |A dátum/idő, amely a kérés lett feldolgozva. |
| Content-Type |Változó |A válasz törzse tartalomtípusa. |
| Content-Encoding |Változó |A gzip deflate vagy, ha szükséges. |

## <a name="standard-http-verbs-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-műveletek
A következő egy is használható, ha így a HTTP-kérelmek, HTTP-műveletek teljes listáját:

| művelet | Leírás |
| --- | --- |
| GET |Egy objektum aktuális értékét adja vissza. |
| POST |Létrehoz egy objektumot a megadott adatok alapján, vagy egy parancsot küldi el. |
| PUT |Az objektum váltja fel, vagy (ha alkalmazható) nevű objektumot hoz létre. |
| DELETE |Törli az objektumot. |
| MERGE |Frissíti egy meglévő objektum elnevezett tulajdonságok módosítását. |
| HEAD |A GET-válaszra objektum metaadatait adja vissza. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Fedezze fel, és keresse meg a Media Services-entitás modell
Ahhoz, hogy könnyebben felfedezhetővé teheti a Media Services entitásokat, a $metadata művelet használható. Ez lehetővé teszi, hogy lekérheti az összes érvényes entitástípusok, entitás tulajdonságai, társítások, függvények, műveletek, és így tovább. A teljes körű, a Media Services REST API-végpont a $metadata művelet hozzáadásával a keresőszolgáltatás érheti el.

 /api/$metadata.

Érdemes hozzáfűzése "? api-version=2.x", az URI Azonosítót, ha szeretné megtekinteni a metaadatokat egy böngészőben, vagy a kérelem nem tartalmazza az x-ms-version fejlécnek végén.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Hitelesítés a Media Services REST Azure Active Directory használatával

A REST API-hitelesítés az Azure Active regisztrált alkalmazástulajdonost keresztül történik.
További információ a szükséges hitelesítési részletek beszerzése a Media Services-fiókját az Azure Portalról: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md).

Kódírás, amely kapcsolódik a REST API használatával az Azure AD-hitelesítés, a részleteket a cikk [eléréséhez az Azure Media Services API REST-tel az Azure AD-hitelesítés](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>További lépések
Az Azure AD-hitelesítés használata a Media Services REST API-val kapcsolatban lásd: [eléréséhez az Azure Media Services API REST-tel az Azure AD-hitelesítés](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

