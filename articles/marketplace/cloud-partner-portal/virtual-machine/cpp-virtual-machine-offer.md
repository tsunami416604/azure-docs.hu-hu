---
title: Az Azure piactéren virtuálisgép-ajánlat |} A Microsoft Docs
description: Az Azure piactér-Virtuálisgép-ajánlat közzétételéhez folyamatának áttekintése.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639777"
---
# <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

Ez a szakasz ismerteti a közzététele egy virtuális gép (VM) elemét, és célja az, a közzétevő számára készült útmutató a [Azure Marketplace-en](https://azuremarketplace.microsoft.com).  A szempontból azt a következő fő részre oszlik:

- [Előfeltételek](./cpp-prerequisites.md) – műszaki és üzleti követelményeit létrehozása vagy egy Virtuálisgép-ajánlat közzététele előtt
- [Hozzon létre Virtuálisgép-ajánlat](./cpp-create-offer.md) -listák az új virtuális gép létrehozásához szükséges lépéseket kínálnak bejegyzés használatával a [Cloud Partner portálra](https://cloudpartner.azure.com)
- [Hozzon létre a virtuális gép technikai eszközök](./cpp-create-technical-assets.md) – a műszaki létrehozása eszközök egy Virtuálisgép-megoldás és a egy virtuális Gépet, a csomag konfigurálása az Azure Marketplace-en ajánlat
- [Virtuálisgép-ajánlat közzététele](./cpp-publish-offer.md) – az ajánlat közzététele az Azure piactér bejelentése


## <a name="vm-publishing-process-flow"></a>Virtuális gép közzétételi folyamata

A következő ábra szemlélteti a magas szintű lépéseket egy Virtuálisgép-ajánlat közzététele. 

![Virtuális gép közzétételi folyamata](./media/publishvm_001.png)

1. Az ajánlat – minden létrehozásához, és az ajánlat információ van konfigurálva, beleértve a marketing anyagok, a jogi, a támogatási információkat és az eszközintelligencia specifikációk ajánlat leírása.

2. Hozzon létre az üzleti és technikai eszközök – az üzleti eszközök (jogi dokumentumok és marketinganyagokat) és a technikai eszközök (itt, a virtuális gépek és a csatlakoztatott lemezek) kapcsolódó megoldás létrehozásához. 

3. A Termékváltozat létrehozása – hozzon létre a társított termékváltozat(ok) az ajánlathoz kapcsolódó, és küldje el őket.  Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek kíván közzétenni. 
 
4. Igazolom, és tegye közzé az ajánlat – az ajánlat és a technikai eszközök befejezése után az ajánlatot küldhet. Benyújtás elindul a közzétételi folyamat, amelyben a megoldás szolgáltatás tesztelése, érvényesítve, megkapta a minősítést, majd "élesíti" a marketplace-en.  

## <a name="next-steps"></a>További lépések

Mielőtt ezeket a lépéseket, meg kell felelnie a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy virtuális Gépet a Microsoft Azure piactéren való közzétételéhez. 
