---
title: A régió- vagy Termékváltozat-sorozat nem érhető el – Azure
description: Egyes Termékváltozat-sorozatok nem érhetők el a kiválasztott előfizetés ebben a régióban, amely előfizetés-kezelési támogatási kérelmet igényelhet.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843632"
---
# <a name="region-or-sku-unavailable"></a>Nem elérhető régió vagy termékváltozat

Ez a cikk ismerteti, hogyan oldja meg a problémát egy Azure-előfizetés nem fér hozzá egy régióhoz vagy egy virtuális gép termékváltozatához.

## <a name="symptoms"></a>Probléma

Virtuális gép telepítésekor az alábbi hibaüzenetek egyike jelenik meg:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

A fenntartott virtuálisgép-példányok vásárlásakor az alábbi hibaüzenetek egyike jelenik meg:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

A számítási alapkvóta növelésére irányuló támogatási kérelem létrehozásakor egy régió vagy egy termékváltozatcsalád nem választható ki.

## <a name="solution"></a>Megoldás

Először azt javasoljuk, hogy fontolja meg egy alternatív régió vagy termékváltozat, amely megfelel az üzleti igényeinek.

Ha nem talál megfelelő régiót vagy termékváltozatot, hozzon létre egy **Előfizetés-kezelési** [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az alábbi lépések szerint:

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget. Ezután válassza **az Új támogatási kérelem lehetőséget.**

1. Az **Alapismeretek**csoportban a **Probléma típusmezőben**válassza **az Előfizetéskezelés**lehetőséget.

1. Válasszon **egy előfizetést,** és adjon meg egy rövid leírást az **Összegzés**ben.

   ![Az Új támogatási kérelem alapjai lapja](./media/SKU-series-unavailable/support-request-basics.png)

1. A **Probléma típus esetén**válassza a **Problématípus kiválasztása**lehetőséget.

1. A **Problématípus kiválasztása területen**válasszon egy beállítást, például: Nem lehet hozzáférni az **előfizetésemhez vagy az erőforrásomhoz** > **A probléma a fenti felsorolásban nem szerepel.** Kattintson a **Mentés** gombra.

   ![Probléma megadása a kérelemhez](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a lehetséges megoldások feltárásához. Ha szükséges, válassza a **Tovább: Részletek** lehetőséget a folytatáshoz.

1. Adja meg a megadható további információkat, valamint a kapcsolattartási adatait.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Miután ellenőrizte az adatokat, válassza a **Létrehozás** gombot a kérelem létrehozásához.

## <a name="send-us-your-suggestions"></a>Küldje el nekünk javaslatait

Mindig nyitottak vagyunk a visszajelzésekre és a javaslatokra! Küldje el [nekünk javaslatait](https://feedback.azure.com/forums/266794-support-feedback). Ezen kívül, akkor vegyenek részt velünk a [Twitter](https://twitter.com/azuresupport) vagy az [MSDN fórumokon](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Részletek

[Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq)
