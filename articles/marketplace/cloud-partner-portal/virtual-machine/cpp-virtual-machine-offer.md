---
title: Virtuálisgép-ajánlat az Azure piactéren
description: A virtuálisgép-ajánlat Azure Marketplace-en való közzétételének folyamatát ismertető cikk áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142803"
---
# <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Ez a szakasz azt ismerteti, hogyan tehet közzé új virtuálisgép-ajánlatot az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. A támogatást a Windows-alapú és a Linux-alapú virtuális gépek is támogatják, amelyek az operációs rendszer virtuális merevlemezét (VHD) és nulla vagy több adatvhd-t tartalmaznak. | ![virtuális gép ikonja](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Közzététel áttekintése

Az alábbi videó, az [Azure Marketplace-ajánlat optimalizálása](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), átfogó áttekintést nyújt az Azure Marketplace-ről, beleértve a piactéren való közzétételt (virtuálisgép-megoldás használatával), a felhasználói élmény optimalizálását a termék oldalával, valamint az opcionális tesztelési meghajtó élményét, a felhasználói érdeklődők létrehozását és a használatuk módját, valamint optimalizálhatja az ügyfelek részvételét.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>VIRTUÁLIS gépek közzétételi folyamata

A következő ábra a virtuálisgép-ajánlat közzétételének magas szintű lépéseit mutatja be. 

![VM-közzétételi folyamat](./media/publishvm_001.png)

1. Ajánlat létrehozása – a szolgáltatással kapcsolatos összes adat és információ konfigurálva van, beleértve az ajánlat leírását, a marketing-anyagokat, a jogi, a támogatási információkat és az eszközök specifikációit.

2. Üzleti és technikai eszközök létrehozása – a kapcsolódó megoldáshoz (itt, a virtuális gépekhez és a csatlakoztatott lemezekhez) tartozó üzleti eszközök (jogi dokumentumok és marketing-anyagok) és technikai eszközök létrehozása. 

3. Hozza létre az SKU-t – hozza létre az ajánlathoz társított SKU (ka) t, és küldje el azokat.  Minden közzétenni kívánt rendszerképhez egyedi SKU szükséges. 
 
4. Az ajánlat hitelesítése és közzététele – az ajánlat és a technikai eszközök befejezése után elküldheti az ajánlatot. Ez a beküldés elindítja a közzétételi folyamatot, amelyben a megoldás tesztelése, ellenőrzése, minősítése, majd a piactéren folytatott "élő adás" lép életbe.  

## <a name="next-steps"></a>További lépések

Mielőtt figyelembe veszi ezeket a lépéseket, meg kell felelnie a virtuális gép Microsoft Azure Marketplace való közzétételének [technikai és üzleti követelményeinek](./cpp-prerequisites.md) . 
