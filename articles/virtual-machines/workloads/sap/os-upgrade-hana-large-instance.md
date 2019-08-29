---
title: Az Azure-beli SAP HANA operációs rendszerének frissítése (nagyméretű példányok) | Microsoft Docs
description: Operációs rendszer verziófrissítésének elvégzése SAP HANA Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098684"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti az operációs rendszer frissítésének részleteit a HANA nagyméretű példányain.

>[!NOTE]
>Az operációs rendszer frissítése az ügyfelek felelőssége, a Microsoft operatív támogatási szolgálata útmutatást nyújt a főbb területekhez, amelyekkel megtekintheti a frissítést. A frissítés megtervezése előtt tekintse meg az operációs rendszer gyártóját is.

A HLI egység kiépítés időpontjában a Microsoft Operations csapat telepíti az operációs rendszert. Az idő múlásával meg kell őriznie az operációs rendszert (például: Javítások, hangolás, verziófrissítés stb.) a HLI egységen.

Mielőtt megkezdené az operációs rendszer jelentős módosításait (például a SP1 verzióról SP2-re), forduljon a Microsoft Operations csapathoz egy támogatási jegy megnyitásával.

Belefoglalás a jegybe:

* A HLI-előfizetés azonosítója.
* A kiszolgáló neve.
* Az alkalmazni kívánt javítási szint.
* A módosítás megtervezésének dátuma. 

Javasoljuk, hogy ezt a jegyet legalább egy héttel a kívánt frissítési dátum előtt nyissa meg, mert a műveleti csapat ellenőrzi, hogy szükség van-e belső vezérlőprogram-frissítésre a kiszolgáló paneljén.


A különböző Linux-verziókkal rendelkező különböző SAP HANA verziók támogatási mátrixa: [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Ismert problémák

A frissítés során néhány gyakori ismert probléma a következő:
- A II. típusú SKU-ban az operációs rendszer frissítése után törlődnek a szoftver Foundation szoftverei (a strukturális alapok). Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis strukturális rendszereket.
- Az Ethernet-kártya illesztőprogramjai (ENIC és FNIC) visszaállnak a régebbi verzióra. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verzióját.

## <a name="next-steps"></a>További lépések
- Tekintse át a [biztonsági mentést és](hana-overview-high-availability-disaster-recovery.md) a visszaállítást az operációs rendszer biztonsági mentése I SKU osztályban.
- Tekintse át az [operációs rendszer biztonsági másolatát](os-backup-type-ii-skus.md) , amely a 2. típusú SKU osztályhoz tartozó 3. típusú bélyegzőket használja.
