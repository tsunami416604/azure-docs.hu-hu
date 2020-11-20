---
title: Red Hat Enterprise Linux – teljes életciklusra kiterjesztett támogatás
description: További információ a Red Hat Enterprise Extended Lifecycle támogatásának hozzáadásáról
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 71ff00dbf32ef32ead85be60cdbe53f23c8e35c7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957571"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) bővített életciklus-támogatás
Ez a cikk a Red Hat Enterprise-lemezképek kiterjesztett életciklus-támogatásával kapcsolatos információkat tartalmaz:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 életciklus
A 2020. november 30-án kezdődően a Red Hat Enterprise Linux 6 a karbantartási fázis végére ér. A karbantartási fázist a hosszabb élettartam fázisa követi. Mivel Red Hat Enterprise Linux 6 átmenet a teljes/karbantartási fázisokból, erősen ajánlott Red Hat Enterprise Linux 7 vagy 8 verzióra frissíteni. Ha az ügyfeleknek a Red Hat Enterprise Linux 6 címen kell maradniük, javasoljuk, hogy adja hozzá a Red Hat Enterprise Linux Extended életciklus-támogatás (ELS) bővítményt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6 rendszert futtatok, és jelenleg nem lehet áttérni egy újabb verzióra. Milyen lehetőségek vannak?
* Folytassa a Red Hat Enterprise Linux 6 futtatásával, és vásárolja meg a kiterjesztett életciklus-támogatás (ELS) Add-On adattárakat, hogy továbbra is korlátozott szoftveres karbantartási és technikai támogatást kapjon (lásd az alábbi lépéseket: a frissítés és a díjszabás részletei).
* A lehető leghamarabb telepítse át Red Hat Enterprise Linux 7 vagy 8 rendszerre.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Mi a kiegészítő díj a Red Hat Enterprise Linux kiterjesztett életciklus-támogatás (ELS) bővítmény használatához?

|Virtuális gép mérete|További díjszabási idő kerete|További dollár összeg (USD)| Megjegyzések|
|---|---|---|---|
| Kis virtuális vendég (<= 4 mag) | Óránkénti kiegészítő díj | TBC | |
|  | Havi kiegészítő díj | TBC | Fenntartott példányok esetén |
|  | Éves kiegészítő díj | TBC | Fenntartott példányok esetén |
| Nagyméretű virtuális vendég (>4 mag) | Óránkénti kiegészítő díj | TBC | |
|  | Havi kiegészítő díj | TBC | Fenntartott példányok esetén |
|  | Éves kiegészítő díj | TBC | Fenntartott példányok esetén |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Mi a folyamat a kiterjesztett életciklus-támogatási (ELS) Tárházak hozzáadásához, hogy továbbra is megkapja a szoftveres karbantartást (a hibák és a biztonsági javítások), valamint a Red Hat Enterprise Linux 6 támogatását?

Az ELS-ra való regisztráció befejezését követően a befejezési folyamat hamarosan elérhető lesz (legkésőbb 2020 november 30-ig).

## <a name="next-steps"></a>Következő lépések

* Az Azure-ban elérhető RHEL-lemezképek teljes listájának megtekintéséhez lásd: [Red Hat Enterprise Linux (RHEL) lemezképek az Azure-ban](./redhat-imagelist.md).
* További információ az Azure Red Hat frissítési infrastruktúráról: [Red Hat frissítési infrastruktúra az igény szerinti RHEL virtuális gépekhez az Azure-ban](./redhat-rhui.md).
* Ha többet szeretne megtudni a RHEL BYOS ajánlatáról, tekintse meg az Azure-beli [saját előfizetéssel rendelkező Gold-lemezképek Red Hat Enterprise Linux](./byos.md).
* További információ a RHEL összes verziójának Red Hat-támogatási szabályzatáról: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata).