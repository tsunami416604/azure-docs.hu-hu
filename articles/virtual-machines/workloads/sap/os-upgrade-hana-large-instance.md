---
title: Az SAP Hana Azure (nagy példányok) operációs rendszer frissítése |} Microsoft Docs
description: Hajtsa végre az operációs rendszer frissítése SAP Hana Azure (nagy példány)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti a részleteket az operációs rendszer frissítései nagy HANA-példányokon.

>[!NOTE]
>Az operációs rendszer frissítése az ügyfelek felelőssége, Microsoft támogatása az operations is végigvezeti Önt a legfontosabb területek a figyelje, hogy a frissítés során. A frissítéshez megtervezése előtt konzultáljon, valamint az operációs rendszer gyártója.

A Microsoft műveleti csapata a kiépítés HLI egység időpontjában az operációs rendszer telepítése. Az idő múlásával, fenntartásához szükséges az operációs rendszer (Példa: javítását, hangolása, frissítése stb.) a HLI egységen.

Mielőtt (például egy operációs rendszer frissítése), az operációs rendszer módosításai fő meg **kell** vegye figyelembe a következő kompatibilitási mátrix. Ön **kell** is a Microsoft Operations csapattól tekintse meg az operációs rendszer tevékenységekhez, mint a frissítés megkezdése előtt egy támogatási jegy megnyitásával.

A támogatási mátrix a különböző Linux verzióival SAP HANA verzióiról, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).

A következő kompatibilitási a HLIs tesztelték. Ha HLI kiszolgálóját a kompatibilitási mátrix kívül esik, a Microsoft ügyfélszolgálatához műveletet.

## <a name="for-type-i-class-sku-category"></a>Típus i. osztály SKU kategória

| Konfiguráció | SUSE12 SP1 | SUSE12 SP2 | 7.2 RHEL | 7.3 RHEL|
| --- | --- | --- | --- | --- |
| Server belső vezérlőprogram | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC verzió | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC verzió | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Letiltva | Letiltva | Letiltva | Letiltva |
| Kernel-verzió | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>A típus II osztály SKU kategória

| Konfiguráció | SUSE12 SP1 | SUSE12 SP2 | 7.2 RHEL | 7.3 RHEL|
| --- | --- | --- | --- | --- |
| RMC belső vezérlőprogram-verziója | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC belső vezérlőprogram-verziója | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Szoftververzió Foundation Server (SFS) | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e verzió    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Kernel-verzió    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Ismert problémák

Az alábbiakban néhány gyakori ismert problémákat a frissítés során:
- Termékváltozat-típus II osztály Termékváltozat a szoftver foundation (SFS) a rendszer eltávolítja az operációs rendszer frissítése után. Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis SFS.
- Ethernet-illesztők (ENIC és FNIC) visszaállítva a régebbi verziót. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verziója.

## <a name="next-steps"></a>További lépések
- Tekintse meg a [biztonsági mentési és visszaállítási](hana-overview-high-availability-disaster-recovery.md) os biztonsági mentési i. típusú SKU osztály.
- Tekintse meg a [az operációs rendszer biztonsági másolat típusa II termékváltozatok](os-backup-type-ii-skus.md) típus II SKU osztály.