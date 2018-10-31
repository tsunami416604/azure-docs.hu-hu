---
title: Újrapróbálkozási logika, a Media Services SDK-ban a .NET-hez |} A Microsoft Docs
description: A témakör áttekintést nyújt az újrapróbálkozási logika a Media Services SDK-ban a .NET-hez.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: juliako
ms.openlocfilehash: 0a4c9db8da046e901241bc383098013b2acc6bb2
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242262"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Újrapróbálkozási logika, a Media Services SDK-ban a .NET-hez
Az Microsoft Azure-szolgáltatások használatakor is fordulnak elő átmeneti hibák. Ha egy átmeneti hiba történik, a legtöbb esetben a művelet sikeres néhány újrapróbálkozás után. A Media Services SDK for .NET valósítja meg az újrapróbálkozási logika átmeneti hibák, kivételek és a webes kérések által okozott hibák kezeléséhez, a lekérdezések, a menti a módosításokat, és a tárolási műveletek végrehajtása.  Alapértelmezés szerint a .NET-keretrendszerhez készült Media Services SDK négy újrapróbálkozások ismételt kivétel az alkalmazásnak a kivétel előtt hajtja végre. Az alkalmazás kódja majd kezelni kell ehhez a kivételhez megfelelően.  

 A következő egy webes kérelem, tároló, lekérdezés és létrehozva házirendek rövid útmutató:  

* A Storage szabályzatra van szükség, a blob storage-műveletek (feltöltések vagy az adategység-fájlok letöltését).  
* A webes kapcsolatkérelmi házirend általános webes kérelmek (például egy hitelesítési tokent első felismerését és feloldását, a felhasználók fürtvégpont) használatos.  
* A lekérdezés házirendet használja az entitások a REST (például mediaContext.Assets.Where(...)).  
* A létrehozva csoportházirend csinál semmit, amely módosítja az adatokat a szolgáltatásban (például frissítése egy entitásban egy szolgáltatás függvény hívása egy művelet egy entitás létrehozása) használatos.  
  
  Ez a témakör felsorolja a kivételtípus és a .NET-keretrendszerhez készült Media Services SDK által kezelt hibakódok újrapróbálkozási logika.  

## <a name="exception-types"></a>Kivételtípusok
A következő táblázat ismerteti a kivételeket, amelyek a .NET-keretrendszerhez készült Media Services SDK kezeli, vagy nem kezeli a néhány művelet, amely az átmeneti hibákat okozhat.  

| Kivétel | Webes kérelem | Storage | Lekérdezés | Létrehozva |
| --- | --- | --- | --- | --- |
| WebException<br/>További információkért lásd: a [WebException állapotkódok](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) szakaszban. |Igen |Igen |Igen |Igen |
| Dataserviceclientexception típusú kivétel<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceQueryException<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceRequestException<br/> További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceTransportException |Nem |Nem |Igen |Igen |
| TimeoutException |Igen |Igen |Igen |Nem |
| SocketException |Igen |Igen |Igen |Igen |
| StorageException |Nem |Igen |Nem |Nem |
| Ioexception kivétel |Nem |Igen |Nem |Nem |

### <a name="WebExceptionStatus"></a> WebException állapotkódok
Az alábbi táblázat mutatja, melyik WebException hiba kódok az újrapróbálkozási logika van megvalósítva. A [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) enumerálás a állapotkódok határozza meg.  

| status | Webes kérelem | Storage | Lekérdezés | Létrehozva |
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
| Protokollhiba lépett <br/>Az újrapróbálkozás Protokollhiba lépett a HTTP állapot kód kezelésének vezérli. További információkért lásd: [HTTP-állapotkódok hiba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Igen |Igen |Igen |Igen |

### <a name="HTTPStatusCode"></a> HTTP-állapotkódok hiba
Lekérdezési és létrehozva műveletei throw dataserviceclientexception típusú kivétel, DataServiceQueryException vagy DataServiceQueryException, amikor a StatusCode tulajdonságban a HTTP-hiba állapotkódot adott vissza.  Az alábbi táblázat a hibakódok az újrapróbálkozási logika van megvalósítva.  

| status | Webes kérelem | Storage | Lekérdezés | Létrehozva |
| --- | --- | --- | --- | --- |
| 401 |Nem |Igen |Nem |Nem |
| 403 |Nem |Igen<br/>A hosszabb ideig vár az újrapróbálkozások kezelésére. |Nem |Nem |
| 408 |Igen |Igen |Igen |Igen |
| 429 |Igen |Igen |Igen |Igen |
| 500 |Igen |Igen |Igen |Nem |
| 502 |Igen |Igen |Igen |Nem |
| 503 |Igen |Igen |Igen |Igen |
| 504 |Igen |Igen |Igen |Nem |

Ha szeretne vessen egy pillantást a Media Services SDK for .NET újrapróbálkozási logikát tényleges megvalósítását, lásd: [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

