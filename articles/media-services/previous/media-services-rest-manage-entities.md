---
title: A Media Services entitáskezelésről REST-tel |} A Microsoft Docs
description: Ismerje meg, hogyan lehet REST API-val a Media Services-entitások kezelését.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 0f500ec776d90755d6738af80c34866105d354f5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999402"
---
# <a name="managing-media-services-entities-with-rest"></a>A Media Services REST-tel entitáskezelésről  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services egy olyan REST-alapú szolgáltatás, OData v3 épül. Adja hozzá, lekérdezés, frissítése és entitások törlése a szinte ugyanúgy, mint a többi OData-szolgáltatás. Kivételek tüntetünk fel, ha az alkalmazható. Az OData további információkért lásd: [Open Data Protocol dokumentáció](http://www.odata.org/documentation/).

Ez a témakör bemutatja, hogyan a REST-tel az Azure Media Services-entitások kezelését.

>[!NOTE]
> 2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. Például 2017. április 1. feladat rekordot a régebbi, mint a 2016. December 31-én fiókjában automatikusan törölve lesznek. Ha a feladatok/műveletek adatainak archiválásához van szüksége, használhatja a jelen témakörben található kódot.

## <a name="considerations"></a>Megfontolandó szempontok  

A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Entitások hozzáadása
Minden entitás, a Media Services entitáskészlet, eszközök, például POST HTTP-kérés kerül.

Az alábbi példa bemutatja, hogyan hozhat létre egy AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Entitások lekérdezése
Lekérdezése, valamint felsorolja az entitások nagyon egyszerű, és csak a GET HTTP-kérés és választható OData-műveleteket foglalja magában.
Az alábbi példa az összes MediaProcessor entitások listájának beolvasása.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Egy adott entitáshoz vagy egy adott entitáshoz társított, például a következő példákban az összes entitáskészletek is lekérhet:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Az alábbi példa az összes feladat csak a State tulajdonsága adja vissza.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Az alábbi példa az összes JobTemplates "SampleTemplate." nevű adja vissza.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Az $expand művelet nem támogatott a Media Services, valamint a nem támogatott LINQ módszerek LINQ szempontokat (WCF-adatszolgáltatások).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Nagy kolekce entit számbavétele
Entitások lekérdezésekor korlátozva van az 1000 entitások adja vissza egy adott időpontban, mert a nyilvános REST v2 korlátozza az 1000 eredmények lekérdezési eredményeket. Használat **kihagyása** és **felső** keresztül a nagy entitások gyűjteményét számbavétele. 

Az alábbi példa bemutatja, hogyan használható **kihagyása** és **felső** hagyja ki az első 2000 feladatok és a következő 1000 feladatok beolvasása.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Entitások frissítése
Az entitás típusa és az állapot azt frissítheti az adott entitástól keresztül egy JAVÍTÁSI tulajdonságok PUT vagy egyesítési HTTP-kérelmek. Ezek a műveletek kapcsolatos további információkért lásd: [javítás/PUT/EGYESÍTÉS](https://msdn.microsoft.com/library/dd541276.aspx).

Az alábbi példakód bemutatja a Name tulajdonság frissítése az Eszközintelligencia entitás.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Entitások törlése
Entitások törlése HTTP-kérés segítségével lehet törölni a Media Services. Az entitás entitások törölje a sorrend fontos is lehet. Például, például a eszközök megköveteli, hogy Ön visszavonása (vagy törli) az eszköz törlése előtt az adott eszköz hivatkozó összes keresőt.

Az alábbi példa bemutatja, hogyan törölni egy keresőt, amellyel a fájl feltöltése a blob storage-bA.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

