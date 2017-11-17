---
title: "Termékváltozat-sorozat nem érhető el |} Microsoft Docs"
description: "Néhány SKU adatsorozat nem állnak rendelkezésre a kiválasztott előfizetés ehhez a régióhoz."
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>A régióban vagy Termékváltozat nem érhető el
Ez a cikk ismerteti, hogyan szeretné megoldani az Azure-előfizetés nem rendelkezik olyan régióban vagy egy virtuális gép SKU elérésére.

## <a name="symptoms"></a>Probléma

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Amikor egy virtuális gép telepítését az alábbi hibaüzenetek valamelyike kapja:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Fenntartott virtuálisgép-példányok beszerzésekor jelenhet meg az alábbi hibaüzenetek valamelyike:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Számítási core kvóta növeléséhez támogatási kérelem létrehozásakor egy régiót vagy SKU-család nincs kijelölésnél érhetők el.

## <a name="solution"></a>Megoldás
Először javasoljuk, hogy érdemes-e egy másik régiót vagy az üzleti igényeinek megfelelő Termékváltozat. Ha még nem található megfelelő régiót vagy SKU, hozzon létre egy "előfizetés kezelése" [támogatási kérelem](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a következő lépésekkel:


- Az alapismeretek lapon válassza az "Előfizetés kezelése" típusú a probléma, válassza ki az előfizetést, és kattintson a "Tovább gombra".

![Alapvető beállítások panel](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   A hiba lapon válassza ki a problémát, "Egyéb általános kérdések".
- A Részletek területen:
  - Kérjük, jelezze, ha a keresett virtuális gépek telepítéséhez, vagy a vételi fenntartott virtuálisgép-példányok
  - Adja meg a régió, SKU, és szeretne telepíteni, vagy a vételi virtuálisgép-példányok száma


![Probléma](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Adja meg a kapcsolattartási adatait, és kattintson a "Létrehozás" gombra.

![Kapcsolattartási adatok](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Visszajelzés
Azt mindig nyitva a visszajelzések és tanácsok! Küldjön nekünk a [javaslatok](https://feedback.azure.com/forums/266794-support-feedback). Emellett képes betartásának vizsgálatára keresztül megszólítása [Twitter](https://twitter.com/azuresupport) vagy a [MSDN fórumain](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Részletek
[Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq)

