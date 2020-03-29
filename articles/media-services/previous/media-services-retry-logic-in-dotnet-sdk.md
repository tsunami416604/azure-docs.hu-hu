---
title: A Media Services SDK for . Microsoft dokumentumok
description: A témakör áttekintést nyújt a Media Services SDK .NET újrapróbálkozási logikájának áttekintéséről.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094663"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>A .NET Media Services SDK-ban az újrapróbálkozási logika  

A Microsoft Azure-szolgáltatásokkal való munka során átmeneti hibák léphetnek fel. Ha átmeneti hiba lép fel, a legtöbb esetben néhány újrapróbálkozás után a művelet sikeres. A .NET Media Services SDK-ja az újrapróbálkozási logikát valósítja meg a webes kérelmek, a lekérdezések végrehajtása, a módosítások és a tárolási műveletek által okozott kivételek és hibák miatti átmeneti hibák kezeléséhez.  Alapértelmezés szerint a Media Services SDK a .NET végrehajtja a négy újrapróbálkozások előtt újra dobott a kivétel az alkalmazás. Az alkalmazásban lévő kódnak megfelelően kell kezelnie ezt a kivételt.  

 A webkérelemre, tárolásra, lekérdezésre és mentett módosításra vonatkozó házirendek rövid iránymutatásai:  

* A storage-szabályzat blob tárolási műveletek (feltöltések vagy az eszközfájlok letöltése) használják.  
* A webkérelem-házirend általános webes kérelmekhez használatos (például hitelesítési jogkivonat beszerzésére és a felhasználók fürtvégpontjának feloldására).  
* A lekérdezési házirend entitások REST-ből történő lekérdezésére szolgál (például mediaContext.Assets.Where(...)).  
* A SaveChanges házirend et a szolgáltatáson belüli adatokat módosító műveletekhez használja (például egy entitást frissítő entitás létrehozásához, egy művelet szolgáltatásfunkciójának hívásához).  
  
  Ez a témakör a Media Services SDK által a .NET újrapróbálkozási logikához kezelt kivételtípusokat és hibakódokat sorolja fel.  

## <a name="exception-types"></a>Kivételtípusok
Az alábbi táblázat azokat a kivételeket ismerteti, amelyeket a Media Services SDK a .NET leíróihoz kezel, vagy nem kezeli azokat a műveleteket, amelyek átmeneti hibákat okozhatnak.  

| Kivétel | Webes kérelem | Storage | Lekérdezés | Módosítások mentése |
| --- | --- | --- | --- | --- |
| WebException<br/>További információt a [WebException állapotkódok](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) című szakaszban talál. |Igen |Igen |Igen |Igen |
| DataServiceClientException<br/> További információ: [HTTP hibaállapot-kódok](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceQueryException<br/> További információ: [HTTP hibaállapot-kódok](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceRequestException<br/> További információ: [HTTP hibaállapot-kódok](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceTransportException |Nem |Nem |Igen |Igen |
| IdőoutKivétel |Igen |Igen |Igen |Nem |
| SocketException |Igen |Igen |Igen |Igen |
| StorageException (StorageException) |Nem |Igen |Nem |Nem |
| IOKivétel |Nem |Igen |Nem |Nem |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>WebException állapotkódok
Az alábbi táblázat bemutatja, hogy a WebException melyik hibakódja esetén valósítja meg az újrapróbálkozási logikát. A [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) enumerálás határozza meg az állapotkódokat.  

| status | Webes kérelem | Storage | Lekérdezés | Módosítások mentése |
| --- | --- | --- | --- | --- |
| Csatlakozási hiba |Igen |Igen |Igen |Igen |
| NameResolutionFailure |Igen |Igen |Igen |Igen |
| ProxyNameResolutionFailure |Igen |Igen |Igen |Igen |
| Sikertelen küldési hiba |Igen |Igen |Igen |Igen |
| PipelineFailure (Folyamathiba) |Igen |Igen |Igen |Nem |
| Kapcsolat lezárva |Igen |Igen |Igen |Nem |
| KeepAliveFailure (KeepAliveFailure) |Igen |Igen |Igen |Nem |
| Ismeretlen hiba |Igen |Igen |Igen |Nem |
| Fogadási hiba |Igen |Igen |Igen |Nem |
| Kéréstörölve |Igen |Igen |Igen |Nem |
| Időkorlát |Igen |Igen |Igen |Nem |
| Protokollhiba <br/>A ProtocolError újrapróbálkozását a HTTP-állapotkód-kezelés vezérli. További információ: [HTTP hibaállapot-kódok](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Igen |Igen |Igen |Igen |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>HTTP-hibaállapot-kódok
Amikor a Query és a SaveChanges műveletek a DataServiceClientException, a DataServiceQueryException vagy a DataServiceQueryException metódust dobják, a HTTP-hibaállapot-kód a StatusCode tulajdonságban jelenik meg.  Az alábbi táblázat bemutatja, hogy az újrapróbálkozási logika mely hibakódokhoz van megvalósítva.  

| status | Webes kérelem | Storage | Lekérdezés | Módosítások mentése |
| --- | --- | --- | --- | --- |
| 401 |Nem |Igen |Nem |Nem |
| 403 |Nem |Igen<br/>Az újrapróbálkozások kezelése hosszabb várakozással. |Nem |Nem |
| 408 |Igen |Igen |Igen |Igen |
| 429 |Igen |Igen |Igen |Igen |
| 500 |Igen |Igen |Igen |Nem |
| 502 |Igen |Igen |Igen |Nem |
| 503 |Igen |Igen |Igen |Igen |
| 504 |Igen |Igen |Igen |Nem |

Ha meg szeretné tekinteni a Media Services SDK .NET újrapróbálkozási logikára vonatkozó tényleges megvalósítását, olvassa el az [azure-sdk-for-media-services című témakört.](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

