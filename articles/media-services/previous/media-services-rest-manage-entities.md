---
title: Media Services entitások kezelése REST-tel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelheti Media Services entitásokat a REST APIokkal.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283315"
---
# <a name="managing-media-services-entities-with-rest"></a>Media Services entitások kezelése a REST-tel  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services a OData v3-ra épülő REST-alapú szolgáltatás. Az entitásokat ugyanúgy veheti fel, kérdezheti le, frissítheti és törölheti, mint bármely más OData-szolgáltatásban. A kivételeket akkor kell meghívni, ha alkalmazható. A OData kapcsolatos további információkért lásd: [az adatprotokoll dokumentációjának megnyitása](https://www.odata.org/documentation/).

Ez a témakör bemutatja, hogyan felügyelheti Azure Media Services entitásokat a REST használatával.

>[!NOTE]
> 2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. Például 2017. április 1-jén a fiókban lévő minden olyan feladatra vonatkozó rekordot, amely a 2016 december 31-én régebbi, automatikusan törlődik. Ha archiválni szeretné a feladat/feladat adatait, használhatja a jelen témakörben ismertetett kódot.

## <a name="considerations"></a>Megfontolandó szempontok  

A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Entitások hozzáadása
Media Services minden entitása hozzá lesz adva egy entitáshoz, például az eszközökhöz a HTTP-kérelem küldése után.

Az alábbi példa bemutatja, hogyan hozhat létre egy AccessPolicy.

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
Az entitások lekérdezése és listázása egyszerű, és csak a GET HTTP-kérést és a választható OData műveleteket foglalja magában.
A következő példa az összes MediaProcessor-entitás listáját kéri le.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Egy adott entitáshoz társított entitást vagy az összes entitás készletét is lekérheti, például az alábbi példákban:

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

A következő példa csak az összes feladat State (állapot) tulajdonságát adja vissza.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

A következő példa a "SampleTemplate" nevű összes JobTemplates adja vissza.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> A $expand művelet nem támogatott a Media Servicesban, valamint a LINQ-megfontolásokban (WCF Data Services) leírt nem támogatott LINQ metódusokban.
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Az entitások nagy gyűjteményének enumerálása
Az entitások lekérdezése esetén a rendszer egy legfeljebb 1000 entitást ad vissza, mert a nyilvános REST v2 a lekérdezés eredményét 1000 eredményre korlátozza. A **kihagyás** és a **felső** érték használata az entitások nagy gyűjteményéből való enumeráláshoz. 

Az alábbi példa bemutatja, hogyan használható a **skip** és a **top** az első 2000 feladat kihagyása és a következő 1000 feladatok beszerzése.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Entitások frissítése
Az entitás típusától és a benne foglalt állapottól függően a HTTP-kérések használatával frissítheti az entitás tulajdonságait. További információ ezekről a műveletekről: [patch/Put/Merge](https://msdn.microsoft.com/library/dd541276.aspx).

A következő mintakód bemutatja, hogyan lehet frissíteni a name tulajdonságot egy eszköz entitáson.

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
Az entitások TÖRLÉSi HTTP-kérelem használatával törölhetők Media Servicesban. Az entitástól függően az entitások törlésének sorrendje fontos lehet. Például az eszközökhöz hasonló entitások megkövetelik, hogy visszavonja (vagy törölje) az adott objektumra hivatkozó összes lokátort az eszköz törlése előtt.

Az alábbi példa bemutatja, hogyan törölhet egy olyan lokátort, amely a fájlok blob Storage-ba való feltöltésére szolgál.

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

