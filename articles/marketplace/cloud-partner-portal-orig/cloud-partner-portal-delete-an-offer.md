---
title: Az ajánlat vagy Termékváltozat törlése az Azure Marketplace-ről |} A Microsoft Docs
description: Az ajánlat vagy Termékváltozat törlésére vonatkozó lépéseket.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809825"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Az ajánlat vagy Termékváltozat törlése az Azure Marketplace-ről
==========================================

Előfordulhat, hogy valamilyen ok miatt ajánlatát el szeretné távolítani a Piactérről. Az ajánlat eltávolítása azt jelenti, hogy az ügyfelek nem tudják többé megvásárolni és üzembe helyezni az ajánlatot, ez azonban nem érinti a már meglévő ügyfeleket.
Az ajánlat megszüntetése a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés megszüntetését jelenti. Útmutatás és ajánlatok eltávolítását és megszüntetését házirendek vonatkoznak rájuk [a Microsoft Marketplace kiadói szerződésében](http://go.microsoft.com/fwlink/?LinkID=699560) (lásd a szakasz
7) és a [részvételi szabályzatában](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (lásd a szakasz 6.2). Ez a cikk előadások információ a különböző támogatott forgatókönyvek és a lépéseket, amelyeket számukra törlése.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Egy élő Termékváltozat törlése az Azure Marketplace-ről
----------------------------------------

Az alábbi lépéseket követve az Azure Marketplace-ről egy élő Termékváltozat törölheti:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  Válassza ki az ajánlattal a **minden ajánlat** fülre.

3.  A képernyő bal oldali panelén válassza a **termékváltozatok** fülre.

4.  Válassza ki a Termékváltozat, amelyet szeretne törölni, és kattintson a Törlés gombra az adott Termékváltozatot.

5.  [Tegye közzé újra](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) az ajánlat az Azure Marketplace-en.

Miután az Azure piactéren az ajánlat élő, a Termékváltozat törlődik a Azure Marketplace-en és az Azure Portalon.

<a name="roll-back-to-a-previous-sku-version"></a>Termékváltozat korábbi verzióinak visszaállítása
----------------------------------

Azure Marketplace-ről egy élő Termékváltozat jelenlegi verziója a lépéseket követve törölheti. A folyamat befejeződése után a Termékváltozat vissza lesz állítva a korábbi verzióra.

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  Válassza ki az ajánlattal a **minden ajánlat** fülre.

3.  A képernyő bal oldali panelén válassza a **termékváltozatok** fülre.

4.  Törölje a legújabb **lemez verziója** közzétett lemez verziók listájában.

5.  [Tegye közzé újra](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) az ajánlat az Azure Marketplace-en.

Miután az Azure piactéren az ajánlat élő, a listában szereplő SKU jelenlegi verziója törlődik a Azure Marketplace-en és az Azure Portalon.
A Termékváltozat vissza lesz állítva a korábbi verzióra.

<a name="delete-a-live-offer"></a>Egy élő ajánlat törlése
-------------------

Számos különféle szempontok igénylő kell elvégzi, egy kérelem egy élő ajánlat eltávolítása esetén. Útmutatást kaphat a támogatási csapat egy élő ajánlat eltávolítása az Azure Marketplace-ről az alábbi lépések végrehajtásával:

1.  Létre egy támogatási jegyet, ez [hivatkozás](https://go.microsoft.com/fwlink/?linkid=844975), vagy kattintson a támogatás a CLoud Partner Portal jobb felső sarkában.

2.  Válassza ki az adott ajánlat típusát a a **Problem type** lista és **távolítsa el a közzétett ajánlat** a a **kategória** listája.

3.  Küldje el a kérelmet.

A támogatási csapat végigvezeti Önt az ajánlat törlése folyamatban.

>[!NOTE]
>Termékváltozat/ajánlat törlése nem érinti az ajánlat/SKU aktuális vásárolt. Az SKU-k/ajánlatokat továbbra is működnek, mint korábban. Azonban ezek nem lesznek elérhetők a jövőben minden új vásárlásokra.
