---
title: Piactér-ajánlatok törlése | Azure piactér
description: Az Azure-és AppSource-piactéren elérhető ajánlatok törlése a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80286455"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace-és AppSource-ajánlatok vagy SKU-i törlése

Számos okból dönthet úgy, hogy visszavonja az ajánlatát a Microsoft Marketplace-ről, amely két űrlapot vehet igénybe:

- Az *ajánlat eltávolításával* biztosítható, hogy az új ügyfelek ne tudják megvásárolni vagy üzembe helyezni az ajánlatot, de nincs hatással a meglévő ügyfelekre, akiket a licencszerződés és a vonatkozó törvények értelmében támogatni kell. 
- Az *ajánlat lezárása* a szolgáltatás és/vagy az Ön és a meglévő ügyfelek közötti licencszerződés leállításának folyamata. 

Az ajánlat eltávolításához és megszüntetéséhez kapcsolódó útmutatást és szabályzatokat [Microsoft Marketplace kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560) és a [részvételi szabályzatok](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (a [felfüggesztést és az eltávolítást biztosító](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)szakasz) szabályozza. 

Ez a cikk a különböző támogatott törlési forgatókönyvekről és az egyes műveletek végrehajtásához szükséges lépésekről beszél.  

> [!NOTE]
> Törölhet egy olyan ajánlatot, amely még nem lett közzétéve, egyszerűen kattintson a **delete (Törlés** ) gombra a **szerkesztő** lap eszköztárán.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Közzétett SKU törlése az Azure Marketplace-ről

A közzétett SKU-t az alábbi lépésekkel törölheti az Azure Marketplace-ről:

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2.  A **minden ajánlat** lapon válassza ki az ajánlatát.  Az ajánlatnak a **szerkesztő** lapon kell megjelennie.
3.  A bal oldali eszköztáron válassza a **SKUs** fület. 
4.  Válassza ki a törölni kívánt SKU-t, majd kattintson a **Törlés** gombra.
5.  [Tegye közzé](./cpp-publish-offer.md) újra az ajánlatot az Azure Marketplace-en.

Miután közzétette a módosított ajánlatot az Azure piactéren, a kiválasztott SKU-t már nem fogja felvenni az Azure Marketplace-en és Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Visszalépés egy korábbi SKU-verzióra

A közzétett SKU aktuális verzióját az Azure Marketplace-en törölheti az itt ismertetett lépések segítségével. A folyamat befejezésekor a rendszer visszaállítja az SKU-t az előző verzióra.

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat** lapon válassza ki az ajánlatát.  Az ajánlatnak a **szerkesztő** lapon kell megjelennie.
3. A bal oldali eszköztáron válassza a **SKUs** fület. 
4. Törölje a társított megoldási eszköz legújabb verzióját a lemez verzióinak listájáról.  Az ajánlat típusától függően ez a mező lehet a **lemez verziója**, a **csomag verziója**vagy hasonló eszköz. 
5. [Tegye közzé](./cpp-publish-offer.md) újra az ajánlatot az Azure Marketplace-en.

Miután közzétette a módosított ajánlatot a theAzure piactéren, a felsorolt SKU aktuális verziója többé nem jelenik meg. Az Azure Marketplace-en és a Azure Portal.  Az SKU visszaáll az előző verzióra.


## <a name="delete-a-live-offer"></a>Élő ajánlat törlése

Az élő ajánlatok eltávolításának számos eljárási, üzleti és jogi aspektusa van. Az alábbi lépések végrehajtásával útmutatást kaphat a támogatási csapattól az Azure piactéren elérhető élő ajánlatok eltávolításához:

1.  [Hozzon létre](https://go.microsoft.com/fwlink/?linkid=844975) egy támogatási jegyet az incidens létrehozása lap használatával vagy a [Cloud Partner Portal](https://cloudpartner.azure.com/)jobb felső sarkában található **támogatás** elemre kattintva.

2.  Válassza ki az adott ajánlat típusát a **probléma típusa** listán, és válassza a **közzétett ajánlat eltávolítása** lehetőséget a **Kategória** listában.

3.  Küldje el a kérelmet.

A támogatási csapat végigvezeti Önt az ajánlat törlési folyamatán.

> [!NOTE]
> Egy ajánlat (vagy SKU) törlése nem befolyásolja az adott ajánlat (vagy SKU) aktuális beszerzését. Ezek a vásárlások továbbra is ugyanúgy működnek, mint korábban. A törölt ajánlatokat vagy SKU-ket azonban nem lehet elérhetővé tennie a jövőbeli vásárlásokhoz.


## <a name="next-steps"></a>További lépések

Miután megismerte az ajánlatok kezeléséhez használt alapszintű műveleteket, készen áll egy Microsoft [Marketplace-ajánlat](../cpp-marketplace-offers.md)példányának létrehozására.
