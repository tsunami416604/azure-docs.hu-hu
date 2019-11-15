---
title: A VMware virtuális gépek/fizikai kiszolgálók feladatátvétele az Azure-ból Azure Site Recovery
description: Ismerje meg, hogyan térhet vissza a helyszíni helyre az Azure-ba történő feladatátvétel után, a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítása során.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084063"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>A VMware virtuális gépek és fizikai kiszolgálók feladatátvétele az Azure-ból a helyszíni helyre

Ez a cikk azt ismerteti, hogyan lehet visszaadni a virtuális gépeket az Azure Virtual Machines a helyszíni VMware-környezetbe. Az ebben a cikkben található utasítások alapján hajtsa végre a VMware virtuális gépeket vagy a Windows/Linux rendszerű fizikai kiszolgálókat, miután azok feladatátvételt végeznek a helyszíni helyről az Azure-ba a [feladatátvétel Azure site Recovery](site-recovery-failover.md) oktatóanyagban való használatával.

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy elolvasta a feladat- [visszavétel különböző típusaival](concepts-types-of-failback.md) és a megfelelő kikötésekkel kapcsolatos adatokat.

> [!WARNING]
> Ha [befejezte az áttelepítést](migrate-overview.md#what-do-we-mean-by-migration), a rendszer áthelyezte a virtuális gépet egy másik erőforráscsoporthoz, vagy törölte az Azure-beli virtuális gépet. Ha letiltja a virtuális gép védelmét, nem végezheti el a feladat-visszavételt.

> [!WARNING]
> Egy Windows Server 2008 R2 SP1 rendszerű fizikai kiszolgáló, ha a védett és az Azure-ba történő feladatátvétele nem sikerült.

> [!NOTE]
> Ha a VMware-alapú virtuális gépek feladatátvétele meghiúsult, akkor nem végezheti el a feladatátvételt egy Hyper-V-gazdagépre.


- A folytatás előtt végezze el az ismételt védelem lépéseit, hogy a virtuális gépek replikált állapotban legyenek, és a feladatátvételt visszaállíthatja egy helyszíni helyre. További információkért lásd: az [Azure-ból a helyszíni rendszerbe való ismételt védelem](vmware-azure-reprotect.md).

- A feladat-visszavétel előtt ellenőrizze, hogy a vCenter csatlakoztatott állapotban van-e. Ellenkező esetben a lemezek leválasztása és a virtuális géphez való csatolása meghiúsul.

- Az Azure-ba történő feladatátvétel során előfordulhat, hogy a helyszíni hely nem érhető el, és előfordulhat, hogy a konfigurációs kiszolgáló nem érhető el, vagy leáll. Az ismételt védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálónak futnia kell, és egy csatlakoztatott OK állapotban kell lennie. 

- A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában, vagy a feladat-visszavétel nem fog sikerülni. Győződjön meg arról, hogy rendszeresen ütemezett biztonsági mentést készít a kiszolgálóról. Ha katasztrófa következik be, vissza kell állítania a kiszolgálót az eredeti IP-címmel a feladat-visszavételhez.

- A fő célkiszolgáló nem rendelkezhet pillanatképekkel az ismételt védelem/feladat-visszavétel elindítása előtt.

## <a name="overview-of-failback"></a>A feladat-visszavétel áttekintése
Az Azure-ba történő feladatátvétel után a következő lépések végrehajtásával visszatérhet a helyszíni helyhez:

1. Állítsa be [újra a virtuális](vmware-azure-reprotect.md) gépeket az Azure-ban, hogy azok a helyszíni helyen lévő VMWare virtuális gépekre replikálódnak. A folyamat részeként a következőket is el kell végeznie:

    * Helyszíni fő cél beállítása. Használjon Windows fő célt a Windows rendszerű virtuális gépekhez, valamint egy Linux rendszerű virtuális gépekhez készült [linuxos fő célt](vmware-azure-install-linux-master-target.md) .
    * Hozzon létre egy [folyamat-kiszolgálót](vmware-azure-set-up-process-server-azure.md).
    * A helyszíni virtuális gép kikapcsolásához és az Azure-beli virtuális gép adatainak a helyszíni lemezekkel való szinkronizálásának megkezdéséhez indítsa el az [újravédelmet](vmware-azure-reprotect.md) .

2. Miután az Azure-beli virtuális gépek replikálódnak a helyszíni helyre, elindít egy feladatátvételt az Azure-ból a helyszíni helyre.

3. Az adat-visszalépést követően újra el kell látnia a helyszíni virtuális gépeket, hogy az Azure-ba replikálja őket.

A gyors áttekintéshez tekintse meg a következő videót, amely bemutatja, hogyan végezheti el a feladatátvételt egy helyszíni helyre:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>A feladat-visszavétel lépései

> [!IMPORTANT]
> A feladat-visszavétel elindítása előtt ellenőrizze, hogy befejeződött-e a virtuális gépek ismételt védelme. A virtuális gépeknek védett állapotban kell lenniük, és az állapotuknak **rendben**kell lennie. A virtuális gépek újravédéséhez olvassa el a következő [témakört](vmware-azure-reprotect.md):.

1. A replikált elemek lapon válassza ki a virtuális gépet. Kattintson rá a jobb gombbal a nem **tervezett feladatátvétel**kiválasztásához.
2. A **feladatátvétel megerősítése lapon**ellenőrizze a feladatátvétel irányát (az Azure-ból). Ezután válassza ki a feladatátvételhez használni kívánt helyreállítási pontot (a legújabbat vagy a legújabb, konzisztens alkalmazást). Az alkalmazás konzisztens pontja a legutóbbi időponthoz kapcsolódik, és adatvesztést okoz.
3. A feladatátvétel során Site Recovery leállítja az Azure-beli virtuális gépeket. Miután megtörtént, hogy a feladat-visszavétel a várt módon fejeződött be, megtekintheti, hogy az Azure-beli virtuális gépek le vannak-e állítva.
4. A feladatátvételt a virtuális gép Azure-ból való eltávolításához **kell végrehajtani.** Kattintson a jobb gombbal a védett elemre, majd válassza a **véglegesítés**lehetőséget. A feladatok megszüntetik az Azure-ban a feladatátvétel alatt álló virtuális gépeket.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Milyen helyreállítási pontra lehet visszaadni a virtuális gépeket?

A feladat-visszavétel során két lehetősége van a virtuális gép/helyreállítási terv visszavételére.

- Ha a legutóbbi feldolgozott időpontot választja, az összes virtuális gép feladatátvételt hajt végre a legutolsó elérhető időpontra. Ha van replikációs csoport a helyreállítási tervben, a replikálási csoport minden virtuális gépe feladatátvételt hajt végre a független legkésőbbi időpontban.

  Egy virtuális gép nem hajtható végre, amíg legalább egy helyreállítási ponttal rendelkezik. Helyreállítási terv nem hajtható végre, amíg az összes virtuális gép legalább egy helyreállítási ponttal rendelkezik.

  > [!NOTE]
  > A legújabb helyreállítási pontok összeomlás-konzisztens helyreállítási pontok.

- Ha az alkalmazás-konzisztens helyreállítási pontot választja, egyetlen virtuális gép feladat-visszavétele helyreállítja a legújabb elérhető alkalmazás-konzisztens helyreállítási pontot. Replikációs csoporttal rendelkező helyreállítási terv esetén minden replikációs csoport a közös rendelkezésre álló helyreállítási pontra lesz helyreállítva.
Az alkalmazással konzisztens helyreállítási pontok az idő alatt lehetnek, és az adatvesztés is lehet.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Mi történik a VMware-eszközök feladat-visszavétel után?

Windows rendszerű virtuális gépek esetén a Site Recovery letiltja a VMware-eszközöket a feladatátvétel során. A Windows rendszerű virtuális gép feladat-visszavétele során a VMware-eszközök ismét engedélyezve lesznek. 


## <a name="reprotect-from-on-premises-to-azure"></a>Ismételt védelem a helyszínről az Azure-ba
Miután befejeződött a feladat-visszavétel, és elindította a véglegesítést, a rendszer törli a helyreállított virtuális gépeket az Azure-ban. A virtuális gép most visszatért a helyszíni helyre, de nem lesz védve. Az Azure-ba való replikálás megkezdéséhez tegye a következőket:

1. Válassza a tár > a **replikált elemek** > **beállítást** , válassza ki a visszaadott virtuális gépeket, majd válassza az **ismételt védelem** **lehetőséget.**
2. Adja meg az Azure-ba történő adatküldéshez használni kívánt folyamat-kiszolgáló értékét.
3. Az ismételt védelem elindításához kattintson **az OK gombra** .

> [!NOTE]
> Ha egy helyszíni virtuális gép elindul, eltart egy ideig, amíg az ügynök vissza nem regisztrálja a konfigurációs kiszolgálót (akár 15 percet is igénybe vehet). Ebben az időszakban az ismételt védelem meghiúsul, és egy hibaüzenetet ad vissza, amely azt jelzi, hogy az ügynök nincs telepítve. Várjon néhány percet, majd próbálkozzon újra az ismételt védekezéssel.

## <a name="next-steps"></a>Következő lépések

Az ismételt védelem befejezése után a virtuális gép visszareplikál az Azure-ba, és [feladatátvételsel](site-recovery-failover.md) áthelyezheti a virtuális gépeket az Azure-ba.


