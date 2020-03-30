---
title: Media Services-entitások kezelése REST-el | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan kezelheti a Media Services-entitásokat a REST API-val.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283315"
---
# <a name="managing-media-services-entities-with-rest"></a>Media Services-entitások kezelése REST-el  

> [!div class="op_single_selector"]
> * [Többi](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

A Microsoft Azure Media Services egy OData v3-ra épülő REST-alapú szolgáltatás. Az entitásokat ugyanúgy veheti fel, kérdezheti le, frissítheti és törölheti, mint bármely más OData-szolgáltatásban. A kivételeket adott esetben kihívják. Az OData protokollról további információt az [Open Data Protocol dokumentációjában](https://www.odata.org/documentation/)talál.

Ez a témakör bemutatja, hogyan kezelheti az Azure Media Services-entitásokat a REST szolgáltatással.

>[!NOTE]
> 2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. 2017. április 1-jén például a fiókban lévő bármely feladatrekord automatikusan törlődik. Ha archiválnia kell a feladat/feladat adatait, használhatja a jelen témakörben leírt kódot.

## <a name="considerations"></a>Megfontolandó szempontok  

Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="adding-entities"></a>Entitások hozzáadása
A Media Services minden entitása hozzáadódik egy entitáskészlethez, például az Eszközök höz egy POST HTTP-kérelemen keresztül.

A következő példa bemutatja, hogyan hozhat létre AccessPolicy házirendet.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Entitások lekérdezése
Az entitások lekérdezése és listázása egyszerű, és csak egy GET HTTP-kérelmet és opcionális OData-műveleteket foglal magában.
A következő példa az összes MediaProcessor-entitás listáját olvassa be.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Egy adott entitást vagy egy adott entitáshoz társított összes entitáskészletet is lekérhet, például a következő példákban:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

A következő példa csak az összes feladat state tulajdonságát adja vissza.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

A következő példa a "SampleTemplate" nevű jobtemplateokat adja vissza.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> A $expand művelet nem támogatott a Media Services, valamint a nem támogatott LINQ módszerek leírt LINQ szempontok (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Számbavétel entitások nagy gyűjteményein keresztül
Entitások lekérdezésekénél, egyszerre legfeljebb 1000 entitás tért vissza, mert a nyilvános REST v2 1000-es eredményeket korlátozza a lekérdezési eredményeket. Használja **a kihagyás** és **a felső** entitások nagy gyűjteményén keresztül történő számbavételt. 

A következő példa bemutatja, hogyan használhatja **a kihagyás** és **a felső** gombot az első 2000 feladat kihagyására és a következő 1000 feladat lefelvételére.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Entitások frissítése
Az entitás típusától és az állapottól függően, amelyen van, frissítheti az entitás tulajdonságait egy PATCH, PUT vagy MERGE HTTP-kérelmeken keresztül. Ezekről a műveletekről további információt a [PATCH/PUT/MERGE című témakörben talál.](https://msdn.microsoft.com/library/dd541276.aspx)

A következő kód példa bemutatja, hogyan frissítheti a Name tulajdonságot egy eszköz entitáson.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Entitások törlése
Az entitások törölhetők a Media Services szolgáltatásban egy DELETE HTTP-kérelem használatával. Az entitástól függően fontos lehet az entitások törlésének sorrendje. Például az entitások, például az eszközök megkövetelik, hogy az eszköz törlése előtt visszavonja (vagy törölje) az adott eszközre hivatkozó összes lokátort.

A következő példa bemutatja, hogyan törölheti a lokátor, amely egy fájl feltöltéséhez használt blob storage.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

