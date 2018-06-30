---
title: Az SAP Hana Azure (nagy példányok) operációs rendszer frissítése |} Microsoft Docs
description: Hajtsa végre az operációs rendszer frissítése SAP Hana Azure (nagy példány)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114592"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti a részleteket az operációs rendszer frissítései nagy HANA-példányokon.

>[!NOTE]
>Az operációs rendszer frissítése az ügyfelek felelőssége, Microsoft támogatása az operations is végigvezeti Önt a legfontosabb területek a figyelje, hogy a frissítés során. A frissítéshez megtervezése előtt konzultáljon, valamint az operációs rendszer gyártója.

A Microsoft műveleti csapata a kiépítés HLI egység időpontjában az operációs rendszer telepítése. Az idő múlásával, fenntartásához szükséges az operációs rendszer (Példa: javítását, hangolása, frissítése stb.) a HLI egységen.

Előtt az operációs rendszer (például frissítési SP1 SP2) módosításai nagyobb, forduljon a támogatási jegy megnyitásával kell forduljon a Microsoft Operations csoporthoz.


A támogatási mátrix a különböző Linux verzióival SAP HANA verzióiról, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Ismert problémák

Az alábbiakban néhány gyakori ismert problémákat a frissítés során:
- Termékváltozat-típus II osztály Termékváltozat a szoftver foundation (SFS) a rendszer eltávolítja az operációs rendszer frissítése után. Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis SFS.
- Ethernet-illesztők (ENIC és FNIC) visszaállítva a régebbi verziót. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verziója.

## <a name="next-steps"></a>További lépések
- Tekintse meg a [biztonsági mentési és visszaállítási](hana-overview-high-availability-disaster-recovery.md) os biztonsági mentési i. típusú SKU osztály.
- Tekintse meg a [az operációs rendszer biztonsági másolat típusa II termékváltozatok](os-backup-type-ii-skus.md) típus II SKU osztály.