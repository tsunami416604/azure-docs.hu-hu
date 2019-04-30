---
title: Termékváltozat-sorozatok nem érhető el |} A Microsoft Docs
description: Egyes Termékváltozat-sorozatok nem érhetők el a megadott előfizetés esetében ebben a régióban.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649109"
---
# <a name="region-or-sku-unavailable"></a>Régió vagy Termékváltozat nem érhető el
Ez a cikk ismerteti, hogyan lehet megoldani a problémát egy régió vagy Virtuálisgép-Termékváltozat nem férhető Azure-előfizetés.

## <a name="symptoms"></a>Probléma

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Virtuális gépek telepítésekor a következő hibaüzeneteket egyikét kapja:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Ha a Reserved Virtual Machine Instances szolgáltatást, a következő hibaüzeneteket egyikét kapja:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Amikor hoz létre egy támogatási kérést a számítási magkvóta növeléséhez, régió vagy Termékváltozat-család nem érhető lehet kiválasztani.

## <a name="solution"></a>Megoldás
Először javasoljuk, hogy érdemes-e egy másik régióba vagy az üzleti igényeinek megfelelő Termékváltozatot. Ha nem találja a megfelelő régióban vagy Termékváltozat, hozzon létre egy "előfizetés-kezelési" [támogatási kérelem](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a következő lépésekkel:


- Az alapismeretek lapon jelölje be "Előfizetés-kezelés" Issue type, válassza ki az előfizetést, és kattintson a "Tovább gombra".

![Alapvető beállítások panel](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   A probléma az oldalon a probléma típusaként válassza "Egyéb általános kérdések".
- A Részletek területen:
  - Jelezze, ha a virtuális gépek üzembe helyezése vagy vásárlása a Reserved Virtual Machine Instances találatokat
  - Adja meg a régiót, Termékváltozat és szeretné telepíteni, vagy vásároljon virtuálisgép-példányok száma


![Probléma](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Adja meg a kapcsolattartási adatait, és kattintson a "Létrehozás" gombra.

![Kapcsolattartási adatok](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Visszajelzés
Mindig tudjuk nyissa meg a vélemények és tanácsok! Küldjön nekünk a [javaslatok](https://feedback.azure.com/forums/266794-support-feedback). Emellett akkor is kapcsolatba léphet a velünk [Twitter](https://twitter.com/azuresupport) vagy a [MSDN-fórumok](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Részletek
[Az Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq)

