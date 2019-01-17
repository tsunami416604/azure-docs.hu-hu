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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: bbe757ccd1d6a37cbcf04f3ecd6dd088ef1ff211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353020"
---
# <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Ez a szakasz ismerteti, hogyan tehet közzé egy új virtuális gép ajánlattal a [Azure Marketplace-en](https://azuremarketplace.microsoft.com). Mind a Windows-alapú és Linux-alapú virtuális gépek, egy operációs rendszer virtuális merevlemezről (VHD) és a nulla vagy több adatokat VHD-ket tartalmazó biztosítunk támogatást. | ![virtuális gép ikonja](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Közzétételi áttekintése

Az alábbi videó [optimalizálása az Azure Marketplace-en ajánlat](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), megadja az alkalmazásukhoz az Azure piactéren, beleértve a felhasználói élmény optimalizálása (egy virtuális gép megoldás használatával), a marketplace-en közzététele a termék oldalát és választható Test Drive felhasználói élményt hogyan felhasználói érdeklődők jönnek létre, és hogyan lehet felhasználni azokat, és optimalizálhatja a customer engagement.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Virtuális gép közzétételi folyamata

A következő ábra szemlélteti a magas szintű lépéseket egy Virtuálisgép-ajánlat közzététele. 

![Virtuális gép közzétételi folyamata](./media/publishvm_001.png)

1. Az ajánlat – minden létrehozásához, és az ajánlat információ van konfigurálva, beleértve a marketing anyagok, a jogi, a támogatási információkat és az eszközintelligencia specifikációk ajánlat leírása.

2. Hozzon létre az üzleti és technikai eszközök – az üzleti eszközök (jogi dokumentumok és marketinganyagokat) és a technikai eszközök (itt, a virtuális gépek és a csatlakoztatott lemezek) kapcsolódó megoldás létrehozásához. 

3. A Termékváltozat létrehozása – hozzon létre a társított termékváltozat(ok) az ajánlathoz kapcsolódó, és küldje el őket.  Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek kíván közzétenni. 
 
4. Igazolom, és tegye közzé az ajánlat – az ajánlat és a technikai eszközök befejezése után az ajánlatot küldhet. Benyújtás elindul a közzétételi folyamat, amelyben a megoldás szolgáltatás tesztelése, érvényesítve, megkapta a minősítést, majd "élesíti" a marketplace-en.  

## <a name="next-steps"></a>További lépések

Mielőtt ezeket a lépéseket, meg kell felelnie a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy virtuális Gépet a Microsoft Azure piactéren való közzétételéhez. 
