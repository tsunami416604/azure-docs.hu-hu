---
title: Media Services-műveletek REST API – áttekintés | Microsoft dokumentumok
description: A "Media Services Operations REST" API-t munkahelyek, eszközök, élő csatornák és egyéb erőforrások létrehozására használják egy Media Services-fiókban. Ez a cikk az Azure Media Services v2 REST API áttekintését tartalmazza.
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
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773660"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services-műveletek REST API – áttekintés 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A **Media Services Operations REST** API-t munkahelyek, eszközök, élő csatornák és egyéb erőforrások létrehozására használják egy Media Services-fiókban. További információt a [Media Services Operations REST API-hivatkozáscímű témakörben talál.](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)

A Media Services olyan REST API-t biztosít, amely json- vagy atom+pub XML formátumot is fogad. A Media Services REST API-hoz speciális HTTP-fejlécek szükségesek, amelyeket minden ügyfélnek el kell küldenie a Media Services szolgáltatáshoz való csatlakozáskor, valamint választható fejléceket. A következő szakaszok azokat a fejléceket és HTTP-műveleteket ismertetik, amelyeket a kérelmek létrehozásakor és a Media Services válaszai fogadásakor használhat.

A Media Services REST API-hitelesítése az Azure Active Directory-hitelesítésen keresztül történik, amelyet az [Azure AD-hitelesítés használata az Azure Media Services API REST szolgáltatással való eléréséhez című](media-services-rest-connect-with-aad.md) cikk ismertet.

## <a name="considerations"></a>Megfontolandó szempontok

A REST használatakor a következő szempontok érvényesek.

* Entitások lekérdezésekénél, egyszerre legfeljebb 1000 entitás tért vissza, mert a nyilvános REST v2 1000-es eredményeket korlátozza a lekérdezési eredményeket. A **Skip** and **Take** (.NET) / **top** (REST) kapcsolót kell használnia [a .NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példában leírtak szerint.](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) 
* A JSON használatakor és a **__metadata** kulcsszó használatának megadásakor (például csatolt objektumra való hivatkozás) az **Elfogadás** fejlécet [JSON Verbose formátumra](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) kell állítani (lásd a következő példát). Az Odata nem érti a **kérelemben** lévő __metadata tulajdonságot, hacsak nem állítja részletesre.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-kérelmek fejlécei
A Media Services szolgáltatásba beküldött minden híváshoz tartozik egy sor szükséges fejléc, amelyet bele kell foglalnia a kérelembe, valamint egy sor választható fejlécet is, amelyeket érdemes lehet felvenni. Az alábbi táblázat a szükséges fejléceket sorolja fel:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Engedélyezés |Tulajdonos |A tulajdonos az egyetlen elfogadott engedélyezési mechanizmus. Az értéknek tartalmaznia kell az Azure Active Directory által biztosított hozzáférési jogkivonatot is. |
| x-ms-verzió |Decimal |2.17 (vagy legújabb verzió)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Mivel a Media Services az OData segítségével teszi elérhetővé a REST API-kat, a DataServiceVersion és a MaxDataServiceVersion fejléceket minden kérelemben fel kell tüntetni; azonban, ha nem, akkor jelenleg a Media Services feltételezi, hogy a DataServiceVersion használatban lévő értéke 3.0.
> 
> 

A következő egy sor választható fejlécek:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Dátum |RFC 1123 dátum |A kérelem időbélyegzője |
| Elfogadás |Tartalomtípus |A válaszkért tartalomtípusa, például a következő:<p> -alkalmazás/json;odata=verbose<p> - alkalmazás/atom+xml<p> Válaszok lehet egy másik tartalomtípus, például egy blob fetch, ahol a sikeres válasz tartalmazza a blob stream, mint a hasznos adat. |
| Elfogadás-kódolás |Gzip, leereszt |GZIP és DEFLATE kódolás, ha alkalmazható. Megjegyzés: Nagy erőforrások esetén a Media Services figyelmen kívül hagyhatja ezt a fejlécet, és nem tömörített adatokat adhat vissza. |
| Accept-Language |"en", "es", és így tovább. |A válasz előnyben részesített nyelvét adja meg. |
| Elfogadás-karakterkészlet |Charset típusú, mint "UTF-8" |Az alapértelmezett érték az UTF-8. |
| X-HTTP-módszer |HTTP-módszer |Lehetővé teszi, hogy az ügyfelek vagy tűzfalak, amelyek nem támogatják a HTTP-módszerek, mint a PUT vagy törlése használni ezeket a módszereket, bújtatott keresztül GET hívás. |
| Content-Type |Tartalomtípus |A kérelemtörzs tartalomtípusa put vagy postai kérelmekben. |
| ügyfél-kérelem-azonosító |Sztring |A hívó által definiált érték, amely azonosítja az adott kérelmet. Ha meg van adva, ez az érték szerepelni fog a válaszüzenetben a kérelem leképezésének módjaként. <p><p>**Fontos**<p>Az értékeket 2096b-re (2k) kell korlátozni. |

## <a name="standard-http-response-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-válaszfejlécek
Az alábbi fejlécek, amelyek a kért erőforrástól és a végrehajtani kívánt művelettől függően visszaadhatók.

| Fejléc | Típus | Érték |
| --- | --- | --- |
| kérés-azonosító |Sztring |Az aktuális művelet egyedi azonosítója, a létrehozott szolgáltatás. |
| ügyfél-kérelem-azonosító |Sztring |A hívó által az eredeti kérelemben megadott azonosító, ha van ilyen. |
| Dátum |RFC 1123 dátum |A kérelem feldolgozásának dátuma/időpontja. |
| Content-Type |Változó |A választörzs tartalomtípusa. |
| Tartalomkódolás |Változó |Gzip vagy leereszteni, ha szükséges. |

## <a name="standard-http-verbs-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-műveletek
Az alábbi lista tartalmazza a HTTP-kérelmek megtételéhez használható HTTP-műveletek teljes listáját:

| Művelet | Leírás |
| --- | --- |
| GET |Egy objektum aktuális értékét adja eredményül. |
| POST |Objektumot hoz létre a megadott adatok alapján, vagy elküld egy parancsot. |
| PUT |Objektumot cserél le, vagy elnevezett objektumot hoz létre (ha van ilyen). |
| DELETE |Objektum törlése. |
| Egyesítése |Egy meglévő objektumot frissít elnevezett tulajdonságmódosításokkal. |
| HEAD |Get válasz objektumának metaadatait adja vissza. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>A Media Services entitásmodell felfedezése és böngészése
A Media Services-entitások felderíthetőbbé adására a $metadata művelet használható. Lehetővé teszi az összes érvényes entitástípus, entitástulajdonságok, társítások, függvények, műveletek és így tovább beolvasását. A $metadata művelet hozzáadása a Media Services REST API-végpont végén, elérheti ezt a felderítési szolgáltatást.

 /api/$metadata.

Ha a metaadatokat böngészőben szeretné megtekinteni, az "?api-version=2.x" parancsot az URI végéhez kell fűzött, vagy ne vegye fel az x-ms-verzió fejlécét a kérelembe.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Hitelesítés a Media Services REST szolgáltatásával az Azure Active Directory használatával

A REST API-n végzett hitelesítés az Azure Active Directory (AAD) szolgáltatáson keresztül történik.
A Media Services-fiók hoz szükséges hitelesítési adatainak az Azure Portalról való bejutásáról az [Access the Azure Media Services API azure AD-hitelesítéssel című](media-services-use-aad-auth-to-access-ams-api.md)témakörben talál részleteket.

A REST API-hoz Azure AD-hitelesítéssel kapcsolódó kód írásával kapcsolatos részleteket az [Azure Media Services API REST-tel való eléréséhez az Azure AD-hitelesítés használata](media-services-rest-connect-with-aad.md)című témakörben olvashat.

## <a name="next-steps"></a>További lépések
Az Azure AD-hitelesítés media Services REST API-val való használatáról az [Azure AD-hitelesítés használata az Azure Media Services API REST szolgáltatással való eléréséhez](media-services-rest-connect-with-aad.md)című témakörben olvashat.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

