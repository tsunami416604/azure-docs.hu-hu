---
title: Virtuálisgép-ajánlat az Azure piactéren
description: A virtuálisgép-ajánlat Azure Marketplace-en való közzétételének folyamatát ismertető cikk áttekintése.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 0313c4f2581615e1d1a3a57fc191db7d7cc6e825
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808560"
---
# <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Ez a szakasz azt ismerteti, hogyan tehet közzé új virtuálisgép-ajánlatot az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. A támogatást a Windows-alapú és a Linux-alapú virtuális gépek is támogatják, amelyek az operációs rendszer virtuális merevlemezét (VHD) és nulla vagy több adatvhd-t tartalmaznak. | ![virtuális gép ikonja](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Közzététel áttekintése

Az alábbi videó, az [Azure Marketplace-ajánlat optimalizálása](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), átfogó áttekintést nyújt az Azure Marketplace-ről, beleértve a piactéren való közzétételt (virtuálisgép-megoldás használatával), a felhasználói élmény optimalizálását a termék oldalán és az opcionális tesztelési meghajtó, a felhasználói érdeklődők generálása és a használatuk módja, valamint az ügyfelek bevonásának optimalizálása.

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
