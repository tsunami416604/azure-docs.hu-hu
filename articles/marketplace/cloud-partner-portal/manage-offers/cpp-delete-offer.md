---
title: Törölje a Marketplace-ajánlatok – az Azure Marketplace-en |} A Microsoft Docs
description: Az Azure és az AppSource-piactér használatával a Cloud Partner portálra szóló ajánlatok törlése
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 1d5d02d65dd3dcf5978639818fba4ebe36ffaaff
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540006"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace és AppSource ajánlatok és termékváltozatok törlése

Különböző okok miatt dönthet úgy, hogy visszavonja az ajánlatot a Microsoft Marketplace, amely két formáját is igénybe vehet.:

- *Ajánlat eltávolítása* biztosítja, hogy már nem lehet-e vásárolhat új ügyfeleket, vagy üzembe helyezni az ajánlatot, azonban nem érinti a meglévő ügyfelekre, akik támogatnia kell a licencszerződést, és a vonatkozó törvényeknek megfelelően. 
- *Ajánlat megszüntetése* azt a folyamatot, a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés. 

Útmutatás és ajánlatok eltávolítását és megszüntetését házirendek vonatkoznak rájuk [a Microsoft Marketplace kiadói szerződésében](https://go.microsoft.com/fwlink/?LinkID=699560) és a [részvételi szabályzatában](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (szakasz [ajánlat felfüggesztése és eltávolítása](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Ez a cikk előadások információ a különböző támogatott törlés forgatókönyvek és az egyes végrehajtásához szükséges lépéseket.  

> [!NOTE]
> Ajánlat, amely nem lett közzétéve egyszerűen kiválasztásával törölheti a **törlése** gomb az eszköztáron, a **szerkesztő** fülre.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Egy közzétett Termékváltozat törlése az Azure Marketplace-ről

Az alábbi lépéseket követve az Azure Marketplace-en közzétett Termékváltozat törölheti:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2.  Az a **minden ajánlat** lapra, jelölje be az ajánlat.  Az ajánlat kell megjelennie a **szerkesztő** fülre.
3.  A bal oldali eszköztáron válassza ki a **termékváltozatok** fülre. 
4.  Válassza ki a törölni, és kattintson a kívánt Termékváltozatát a **törlése** gombra.
5.  [Tegye közzé újra](./cpp-publish-offer.md) az ajánlat az Azure Marketplace-en.

A módosított ajánlat az Azure piactéren történő közzététele után a kiválasztott Termékváltozat már nem jelennek az Azure Marketplace-en és az Azure Portalon.


## <a name="roll-back-to-a-previous-sku-version"></a>Termékváltozat korábbi verzióinak visszaállítása

Azure Marketplace-ről itt lépéseket követve törölheti közzétett Termékváltozat jelenlegi verziója. A folyamat befejeződése után a Termékváltozat előző verziójának vissza lesz állítva.

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. Az a **minden ajánlat** lapra, jelölje be az ajánlat.  Az ajánlat kell megjelennie a **szerkesztő** fülre.
3. A bal oldali eszköztáron válassza ki a **termékváltozatok** fülre. 
4. Törölje a társított megoldás eszköz a legújabb verzióra a lemez-verziók listáját.  Függően az ajánlat típusát, ez a mező lehet **lemez verziója**, **alkalmazáscsomag-verziók**, vagy hasonló adategység. 
5. [Tegye közzé újra](./cpp-publish-offer.md) az ajánlat az Azure Marketplace-en.

A módosított ajánlatot a piactéren theAzure közzététele után a listában szereplő SKU jelenlegi verziója már nem jelennek meg. az Azure Marketplace-en és az Azure Portalon.  A Termékváltozat előző verziójának vissza lesz állítva.


## <a name="delete-a-live-offer"></a>Egy élő ajánlat törlése

Nincsenek különböző eljárási, üzleti és jogi szempontból az élő ajánlat eltávolítása. Kövesse az útmutatást kaphat a támogatási csapat egy élő ajánlat eltávolítása az Azure Marketplace-ről az alábbi lépéseket:

1.  Egy támogatási jegyet a előléptetése a [incidens létrehozása](https://go.microsoft.com/fwlink/?linkid=844975) oldalon, vagy ikonjára kattintva **támogatja** jobb felső sarkában a [Cloud Partner portálra](https://cloudpartner.azure.com/).

2.  Válassza ki az adott ajánlat típusát a a **Problem type** listájára és válassza ki **távolítsa el a közzétett ajánlat** a a **kategória** listája.

3.  Küldje el a kérelmet.

A támogatási csapat végigvezeti az ajánlat törlése.

> [!NOTE]
> Egy ajánlat (vagy a Termékváltozat) törlése nem érinti a jelenlegi vásárlások az adott ajánlat (vagy Termékváltozat). Ezek a vásárlások továbbra is működnek, mint korábban. Azonban a törölt ajánlatok és termékváltozatok nem érhető el minden jövőbeli vásárlásokra.


## <a name="next-steps"></a>További lépések

Az ajánlatok kezelésére szolgáló alapvető műveleteit megismerése után készen áll hozzon létre egy példányt egy Microsoft [Piactéri ajánlat](../cpp-marketplace-offers.md).
