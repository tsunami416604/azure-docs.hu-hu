---
title: Piactéri ajánlatok törlése | Azure Piactér
description: Ajánlatok törlése az Azure- és AppSource-piactereken a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286455"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Az Azure Marketplace- és AppSource-ajánlatok vagy -sUK törlése

Különböző okok miatt dönthet úgy, hogy visszavonja az ajánlatot a Microsoft piacteréről, amely két formát ölthet:

- *Az ajánlat eltávolítása* biztosítja, hogy az új ügyfelek többé ne vásárolhassák meg vagy telepíthessék az ajánlatot, de nincs hatással a meglévő ügyfelekre, akiket a licencszerződés nek és a vonatkozó törvényeknek megfelelően kell támogatnia. 
- *Az ajánlat megszüntetése* az Ön és meglévő ügyfelei közötti szolgáltatási és/vagy licencszerződés megszüntetésének folyamata. 

Az ajánlatok eltávolításával és megszüntetésével kapcsolatos útmutatásokra és irányelvekre a [Microsoft Marketplace kiadói szerződése](https://go.microsoft.com/fwlink/?LinkID=699560) és a Részvételi [irányelvek](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) [(Az ajánlat felfüggesztése és eltávolítása](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)) vonatkoznak. 

Ez a cikk a különböző támogatott törlési forgatókönyvekről és az egyes műveletek végrehajtásához szükséges lépésekről szól.  

> [!NOTE]
> A még közzé nem tett ajánlat törléséhez egyszerűen jelölje ki a **Törlés** gombot a **Szerkesztő** lap eszköztárán.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Közzétett termékváltozat törlése az Azure Piactérről

A közzétett termékváltozatokat az alábbi lépésekkel törölheti az Azure Piactérről:

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2.  A **Minden ajánlat** lapon válassza ki az ajánlatot.  Az ajánlatnak meg kell jelennie a **Szerkesztő** lapon.
3.  A bal oldali eszköztáron válassza a **SK-k** lapot. 
4.  Jelölje ki a törölni kívánt termékváltozatot, és kattintson a **Törlés** gombra.
5.  [Tegye közzé újra](./cpp-publish-offer.md) az ajánlatot az Azure Marketplace-en.

Miután a módosított ajánlat közzé került az Azure Marketplace-en, a kiválasztott termékváltozat már nem jelenik meg az Azure Marketplace-en és az Azure Portalon.


## <a name="roll-back-to-a-previous-sku-version"></a>Visszaállítás egy korábbi Termékváltozat-verzióra

A közzétett termékváltozat aktuális verzióját törölheti az Azure Piactérről az itt leírt lépések segítségével. Amikor a folyamat befejeződött, a termékváltozat visszaáll az előző verzióra.

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat** lapon válassza ki az ajánlatot.  Az ajánlatnak meg kell jelennie a **Szerkesztő** lapon.
3. A bal oldali eszköztáron válassza a **SK-k** lapot. 
4. Törölje a társított megoldáseszköz legújabb verzióját a lemezverziók listájából.  Az ajánlat típusától függően ez a mező lehet **Lemezverzió,** **Csomagverzió**vagy hasonló eszköz. 
5. [Tegye közzé újra](./cpp-publish-offer.md) az ajánlatot az Azure Marketplace-en.

Miután a módosított ajánlat közzé került az Azure Marketplace-en, a felsorolt termékváltozat aktuális verziója már nem jelenik meg. az Azure Marketplace-en és az Azure Portalon.  A termékváltozat visszaáll az előző verzióra.


## <a name="delete-a-live-offer"></a>Élő ajánlat törlése

Vannak különböző eljárási, üzleti, és jogi szempontok eltávolítása élő ajánlatot. Az alábbi lépésekkel útmutatást kaphat a támogatási csapattól az Azure Piactérről való élő ajánlat eltávolításához:

1.  Emelje fel a támogatási jegyet az [Incidens létrehozása](https://go.microsoft.com/fwlink/?linkid=844975) lapon, vagy kattintson a **Támogatás** elemre a [Cloud Partner Portal](https://cloudpartner.azure.com/)jobb felső sarkában.

2.  Válassza ki az adott ajánlat típusát a **Probléma típus** listájában, és válassza a Közzétett ajánlat **eltávolítása lehetőséget** a **Kategória** listában.

3.  Küldje el a kérelmet.

A támogatási csoport végigvezeti az ajánlat törlési folyamatán.

> [!NOTE]
> Egy ajánlat (vagy Termékváltozat) törlése nincs hatással az adott ajánlat (vagy Termékváltozat) aktuális vásárlásaira. Ezek a vásárlások továbbra is a munka, mint korábban. A törölt ajánlatok vagy sk-ek azonban nem lesznek elérhetők a jövőbeli vásárlásokhoz.


## <a name="next-steps"></a>További lépések

Miután ismeri az ajánlatok kezeléséhez használt alapvető műveleteket, készen áll a Microsoft [piactér-ajánlat egy példányának létrehozására.](../cpp-marketplace-offers.md)
