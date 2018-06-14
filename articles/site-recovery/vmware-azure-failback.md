---
title: Sikertelen az Azure-ból az Azure Site Recovery VMware |} Microsoft Docs
description: Az Azure virtuális gépek feladatátvétel után megkezdheti a feladat-visszavétel vissza a helyszíni virtuális gépek érdekében. Ismerje meg a szükséges lépéseket a feladat-visszavételt.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 8f580fa40bade2bf586dd116729881b249bbba88
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29944007"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Az Azure-ból a helyszíni helyhez sikertelen

Ez a cikk ismerteti, hogyan sikertelen hátsó virtuális gépeket az Azure virtuális gépek egy helyszíni VMware-környezetben. Ez a cikk utasításait követve sikertelen biztonsági a VMware virtuális gépeket vagy windowsos/Linuxos fizikai kiszolgálók után azok feladatátvételt is a helyszíni hely Azure használatával hajtsa végre a [az Azure Site Recovery feladatátvételi](site-recovery-failover.md) oktatóanyag.

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy rendelkezik olvassa el a részleteket a [feladat-visszavétel különböző típusú](concepts-types-of-failback.md) és a megfelelő korlátozásokkal.

> [!WARNING]
> Nem utasíthat el után már [áttelepítésével kész](migrate-overview.md#what-do-we-mean-by-migration), a virtuális gép áthelyezése a másik erőforráscsoportba vagy törölni az Azure virtuális géphez. Ha letiltja a virtuális gép védelmét, akkor nem feladat-visszavételt.

> [!WARNING]
> Windows Server 2008 R2 SP1 fizikai kiszolgálók, ha a védett, és az Azure-ba, a feladatátvételt nem sikerült vissza.

> [!NOTE]
> Ha VMware virtuális gépeket sikertelenek voltak, nem utasíthat el a Hyper-V gazdagépet.


- A folytatás előtt hajtsa végre a védelem-újrabeállítási, hogy a virtuális gépek replikált állapotban van, és elindíthatja a feladatátvételt egy helyszíni hely. További információkért lásd: [hogyan lássa el újból védelemmel az Azure-ból a helyszíni](vmware-azure-reprotect.md).

- Győződjön meg arról, hogy a vCenter csatlakoztatott állapotban van, a feladat-visszavétel előtt. Ellenkező esetben lemez leválasztása és csatolva a virtuális gépre meghiúsul.

- Az Azure-bA a feladatátvétel során a helyszíni hely nem feltétlenül érhető el, és előfordulhat, hogy a konfigurációs kiszolgáló nem érhető el, vagy leállított le. Védelem-újrabeállítási és a feladat-visszavétel során a helyszíni konfigurációs kiszolgáló fut, és egy csatlakoztatott OK állapotúnak kell lennie. 

- A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában, vagy nem sikerült a feladat-visszavétel. Győződjön meg arról, hogy a rendszeresen ütemezett biztonsági mentések a kiszolgáló is. Ha katasztrófa történik, a feladat-visszavételi működéséhez az eredeti IP-címmel-kiszolgáló helyreállítása szeretné.

- A fő célkiszolgáló nem kell meglévő pillanatképeket védelem-újrabeállítási/feladat-visszavétel elindítása előtt.

## <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése
Miután az Azure-bA rendelkezik nem sikerült keresztül, akkor is visszaadják feladataikat a helyszíni hely a következő lépések végrehajtásával:

1. [Lássa el újból védelemmel](vmware-azure-reprotect.md) az Azure virtuális gépeket, hogy elindítja a helyszíni hely VMware virtuális gépeket replikálni. Ez a folyamat részeként szükség:

    * Állítson be egy helyszíni fő célkiszolgáló. Használja a Windows fő célkiszolgáló Windows virtuális gépek és a [Linuxos fő célkiszolgáló](vmware-azure-install-linux-master-target.md) Linux virtuális gépekhez.
    * Állítson be egy [folyamatkiszolgáló](vmware-azure-set-up-process-server-azure.md).
    * Start [lássa el újból védelemmel](vmware-azure-reprotect.md) kapcsolja ki a helyszíni virtuális gépet, és az Azure virtuális gépek adatainak szinkronizálja a helyi lemezek.

2. Miután az Azure virtuális gépei replikálnak a helyi webhelyhez, akkor indítson el egy feladatátvételt az Azure-ból a helyszíni hely.

3. Az adatok visszaállítása sikertelen, akkor állítsa a helyszíni virtuális gépek újra, hogy elindítja replikálása Azure-bA.

Gyors áttekintését tekintse meg a következő videót visszaadják feladataikat egy helyszíni hely kapcsolatos:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>A feladat-visszavételt lépései

> [!IMPORTANT]
> Feladat-visszavétel megkezdése előtt győződjön meg arról, hogy befejeződött-e a virtuális gépek ismételt védelem. A virtuális gépek védett állapotban kell lennie, és meg kell az állapotukat **OK**. Adja meg újra a virtuális gépek védelmét, olvassa el [hogyan állítsa](vmware-azure-reprotect.md).

1. A replikált elemek lapon válassza ki a virtuális gép. Kattintson a jobb gombbal, válassza ki, hogy **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel**, ellenőrizze a feladatátvételi irányát (az Azure-ból). Válassza ki a helyreállítási pontot (legújabb vagy a legutóbbi alkalmazáskonzisztens), amely a feladatátvételre használni kívánt. Az alkalmazás konzisztens pont mögött a legutóbbi pontnak időben, ami adatvesztést.
3. A feladatátvétel során a Site Recovery Azure virtuális gépek leáll. Után ellenőrizze, hogy feladat-visszavétel befejezése a várt módon, ellenőrizheti, hogy a virtuális gépeket az Azure-on le.
4. **Véglegesítse** az Azure-ból a átvevő virtuális gép eltávolításához szükséges. Kattintson a jobb gombbal a védett elem, majd válassza ki **véglegesítése**. Egy feladat eltávolítja az átvevő virtuális gépek az Azure-ban.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Milyen helyreállítási pontra is szeretnék a feladat-visszavételt a virtuális gépek?

A feladat-visszavétel során történő feladat-visszavételt a virtuális gép vagy helyreállítási terv két választási lehetősége van.

- Idő a legújabb feldolgozott pontot választ, ha az összes virtuális gép átadja a legújabb elérhető pont időben. Ha a replikációs csoport a helyreállítási terv belül, a replikációs csoport minden egyes virtuális gép feladatátadás a független legújabb pontjára időben.

  Nem utasíthat el újra a virtuális gépek amíg rá nem kényszerül legalább egy helyreállítási pontot. Nem utasíthat el újra a helyreállítási terv mindaddig, amíg az összes virtuális gép rendelkezik legalább egy helyreállítási pontot.

  > [!NOTE]
  > A legutóbbi helyreállítási pontot az összeomlás-konzisztens helyreállítási pontot.

- Ha a alkalmazáskonzisztens helyreállítási pontot választja, a legutóbbi elérhető alkalmazáskonzisztens helyreállítási pontnak egyetlen virtuális gép feladat-visszavétel állítja helyre. Minden replikációs csoport esetében a replikációs csoport helyreállítási tervet, az általános rendelkezésre álló helyreállítási pontra állítja helyre.
Alkalmazáskonzisztens helyreállítási pontokat is szerepelhet mögött ideje, és előfordulhat adatvesztés adatokat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Mi történik a VMware-eszközök feladás egy vagy több feladat-visszavétel?

Egy Windows rendszerű virtuális gép esetén a Site Recovery a VMware-eszközök letiltja a feladatátvétel során. A VMware-eszközök a Windows virtuális gép feladat-visszavétel során újra vannak engedélyezve. 


## <a name="reprotect-from-on-premises-to-azure"></a>Állítsa a helyszíni Azure-bA
Követően visszaadását, és már elindította véglegesítési, a rendszer törli a helyreállított virtuális gépeket az Azure-ban. Most a virtuális gép vissza a helyszíni hely van, de azt nem lehet védetté tenni. Indítsa el újra az Azure-bA replikálni, tegye a következőket:

1. Válassza ki **tároló** > **beállítás** > **replikált elemek**, vissza sikertelen virtuális gépeket, majd válassza ki és  **Ismételt védelemmel**.
2. Adja meg a folyamatkiszolgáló, amelyet a adatokat küldhet vissza az Azure-bA értékét.
3. Válassza ki **OK** a védelem-újrabeállítási feladat megkezdéséhez.

> [!NOTE]
> Miután egy helyszíni virtuális gép elindul, csak bizonyos idő az ügynök regisztrálása a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ebben az időszakban a védelem-újrabeállítási sikertelen lesz, és egy hibaüzenetet jelenít meg arról, hogy az ügynök nincs telepítve. Várjon néhány percet, és próbálkozzon újra az védelem-újrabeállítási.

## <a name="next-steps"></a>További lépések

A védelem-újrabeállítási feladat befejezése után a virtuális gép replikálódik vissza Azure, és végezheti a [feladatátvételi](site-recovery-failover.md) újra át a virtuális gépet az Azure-bA.


