---
title: Az SKU-sorozat nem érhető el | Microsoft Docs
description: Egyes SKU-sorozatok nem érhetők el a kiválasztott előfizetéshez ehhez a régióhoz.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896726"
---
# <a name="region-or-sku-unavailable"></a>Nem elérhető régió vagy termékváltozat
Ez a cikk azt ismerteti, hogyan oldható fel egy olyan Azure-előfizetés problémája, amely nem fér hozzá egy adott régióhoz vagy virtuális géphez tartozó SKU-hoz.

## <a name="symptoms"></a>Probléma

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Virtuális gép telepítésekor a következő hibaüzenetek egyike jelenik meg:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Fenntartott virtuálisgép-példányok vásárlása esetén a következő hibaüzenetek egyike jelenik meg:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Ha támogatási kérést hoz létre a számítási alapkvóta növeléséhez, akkor a régió vagy SKU-család nem érhető el a kiválasztáshoz.

## <a name="solution"></a>Megoldás
Először azt javasoljuk, hogy vegye fontolóra egy olyan alternatív régiót vagy SKU-t, amely megfelel az Ön üzleti igényeinek. Ha nem talál megfelelő régiót vagy SKU-t, hozzon létre egy "előfizetés-kezelési" [támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az alábbi lépésekkel:


- Az alapok lapon válassza a probléma típusa "előfizetés-kezelés" lehetőséget, válassza ki az előfizetést, és kattintson a "tovább" gombra.

![Alapvető beállítások panel](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   A probléma lapon válassza a probléma típusát "egyéb általános kérdések" elemre.
- A Részletek szakaszban:
  - Kérjük, jelezze, ha virtuális gépeket szeretne telepíteni vagy fenntartott virtuálisgép-példányokat kíván vásárolni
  - Adja meg a telepítendő vagy megvásárolni kívánt virtuálisgép-példányok régióját, SKU-jának számát.


![Probléma](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Adja meg a kapcsolattartási adatait, és kattintson a "létrehozás" gombra.

![Kapcsolattartási adatok](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Visszajelzés
Mindig nyitottak vagyunk visszajelzésre és javaslatokra! Küldje el nekünk [javaslatait](https://feedback.azure.com/forums/266794-support-feedback). Emellett a [Twitteren](https://twitter.com/azuresupport) vagy az [MSDN-fórumokon](https://social.msdn.microsoft.com/Forums/azure)is elvégezheti a kapcsolatfelvételt.

## <a name="learn-more"></a>További információk
[Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq)

