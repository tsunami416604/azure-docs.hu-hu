---
title: Újrapróbálkozási logika a Media Services SDK for .NET |} Microsoft Docs
description: A témakör áttekintést nyújt az újrapróbálkozási logika a Media Services SDK-ban a .NET-hez.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790425"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Újrapróbálkozási logika a Media Services SDK for .NET
Átmeneti akkor fordulhat elő, az Microsoft Azure-szolgáltatások használatakor. Ha egy átmeneti hiba akkor fordul elő, a legtöbb esetben néhány újrapróbálkozás után a művelet sikeres lesz. A Media Services SDK for .NET valósítja meg az újrapróbálkozási logika átmeneti hibák, kivételek és webes kérelmek által okozott hibák társított kezelésére menti a módosításokat, és a tárolási műveletek a lekérdezések végrehajtása.  Alapértelmezés szerint a Media Services SDK for .NET újra a az alkalmazás Kivétel kiváltása előtt végrehajtja a négy újrapróbálkozási lehetőségbe. Az alkalmazás kódja majd kezelni kell a kivétel megfelelően.  

 A következő egy webes kérelem, a tárolás, a lekérdezés és a SaveChanges metódus házirendek rövid eszközöket:  

* A tárolási házirend blob storage műveletek (feltöltések vagy az eszköz fájlok letöltésére) szolgál.  
* A webalkalmazás-házirend általános webes kérelmek (például az első egy hitelesítési jogkivonatot, és a felhasználók a fürt végpontja megoldása) használható.  
* A lekérdezés házirend használt REST (például mediaContext.Assets.Where(...)) az entitás lekérdezése.  
* A SaveChanges metódus házirend használható bármi, amely módosítja az adatokat (például egy entitás hívja service függvény művelet frissítése entitás létrehozása) szolgáltatáson belül.  
  
  Ez a témakör felsorolja a kivétel típusa és hibakódok .NET-keretrendszerhez készült Media Services SDK által végrehajtott újrapróbálkozási logika.  

## <a name="exception-types"></a>Kivétel típusa
A következő táblázat ismerteti a kivételeket, amelyek a Media Services SDK for .NET kezeli, vagy nem kezeli az egyes műveletek, amelyek átmeneti okozhatnak.  

| Kivétel | Webes kérelem | Storage | Lekérdezés | A SaveChanges metódus |
| --- | --- | --- | --- | --- |
| WebException<br/>További információkért lásd: a [WebException állapotkódok](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) szakasz. |Igen |Igen |Igen |Igen |
| DataServiceClientException<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceQueryException<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceRequestException<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceTransportException |Nem |Nem |Igen |Igen |
| TimeoutException |Igen |Igen |Igen |Nem |
| SocketException |Igen |Igen |Igen |Igen |
| StorageException |Nem |Igen |Nem |Nem |
| Ioexception kivétel |Nem |Igen |Nem |Nem |

### <a name="WebExceptionStatus"></a> WebException állapotkódok
Az alábbi táblázat a WebException hibakódok az újrapróbálkozási logika van megvalósítva. A [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) számbavételi állapot kód határozza meg.  

| status | Webes kérelem | Storage | Lekérdezés | A SaveChanges metódus |
| --- | --- | --- | --- | --- |
| ConnectFailure |Igen |Igen |Igen |Igen |
| NameResolutionFailure |Igen |Igen |Igen |Igen |
| ProxyNameResolutionFailure |Igen |Igen |Igen |Igen |
| SendFailure |Igen |Igen |Igen |Igen |
| PipelineFailure |Igen |Igen |Igen |Nem |
| ConnectionClosed |Igen |Igen |Igen |Nem |
| KeepAliveFailure |Igen |Igen |Igen |Nem |
| UnknownError |Igen |Igen |Igen |Nem |
| ReceiveFailure |Igen |Igen |Igen |Nem |
| RequestCanceled |Igen |Igen |Igen |Nem |
| Időtúllépés |Igen |Igen |Igen |Nem |
| Protokollhiba lépett <br/>A kísérletek száma Protokollhiba lépett kód kezelésének HTTP állapotát vezérli. További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Igen |Igen |Igen |Igen |

### <a name="HTTPStatusCode"></a> HTTP-állapotkódok hiba
Ha a lekérdezés és a SaveChanges metódus műveletek throw DataServiceClientException, DataServiceQueryException vagy DataServiceQueryException, a HTTP állapot hibakód StatusCode tulajdonságában.  Az alábbi táblázat a hibakódok az újrapróbálkozási logika van megvalósítva.  

| status | Webes kérelem | Storage | Lekérdezés | A SaveChanges metódus |
| --- | --- | --- | --- | --- |
| 401 |Nem |Igen |Nem |Nem |
| 403 |Nem |Igen<br/>Kezeli a hosszabb ideig vár az újrapróbálkozások. |Nem |Nem |
| 408 |Igen |Igen |Igen |Igen |
| 429 |Igen |Igen |Igen |Igen |
| 500 |Igen |Igen |Igen |Nem |
| 502 |Igen |Igen |Igen |Nem |
| 503 |Igen |Igen |Igen |Igen |
| 504 |Igen |Igen |Igen |Nem |

Ha azt szeretné, hogy vessen egy pillantást a Media Services SDK for .NET újrapróbálkozási logika tényleges végrehajtására, lásd: [azure-sdk-az-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

