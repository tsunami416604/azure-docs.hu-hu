---
title: Általános FabricClient-kivételek elvetve
description: Ismerteti azokat a gyakori kivételeket és hibákat, amelyeket a FabricClient API-k okozhatnak az alkalmazás-és fürtszolgáltatási műveletek végrehajtása során.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: d22184d6d275adb95932fb29338768210547c199
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392624"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>A FabricClient API-k használata során előforduló gyakori kivételek és hibák
A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API-k lehetővé teszik a fürt és az alkalmazás rendszergazdái számára, hogy rendszergazdai feladatokat végezzenek el egy Service Fabric alkalmazáson, szolgáltatáson vagy fürtön. Például az alkalmazások központi telepítése, frissítése és eltávolítása, a fürt állapotának ellenőrzése vagy a szolgáltatás tesztelése. Az alkalmazások fejlesztői és a FabricClient API-kat használhatnak a Service Fabric-fürt és-alkalmazások kezeléséhez szükséges eszközök fejlesztéséhez.

A FabricClient használatával számos különböző típusú műveletet lehet elvégezni.  Az egyes módszerek kivételeket okozhatnak a helytelen bemeneti, futásidejű hibák vagy átmeneti infrastrukturális problémák miatti hibákhoz.  Tekintse meg az API-dokumentációt, amelyből megtudhatja, hogy mely kivételeket dobják egy adott módszer. Vannak azonban kivételek, amelyeket számos különböző [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API okozhat. A következő táblázat felsorolja azokat a kivételeket, amelyek közösek a FabricClient API-k között.

| Kivétel | Kidobás időpontja |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum lezárt állapotban van. Az Ön által használt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -objektum és az új [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -objektum példányának elvetése. |
| [System. Timeoutexception osztályról](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A művelet időkorlátja lejárt. A rendszer akkor adja vissza a [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) , ha a művelet végrehajtása a MaxOperationTimeout-nál több időt vesz igénybe. |
| [System. UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Sikertelen volt a művelethez való hozzáférés-ellenőrzési művelet. E_ACCESSDENIED visszaadva. |
| [System. Fabric. FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Futásidejű hiba történt a művelet végrehajtása közben. A FabricClient metódusok bármelyike potenciálisan [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), a [errorcode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) tulajdonság a kivétel pontos okát jelzi. A hibakódok meghatározása a [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) enumerálásban történik. |
| [System. Fabric. FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A művelet valamilyen átmeneti hiba miatt nem sikerült. Előfordulhat például, hogy egy művelet meghiúsul, mert a replikák kvóruma átmenetileg nem érhető el. Az átmeneti kivételek az újrapróbálható sikertelen műveleteknek felelnek meg. |

Néhány gyakori [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) -hiba, amely egy [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)adható vissza:

| Hiba | Állapot |
| --- |:--- |
| CommunicationError |Egy kommunikációs hiba miatt a művelet meghiúsult, próbálja megismételni a műveletet. |
| InvalidCredentialType |A hitelesítő adat típusa érvénytelen. |
| InvalidX509FindType |A X509FindType érvénytelen. |
| InvalidX509StoreLocation |A X509 tároló helye érvénytelen. |
| InvalidX509StoreName |A X509-tároló neve érvénytelen. |
| InvalidX509Thumbprint |A X509 tanúsítványának ujjlenyomat-karakterlánca érvénytelen. |
| InvalidProtectionLevel |A védelmi szint érvénytelen. |
| InvalidX509Store |Nem lehet megnyitni a X509-tanúsítványtárolót. |
| InvalidSubjectName |A tulajdonos neve érvénytelen. |
| InvalidAllowedCommonNameList |A köznapi név lista karakterláncának formátuma érvénytelen. Vesszővel tagolt listának kell lennie. |

