---
title: Közös FabricClient kivételek |} Microsoft Docs
description: A közös kivételeket és alkalmazások és a fürt felügyeleti műveletek végrehajtása közben a FabricClient API-k által is okozott hibák ismerteti.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: ryanwi
ms.openlocfilehash: e854ed42b6af8bc090950e8399e3229e202a2ed0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293412"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Közös kivételeket és hibák, amikor a FabricClient API-k használata
A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API-k lehetővé teszik a felügyeleti feladatokat hajthat végre a Service Fabric-alkalmazás, szolgáltatás vagy fürt a fürt- és rendszergazdák. Például alkalmazás központi telepítése, frissítése és eltávolítása, a fürt ellenőrzése, vagy egy szolgáltatás tesztelése. Alkalmazásfejlesztők és a fürt rendszergazdák a FabricClient API-k használhatja a Service Fabric-fürt és az alkalmazások kezelésére szolgáló eszközök fejlesztéséhez.

Nincsenek számos különböző típusú műveleteket, amelyek FabricClient használatával végezheti el.  Az egyes módszerek is throw kivételek miatt helytelen bemeneti hibák, futásidejű hibák vagy átmeneti infrastruktúra kapcsolatos problémákkal kapcsolatban.  Tekintse meg az API-referenciadokumentáció található mely kivételek egy adott módszerrel. Bizonyos kivételek vannak, azonban számos által is okozhat, amelyek különböző [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API-k. A következő táblázat a kivételeket a FabricClient API-k által közösen használt.

| Kivétel | Mikor történt |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objektum zárt állapotban van. Rendelkezési a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objektum használja, és a hozható létre egy új [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objektum. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |A művelet túllépte az időkorlátot. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) ad vissza, ha a művelet kiszámítása konfigurált MaxOperationTimeout befejezéséhez. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |A hozzáférés-ellenőrzést, a művelet sikertelen volt. E_ACCESSDENIED adja vissza. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Futási hiba történt a művelet végrehajtása közben. A FabricClient módszerekkel potenciálisan throw [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), a [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) tulajdonság jelzi a kivétel pontos okát. Hibakódok vannak definiálva a [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) enumerálása. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |A művelet valamilyen egy átmeneti hiba miatt nem sikerült. Egy művelet például meghiúsulhat, mert replikák másodlagosak átmenetileg nem érhető el. Átmeneti kivételek követően újra megkísérelhető a sikertelen műveletek felelnek meg. |

Néhány gyakori [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) a hibákat, a adhatók vissza a [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Hiba | Állapot |
| --- |:--- |
| CommunicationError |Kommunikációs hiba miatt a művelet sikertelen, próbálja megismételni a műveletet. |
| InvalidCredentialType |A hitelesítőadat-típus érvénytelen. |
| InvalidX509FindType |A X509FindType érvénytelen. |
| InvalidX509StoreLocation |A X509 tárolási helye érvénytelen. |
| InvalidX509StoreName |A X509 tároló neve érvénytelen. |
| InvalidX509Thumbprint |A X509 tanúsítvány ujjlenyomata karakterlánc érvénytelen. |
| InvalidProtectionLevel |A védelmi szint érvénytelen. |
| InvalidX509Store |Nem lehet megnyitni a tanúsítványtárolót a X509. |
| InvalidSubjectName |A tulajdonos neve érvénytelen. |
| InvalidAllowedCommonNameList |Köznapi név lista karakterlánc formátuma érvénytelen. Vesszővel tagolt listájának kell lennie. |

