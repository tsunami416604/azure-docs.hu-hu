---
title: Egy ajánlat/SKU törlése az Azure Marketplace-ről
description: Egy ajánlat/SKU törlése az Azure Marketplace-ről
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5ad106d45c8bae2d41e0bde74b27f80f4d8ab79b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241276"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>Egy ajánlat/SKU törlése az Azure Marketplace-ről 
==========================================

Előfordulhat, hogy valamilyen ok miatt ajánlatát el szeretné távolítani a Piactérről. Új ügyfeleket már nem megvásárlása és üzembe helyezni az ajánlatot, de a meglévő ügyfeleket nem érinti.
Az ajánlat megszüntetése a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés megszüntetését jelenti. Útmutatás és ajánlatok eltávolítását és megszüntetését házirendek vonatkoznak rájuk [a Microsoft Marketplace kiadói szerződésében](https://go.microsoft.com/fwlink/?LinkID=699560) (lásd a szakasz
7) és a [részvételi szabályzatában](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (lásd a szakasz 6.2). A különböző támogatott forgatókönyvek törölje, és a lépéseket ismertetik.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Egy élő Termékváltozat törlése az Azure Marketplace-ről 
----------------------------------------

Azure Marketplace-ről egy élő Termékváltozat törölheti az alábbi lépéseket:

1.  Jelentkezzen be a [Cloud Partner Portalon](http://cloudpartner.azure.com).
2.  Válassza ki az ajánlat az összes ajánlatok lapján.
3.  A képernyő bal oldali panelén válassza a termékváltozatok fülre.
4.  Válassza ki a Termékváltozat törlése, és kattintson a Törlés gombra az adott Termékváltozatot.
5.  Tegye közzé újra az ajánlat az Azure Marketplace-en.

Miután az Azure piactéren az ajánlat élő, a Termékváltozat törlődik a Azure Marketplace-en és az Azure Portalon.

<a name="roll-back-to-a-previous-sku-version"></a>Termékváltozat korábbi verzióinak visszaállítása 
----------------------------------

Azure Marketplace-ről egy élő Termékváltozat jelenlegi verziója a lépéseket követve törölheti. A folyamat befejeződése után a Termékváltozat vissza lesz állítva a korábbi verzióra.

1.  Jelentkezzen be a [Cloud Partner Portalon](http://cloudpartner.azure.com).
2.  Válassza ki az ajánlat az összes ajánlatok lapján.
3.  A képernyő bal oldali panelén válassza a termékváltozatok fülre.
4.  Törölje a csomag legújabb közzétett csomagot a listából.
5.  Tegye közzé újra az ajánlat az Azure Marketplace-en.

Miután az Azure piactéren az ajánlat élő, a listában szereplő SKU jelenlegi verziója törlődik a Azure Marketplace-en és az Azure Portalon.
A Termékváltozat vissza lesz állítva a korábbi verzióra.

<a name="delete-a-live-offer"></a>Egy élő ajánlat törlése 
-------------------

Útmutatást kaphat a támogatási csapat egy élő ajánlat eltávolítása az Azure Marketplace-ről az alábbi lépések végrehajtásával:

1.  Létre egy támogatási jegyet, ez [hivatkozás](https://go.microsoft.com/fwlink/?linkid=844975)
2.  SELECT kezelése lehetővé teszi a probléma írja be a lista, módosítja egy ajánlatot, illetve a Termékváltozat már a kategóriák listája az éles környezetben.
3.  Küldje el a kérelmet.

A támogatási csapat végigvezeti Önt az ajánlat törlése folyamatban.

Egy Termékváltozat ajánlat törlése nem érinti a már elvégzett SKU/ajánlat aktuális vásárlások. Továbbra is működnek, mint korábban, de nem lesz elérhető a jövőben minden új vásárlásokra.
