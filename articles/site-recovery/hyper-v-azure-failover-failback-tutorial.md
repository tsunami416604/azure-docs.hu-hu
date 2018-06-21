---
title: A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs
description: Útmutató az Azure-bA Hyper-V virtuális gépek a feladatátvétel, és visszaadják feladataikat a helyszíni hely Azure Site Recovery szolgáltatással
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294292"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>A feladatátvételi és a feladat-visszavétel Hyper-V virtuális gépek Azure-felhőbe replikált

Ez az oktatóanyag leírja, hogyan egy Hyper-V virtuális gép az Azure-bA. Miután a rendelkezésre álló meghiúsult már keresztül, hogy feladat-visszavétel és a helyszíni hely számára. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy ellenőrizze a Hyper-V virtuális gép tulajdonságok adott a specifikációknak való Azure-követelmények
> * Feladatátvétel futtatása az Azure-ban
> * Feladat-visszavétel az Azure-ból a helyszíni
> * A visszirányú replikálás a helyszíni virtuális gépek replikálása Azure-bA újra elindítani

Ez az oktatóanyag egy sorozat ötödik oktatóanyag. Feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagok a.    

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni Hyper-V előkészítése](tutorial-prepare-on-premises-hyper-v.md)
3. Állítsa be a vész-helyreállítási [Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md), vagy a [System Center VMM-felhőkben felügyelt Hyper-V virtuális gépek](tutorial-hyper-v-vmm-to-azure.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Készítse elő a feladatátvétel és a feladat-visszavétel

Győződjön meg arról, hogy a virtuális gép nem pillanatképek vannak, és, hogy a helyszíni virtuális gép ki van kapcsolva feladat-visszavétel során. Segít biztosítani az adatok konzisztenciájának replikáció során. Ne kapcsolja be a helyszíni virtuális gép feladat-visszavétel során. 

A feladatátvételi és a feladat-visszavétel rendelkezik három fázisból áll:

1. **Feladatátvétel az Azure-bA**: feladatátvételi Hyper-V virtuális gépek a helyszíni helyről az Azure-bA.
2. **A helyszíni feladat-visszavétel**: feladatátvételi Azure virtuális gépek helyszíni webhelyekhez, ha azok elérhetők. Virtuális gépek replikálása a helyszíni Hyper-V virtuális gépek kezdődik. Kezdeti szinkronizálás után, a helyszíni hely feladatátvételi Azure virtuális gépeken.  
3. **A visszirányú replikálás a helyszíni virtuális gépek**: után adatok visszaállítása sikertelen volt, a névkeresési őket az Azure-bA replikálást indítani a helyszíni virtuális gépek replikálása.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

Feladatátvétel előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép szervezzen [Azure-követelményeknek](hyper-v-azure-support-matrix.md#replicated-vms).

1. A **védett elemek**, kattintson a **replikált elemek** >< virtuális gép neve >.

2. Az a **replikált elemek** ablaktáblában tekintse át a Virtuálisgép-adatok, állapotát és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
     - A **számítás és hálózat**, módosíthatja a virtuális gép beállításait, és a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található feladatátvétel után, és az IP-cím, amely rendeli hozzá. Felügyelt lemezek feladat-visszavételi az Azure-ból a Hyper-v-hez nem támogatottak.
      - A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-bA

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
2. A **feladatátvételi** válassza ki a **legújabb** helyreállítási pontot. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli hajtsa végre a forrás virtuális gépek leállítása ahhoz, hogy kiváltsa a feladatátvételt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Kattintson a feladatátvétel ellenőrzése után **véglegesítése**. Törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: Ha megszakítja a folyamatban lévő feladatátvételi leállítja, de a virtuális gép nem fog újra replikálni.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Feladat-visszavétel Azure virtuális gép a helyszíni és a fordított replikáció a helyszíni virtuális gép

Feladat-visszavételi művelet alapvetően a feladatátvétel az Azure-ból a helyszíni hely és a visszirányú replikálás újra elindul a helyszíni helyről származó virtuális gépek replikálása Azure-bA.

1. A **beállítások** > **replikált elemek**, kattintson a virtuális gép > **tervezett feladatátvétel**.
2. A **tervezett feladatátvétel megerősítése**, ellenőrizze a feladatátvételi irányát (az Azure-ból), és válassza ki a forrás és cél helyeket.
3. Válassza ki **szinkronizálja az adatokat a feladatátvétel előtt (csak a változási különbözeteket szinkronizálása)**. Ezt a beállítást VM állásidő minimálisra csökkenti, mert a virtuális gép leállítása nélkül szinkronizálják.
4. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
5. A kezdeti adatok után szinkronizálás történik, és készen áll az Azure virtuális gépek kattintson leállítása **feladatok** > tervezett-feladatátvételi-feladat-neve > **befejezheti a feladatátvételt**. Azt az Azure virtuális gép leállása, átviszi a legutóbbi változtatásokat a helyszíni és a helyszíni virtuális gép elindul.
6. Jelentkezzen be a helyszíni virtuális gép ellenőrizni a várt módon érhető el.
7. A helyszíni virtuális gép van egy **függőben lévő véglegesítési** állapotát. Kattintson a **véglegesítése**. Törli az Azure virtuális gépek és a lemezek, és a helyszíni virtuális gép előkészíti a fordított replikációra.
Indítsa el a helyszíni virtuális gép replikálása Azure-ba, engedélyezze a **visszirányú replikálása**. Elindítja a replikációs különbözeti változásainak óta az Azure virtuális gép volt kapcsolható ki.  
