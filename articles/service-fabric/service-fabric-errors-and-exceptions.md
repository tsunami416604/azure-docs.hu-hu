---
title: Gyakori FabricClient-kivételek
description: Ez a témakör azokat a gyakori kivételeket és hibákat ismerteti, amelyeket a FabricClient API-k az alkalmazás- és fürtkezelési műveletek végrehajtása során elhajthatnak.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457932"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>A FabricClient API-k használata során előforduló gyakori kivételek és hibák
A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API-k lehetővé teszik a fürt- és alkalmazás-rendszergazdák számára, hogy felügyeleti feladatokat hajtsanak végre egy Service Fabric-alkalmazásban, szolgáltatáson vagy fürtön. Például az alkalmazások telepítése, frissítése és eltávolítása, a fürt állapotának ellenőrzése vagy egy szolgáltatás tesztelése. Az alkalmazásfejlesztők és a fürtrendszergazdák a FabricClient API-k segítségével fejleszthetnek eszközöket a Service Fabric-fürt és -alkalmazások kezeléséhez.

Számos különböző típusú műveletek, amelyek a FabricClient használatával hajtható végre.  Minden módszer kivételeket okozhat a helytelen bemeneti, futásidejű hibák vagy átmeneti infrastruktúra-problémák miatt beszerzett hibák miatt.  Tekintse meg az API-referencia dokumentációt, hogy megtudja, mely kivételek dobott egy adott módszer. Vannak azonban kivételek, amelyek számos különböző [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API-k által dobott. Az alábbi táblázat felsorolja a FabricClient API-kban közös kivételeket.

| Kivétel | Dobott, ha |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum zárt állapotban van. A használt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum ártalmatlanítása és egy új [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum példányossá. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A művelet idővel csökkent. [Az OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) akkor kerül visszaadásra, ha a művelet a MaxOperationTimeout-nál többet vesz igénybe. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |A művelet hozzáférési ellenőrzése nem sikerült. E_ACCESSDENIED visszatért. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Futásidejű hiba történt a művelet végrehajtása közben. A FabricClient metódusok bármelyike potenciálisan elhajthatja a [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)tulajdonságot, az [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) tulajdonság a kivétel pontos okát jelzi. A hibakódok a [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) számbavételben vannak definiálva. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A művelet valamilyen átmeneti hibamiatt meghiúsult. Egy művelet például sikertelen lehet, mert a replikák kvóruma átmenetileg nem érhető el. Az átmeneti kivételek olyan sikertelen műveleteknek felelnek meg, amelyek újratárgyalhatók. |

Néhány gyakori [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) hiba, amely a [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)függvényben adható vissza:

| Hiba | Állapot |
| --- |:--- |
| Kommunikációs hiba |Kommunikációs hiba miatt a művelet sikertelen volt, próbálkozzon újra a művelettel. |
| InvalidCredentialType |A hitelesítő adatok típusa érvénytelen. |
| InvalidX509FindType |Az X509FindType érvénytelen. |
| InvalidX509Tárolóhely |Az X509 tároló helye érvénytelen. |
| InvalidX509Üzletnév |Az X509-tároló neve érvénytelen. |
| InvalidX509Ujjlenyomat |Az X509 tanúsítvány ujjlenyomat-karakterlánca érvénytelen. |
| Érvénytelenvédelmi szint |A védelmi szint érvénytelen. |
| InvalidX509Áruház |Az X509 tanúsítványtároló nem nyitható meg. |
| Érvénytelen szubjektusnév |A tulajdonos neve érvénytelen. |
| ÉrvénytelenAllowedCommonNameList lista |A köznapi névlista-karakterlánc formátuma érvénytelen. Egy vesszővel tagolt listának kellene lennie. |

