---
title: Nem sikerült az Azure-ból az Azure Site Recovery VMware |} A Microsoft Docs
description: Az Azure virtuális gépek a feladatátvételt követően térjen vissza a helyszíni virtuális gépek csatlakozva a feladat-visszavételhez is elindítható. Ismerje meg, hogy a feladat-visszavételt lépéseit.
author: nsoneji
ms.service: site-recovery
ms.date: 10/11/2018
ms.topic: conceptual
ms.author: nisoneji
ms.openlocfilehash: 841e036555723051dfeaf946f31eaf9eedaee798
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093510"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Az Azure-ból a helyszíni helyhez sikertelen

Ez a cikk ismerteti, hogyan végezhet vissza virtuális gépeket az Azure Virtual Machines egy helyszíni VMware-környezetről. Ez a cikk utasításait követve sikertelen biztonsági a VMware virtuális gépek vagy Windows/Linux hely után, a feladatátvétel a helyszíni fizikai kiszolgálóknak az Azure-bA használatával kövesse a [az Azure Site Recovery feladatátvételi](site-recovery-failover.md) oktatóanyag.

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy rendelkezik-e a részletek olvasási kapcsolatos, a [különböző típusú feladat-visszavétel](concepts-types-of-failback.md) és a megfelelő korlátozásokkal.

> [!WARNING]
> Nem utasíthat el újra, miután van az egyik [áttelepítésével kész](migrate-overview.md#what-do-we-mean-by-migration), egy virtuális gép áthelyezése egy másik erőforráscsoportot, vagy az Azure virtuális gép törlése. Ha letiltja a virtuális gép védelmét, visszavételt nem.

> [!WARNING]
> Egy Windows Server 2008 R2 SP1 fizikai kiszolgálóra, ha védett, és az Azure-bA feladatátvétele nem kell a feladatátvételben.

> [!NOTE]
> Ha VMware virtuális gépek feladatátvétel nem visszaadja a feladatokat egy Hyper-V gazdagépen.


- A folytatás előtt hajtsa végre az ismételt védelem, hogy egy replikált állapotban van a virtuális gépek és a egy helyszíni hely feladatátvétel megkezdése. További információkért lásd: [hogyan ismételt védelme az Azure-ból a helyszíni](vmware-azure-reprotect.md).

- Győződjön meg arról, hogy a vcenter-kiszolgáló csatlakoztatott állapotban a feladat-visszavétel előtt. Ellenkező esetben lemezek leválasztása és csatolja őket a virtuális gépre sikertelen lesz.

- Az Azure-ba történő feladatátvétel során a helyszíni hely nem érhető el, és előfordulhat, hogy a konfigurációs kiszolgáló nem érhető el, vagy leállították le. Védelem-újrabeállítást és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálón fut, és a egy csatlakoztatott OK állapotúnak kell lennie. 

- A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában, vagy nem sikerült a feladat-visszavétel. Ellenőrizze, hogy tegye meg a kiszolgáló rendszeresen ütemezett biztonsági mentéseket. Ha katasztrófa történik, meg kell-visszaállítás működéséhez a feladat-visszavétel az eredeti IP-címet a kiszolgálón.

- A fő célkiszolgáló nem kell minden olyan pillanatképek aktiválása az ismételt védelem, illetve-visszavétel előtt.

## <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése
Miután Ön rendelkezik a feladatátvétel az Azure-ba is visszaadja a feladatokat a helyszíni hely a következő lépések végrehajtásával:

1. [Ismételt védelem](vmware-azure-reprotect.md) a virtuális gépek az Azure-ban, hogy megkezdődhessen a helyszíni helyen található VMware virtuális gépek replikálása. Ez a folyamat részeként szükség:

    * Állítsa be egy helyszíni fő célkiszolgálót. Egy Windows fő célkiszolgálót használja a Windows virtuális gépek és a egy [Linuxos fő célkiszolgáló](vmware-azure-install-linux-master-target.md) Linux rendszerű virtuális gépek.
    * Állítsa be a [folyamatkiszolgáló](vmware-azure-set-up-process-server-azure.md).
    * Indítsa el [ismételt védelme](vmware-azure-reprotect.md) kapcsolja ki a helyszíni virtuális gépet, és az Azure virtuális gépek adatainak szinkronizálja a helyi lemezek.

2. Miután az Azure-beli virtuális gépek replikálása a helyszíni helyre, megkezdése feladatátvétel az Azure-ból a helyszíni helyre.

3. Után az adatok vissza nem sikerül, akkor ismételt védelme a helyszíni virtuális gépeket újra, hogy megkezdődhessen a replikálás az Azure-bA.

Röviden tekintse meg a következő videó bemutatja, hogyan feladat-visszavételhez egy helyszíni helyhez:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Feladat-visszavételt lépései

> [!IMPORTANT]
> Feladat-visszavétel előtt ellenőrizze, hogy befejeződött-e a virtuális gépek ismételt védelem. A virtuális gép védett állapotban kell lennie, és azok állapotának kell **OK**. A virtuális gépek ismételt védelme, olvassa el [hogyan védi meg ismét](vmware-azure-reprotect.md).

1. A replikált elemet oldalon válassza ki a virtuális gépet. Kattintson a jobb gombbal, válassza ki, hogy **nem tervezett feladatátvétel**.
2. A **feladatátvétel megerősítése**, ellenőrizze a feladatátvétel iránya (az Azure-ból). Válassza ki a helyreállítási pontot (legújabb vagy a legutóbbi alkalmazáskonzisztens), amelyet a feladatátvételre használni szeretne. Az alkalmazás konzisztens pont időben a legutóbbi időpontra mögött van, és némi adatvesztést okoz.
3. A feladatátvétel során a Site Recovery leállítja a virtuális gépek az Azure-ban. Után ellenőrizheti, hogy feladat-visszavétel befejezése a várt módon, nézze meg, hogy az Azure-beli virtuális gépek leállítása.
4. **Véglegesítse** az Azure-ból a átvevő virtuális gép eltávolításához szükséges. Kattintson a jobb gombbal a védett elem, és válassza **véglegesítése**. Egy feladat eltávolítja a átvevő virtuális gépeket az Azure-ban.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Milyen helyreállítási pontnak is szeretnék feladat-visszavételt a virtuális gépek?

A feladat-visszavétel során való feladat-visszavételt a virtuális gép, illetve helyreállítási terv két lehetősége van.

- Ha a legutóbbi feldolgozott pont időben, az összes virtuális gép feladatátvételt a legutóbbi pont időben. Ha egy replikációs csoportot a helyreállítási terven belül, a replikációs csoport minden virtuális gép feladatait a független legújabb pontnak időben.

  Nem utasíthat el vissza egy virtuális gép mindaddig, amíg legalább egy helyreállítási pont rendelkezik. Nem utasíthat el vissza a helyreállítási terv mindaddig, amíg az összes virtuális gép rendelkezik legalább egy helyreállítási pontot.

  > [!NOTE]
  > A legutóbbi helyreállítási pontot az összeomlás-konzisztens helyreállítási pont.

- Ha az alkalmazás-konzisztens helyreállítási pont, egy egyetlen virtuális gép feladat-visszavétel, a legutóbbi elérhető alkalmazáskonzisztens helyreállítási pontot állítja helyre. A replikációs csoport helyreállítási terv esetén minden replikációs csoport az általános rendelkezésre álló helyreállítási pontot állítja helyre.
Alkalmazáskonzisztens helyreállítási pontok időben késhet, és előfordulhat adatvesztés az adatokat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Mi történik a VMware tools utáni feladat-visszavétel?

Egy Windows virtuális gép esetén a Site Recovery a VMware-eszközök letiltja a feladatátvétel során. A Windows virtuális gép feladat-visszavétel során a VMware-eszközöket újra vannak engedélyezve. 


## <a name="reprotect-from-on-premises-to-azure"></a>A helyszínről az Azure-bA ismételt védelme
Feladat-visszavétel befejezése és a véglegesítés elindítása után a rendszer törli a helyreállított virtuális gépek az Azure-ban. Most a virtuális gép vissza a helyszíni helyen van, de már nem biztosítható. Indítsa el újra az Azure-bA replikálni, tegye a következőket:

1. Válassza ki **tároló** > **beállítás** > **replikált elemek**, a virtuális gépek, amelyek a feladat-visszavételben, majd válassza ki és  **Ismételt védelem**.
2. Adja meg a folyamatkiszolgálót kell használni adatok küldése az Azure-ba, az értékét.
3. Válassza ki **OK** az ismételt védelmi feladat elindításához.

> [!NOTE]
> Ha egy helyszíni virtuális gép generációazonosítójának ideig, az ügynököt, hogy regisztrálja a konfigurációs kiszolgálóra (akár 15 perc) vesz igénybe. Ebben az időszakban az ismételt védelem sikertelen lesz, és egy hibaüzenetet jelenít meg arról, hogy az ügynök nincs telepítve. Várjon néhány percet, és próbálkozzon az ismételt védelem-újrabeállításba.

## <a name="next-steps"></a>További lépések

Teheti meg az ismételt védelmi feladat befejezése után a virtuális gépet replikálja vissza az Azure, és a egy [feladatátvételi](site-recovery-failover.md) a virtuális gépek áthelyezése az Azure-bA újra.


