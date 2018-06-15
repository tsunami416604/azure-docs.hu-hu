---
title: Hogyan VMware tudni az Azure-ból |} Microsoft Docs
description: Az Azure virtuális gépek feladatátvétel után a feladat-visszavétel a virtuális gépeket kapcsolja vissza a helyszíni is kezdeményezhető. Ismerje meg a szükséges lépéseket a feladat-visszavételt.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauravd
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 50dccf6196b7affd3d21087f851b929d0e850f6d
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768472"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Az Azure-ból a helyszíni helyhez sikertelen

Ez a cikk ismerteti, hogyan sikertelen hátsó virtuális gépeket az Azure virtuális gépek helyszíni VMware-környezetben. Ez a cikk utasításait követve sikertelen biztonsági a VMware virtuális gépeket vagy windowsos/Linuxos fizikai kiszolgálók után azok feladatátvételt is a helyszíni hely Azure használatával hajtsa végre a [a Site Recovery feladatátvételi](site-recovery-failover.md) oktatóanyag.

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy rendelkezik olvassa el a részleteket a [feladat-visszavétel különböző típusú](concepts-types-of-failback.md) és a megfelelő korlátozásokkal.

> [!WARNING]
> Feladat-visszavétel nem, miután vagy [áttelepítésével kész](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), a virtuális gép áthelyezése a másik erőforráscsoportba vagy törölni az Azure virtuális géphez. Ha letiltja a virtuális gép védelme, feladat-visszavétel nem.

> [!WARNING]
> Windows Server 2008 R2 SP1 fizikai kiszolgálók, ha a védett, és az Azure-ba, a feladatátvételt nem sikerült vissza.

> [!NOTE]
> Ha VMware virtuális gépek átvette majd nem lehet a feladat-visszavétel egy Hyper-v gazdagépen.


- Mielőtt továbblépne, hajtsa végre a védelem-újrabeállítási, hogy a virtuális gépek replikált állapotban van, és megkezdheti a feladatátvételt egy helyszíni hely. További információkért lásd: [hogyan lássa el újból védelemmel az Azure-ból a helyszíni](site-recovery-how-to-reprotect.md).

- Győződjön meg arról, hogy a vCenter csatlakoztatott állapotban van, a feladat-visszavétel előtt. Ellenkező esetben lemez leválasztása és csatolva a virtuális gépre meghiúsul.

- Az Azure-bA a feladatátvétel során a helyszíni hely nem lehet elérni, és ezért a konfigurációs kiszolgáló lehet vagy nem érhető el vagy -leállítás. Védelem-újrabeállítási és a feladat-visszavétel során a helyszíni konfigurációs kiszolgáló fut, és egy csatlakoztatott OK állapotúnak kell lennie. 

- A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában, vagy nem sikerült a feladat-visszavétel. Ezért győződjön meg arról, hogy a rendszeresen ütemezett biztonsági mentések a kiszolgáló is. Hiba történt egy olyan vészhelyzet esetén, ha a kiszolgáló az eredeti IP-címmel, a feladat-visszavételre működéséhez helyreállítása szeretné.

- A fő célkiszolgáló nem kell meglévő pillanatképeket védelem-újrabeállítási/feladat-visszavétel elindítása előtt.

## <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése
Miután az Azure-bA rendelkezik nem sikerült keresztül, akkor is visszaadják feladataikat a helyszíni hely a következő lépések végrehajtásával:

1. [Lássa el újból védelemmel](site-recovery-how-to-reprotect.md) az Azure virtuális gépeket, hogy elindítja a helyszíni hely VMware virtuális gépeket replikálni. Ez a folyamat részeként szükség:
    1. Egy helyszíni fő célkiszolgáló beállítása: Windows fő célkiszolgáló Windows virtuális gépek és [Linuxos fő célkiszolgáló](site-recovery-how-to-install-linux-master-target.md) Linux virtuális gépekhez.
    2. Állítson be egy [folyamatkiszolgáló](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Kezdeményezzen [lássa el újból védelemmel](site-recovery-how-to-reprotect.md). Ez a helyszíni virtuális gép kikapcsolása, és az Azure virtuális gépek adatainak szinkronizálása a helyszíni lemezeket.

1. A virtuális gépek Azure a helyszíni helyre replikál, Miután elindította egy sikertelen keresztül az Azure-ból a helyszíni hely.

1. Után az adatok visszaállítása sikertelen volt, hogy lássa el újból védelemmel a helyszíni virtuális gépek újra, hogy elindítja replikálása Azure-bA.

Gyors áttekintését tekintse meg a következő videót visszaadják feladataikat egy helyszíni hely kapcsolatos.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>A feladat-visszavételt lépései

> [!IMPORTANT]
> Feladat-visszavétel elindítása előtt győződjön meg arról, hogy végrehajtotta-e a virtuális gépek ismételt védelem. A virtuális gépek védett állapotban kell lennie, és meg kell az állapotukat **OK**. Adja meg újra a virtuális gépek védelmét, olvassa el [hogyan állítsa](site-recovery-how-to-reprotect.md).

1. A replikált elemek oldalon válassza ki a virtuális gépet, és kattintson a jobb gombbal, válassza ki, hogy **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel**, ellenőrizze a feladatátvételi irányát (az Azure-ból), és válassza ki a helyreállítási pontot (legújabb vagy a legutóbbi alkalmazáskonzisztens), amely a feladatátvételre használni kívánt. Az alkalmazás konzisztens pont mögött a legutóbbi pontnak időben, ami adatvesztést.
3. A feladatátvétel során a Site Recovery Azure virtuális gépek leáll. Után ellenőrizheti, hogy feladat-visszavétel a várt módon befejeződött, ellenőrizheti, hogy a virtuális gépek Azure-be lett zárva.
4. **Véglegesítse** távolítsa el a feladatokat átadó virtuális gép Azure.Right kattintással a védett elem, majd kattintson a szükséges **véglegesítése**. Egy feladat fog távolítsa el a sikertelen virtuális gépeket az Azure-ban.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Milyen helyreállítási pontra is szeretnék a feladat-visszavételt a virtuális gépek?

A feladat-visszavétel során történő feladat-visszavételt a virtuális gép vagy helyreállítási terv két választási lehetősége van.

- Ha a legújabb feldolgozott pont időben, az összes virtuális gép átvétele fog megtörténni a legutóbbi pontnak időben. Abban az esetben, egy replikációs csoportot a helyreállítási terv belül van, majd a replikációs csoport minden egyes virtuális gép hajt végre feladatátvételt a független legújabb pontjára időben.

    Nem utasíthat el újra a virtuális gépek amíg rá nem kényszerül legalább egy helyreállítási pontot. Nem utasíthat el újra a helyreállítási terv mindaddig, amíg az összes virtuális gép rendelkezik legalább egy helyreállítási pontot.

> [!NOTE]
> A legutóbbi helyreállítási pontot az összeomlás-konzisztens helyreállítási pontot.

- Ha az alkalmazáskonzisztens helyreállítási pontnak, egyetlen virtuális gép feladat-visszavétel anélkül állíthatóak vissza a legutóbbi elérhető alkalmazáskonzisztens helyreállítási pontnak. Minden replikációs csoport esetében a replikációs csoport helyreállítási tervet, az általános rendelkezésre álló helyreállítási pontra állítja helyre.
Alkalmazáskonzisztens helyreállítási pontokat is szerepelhet mögött ideje, és előfordulhat adatvesztés adatokat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Mi történik a VMware-eszközök feladás egy vagy több feladat-visszavétel?

Esetén a Windows rendszerű virtuális gép Azure Site Recovery a VMware-eszközök letiltása a feladatátvétel során. A Windows virtuális gép feladat-visszavétel során a VMware-eszközök ismét engedélyezni. 


## <a name="reprotect-from-on-premises-to-azure"></a>Állítsa a helyszíni Azure-bA
Feladat-visszavétel befejezése, és kezdeményezte a véglegesítés után a rendszer törli a helyreállított virtuális gépeket az Azure-ban. Most a virtuális gép vissza a helyszíni hely van, de azt nem lehet védetté tenni. Indítsa el újra az Azure-bA replikálni, tegye a következőket:

1. A **tároló** > **beállítás** > **replikált elemek**, válassza ki a visszaállítása sikertelen volt, és kattintson a virtuális gépek **védelmének újbóli beállításához**.
2. Az érték a folyamatkiszolgáló, amelyet a adatokat küldhet vissza az Azure ad.
3. Kattintson a **OK** a védelem-újrabeállítási feladat megkezdéséhez.

> [!NOTE]
> Miután egy helyszíni virtuális gép elindul, csak bizonyos idő az ügynök regisztrálása a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ebben az időszakban a védelem-újrabeállítási sikertelen lesz, és egy hibaüzenetet jelenít meg arról, hogy az ügynök nincs telepítve. Várjon néhány percet, és próbálkozzon újra az védelem-újrabeállítási.

## <a name="next-steps"></a>További lépések

Miután a védelem-újrabeállítási feladat befejeződik, a virtuális gép replikálódik vissza az Azure-ba, és mindent egy [feladatátvételi](site-recovery-failover.md) újra át a virtuális gépet az Azure-bA.


