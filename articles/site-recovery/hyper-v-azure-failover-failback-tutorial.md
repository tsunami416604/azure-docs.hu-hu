---
title: "A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Útmutató az Azure-bA Hyper-V virtuális gépek a feladatátvétel, és visszaadják feladataikat a helyszíni hely Azure Site Recovery szolgáltatással"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: 7d2d99c2429a461307cbb9a276eb3b62d13718d2
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek Azure-felhőbe replikált

Ez az oktatóanyag az Azure-bA a Hyper-V virtuális gépek a feladatátvétel ismerteti. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy ellenőrizze a Hyper-V virtuális gép tulajdonságok adott a specifikációknak való Azure-követelmények
> * Feladatátvétel futtatása az Azure-ban
> * Az Azure-beli virtuális gépek ismételt védelme a helyszíni helyen
> * Az Azure-ból a helyszíni sikertelen
> * A helyszíni virtuális gépek ismételt védelme, hogy ismét az Azure-ba replikáljon

Ez az oktatóanyag egy sorozat ötödik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait.

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](tutorial-prepare-on-premises-hyper-v.md)
3. Állítsa be a vész-helyreállítási [Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md), vagy a [System Center VMM-felhőkben felügyelt Hyper-V virtuális gépek](tutorial-hyper-v-vmm-to-azure.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Készítse elő a feladatátvétel és a feladat-visszavétel

Győződjön meg arról, hogy a virtuális gép nem pillanatképek vannak, és, hogy a helyszíni virtuális gép ki van kapcsolva ismételt védelem. Ez elősegíti az adatok konzisztenciáját a replikáció során. Ne kapcsolja be a virtuális gépet az ismételt védelem befejeződése után. 

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-ba**: Gépek átvétele a helyszíni helyről az Azure-ba.
2. **Állítsa az Azure virtuális gépek**: állítsa az Azure virtuális gépeket, így replikálni vissza a helyszíni Hyper-V virtuális gépek indítása.
3. **Feladatok átadása a helyszíni**: feladatátvételt az Azure-ból a helyi webhelyhez, ha azok elérhetők.
4. **Védelem-újrabeállítási a helyszíni virtuális gépek**: Miután adatok visszaállítása sikertelen volt, állítsa a helyszíni virtuális gépek őket az Azure-bA replikálást indítani.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

Ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **védett elemek**, kattintson a **replikált elemek** >< virtuális gép neve >.

2. Az a **replikált elemek** ablaktáblában tekintse át a Virtuálisgép-adatok, állapotát és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
     - A **számítás és hálózat**, módosítsa a virtuális gép beállításait, és hálózati beállításai, többek között az alhálózaton, amelyben az Azure virtuális Géphez. Felügyelt lemezek feladat-visszavételi az Azure-ból a Hyper-v-hez nem támogatottak.
   kerülnek után feladatátvétel és a hozzá rendelt IP-címet.
    - A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="fail-over-to-azure"></a>Feladatok átadása a Azure

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
2. A **feladatátvételi** válassza ki a **legújabb** helyreállítási pontot. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli hajtsa végre a forrás virtuális gépek leállítása ahhoz, hogy kiváltsa a feladatátvételt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Kattintson a feladatátvétel ellenőrzése után **véglegesítése**. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételi leállítja, de a virtuális gép nem fog újra replikálni.

## <a name="reprotect-azure-vms"></a>Az Azure-beli virtuális gépek ismételt védelme

1. Az a **AzureVMVault** > **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, amely a feladatátvételt volt, és válassza ki **védelmének újbóli beállításához**.
2. Ellenőrizze, hogy a védelem irányba **Azure a helyszíni**.
3. A helyszíni virtuális gép ismételt védelem érdekében az adatok konzisztenciájának ki van kapcsolva. Ne kapcsolja be ismételt védelem befejezése után.
4. A ismételt védelemmel ellátni azt követően a virtuális gép elindul, az Azure-ból a helyszíni helyre történő replikálása.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Feladatok átadása az Azure-ból, és állítsa a helyszíni virtuális gép

Feladatok átadása az Azure-ból a helyszíni hely, és indítsa el a virtuális gépek replikálása Azure a helyszíni helyről.

1. A **beállítások** > **replikált elemek**, kattintson a virtuális gép > **tervezett feladatátvétel**.
2. A **tervezett feladatátvétel megerősítése**, ellenőrizze a feladatátvételi irányát (az Azure-ból), és válassza ki a forrás és cél helyeket.
3. Válassza ki **szinkronizálja az adatokat a feladatátvétel előtt (csak a változási különbözeteket szinkronizálása)**. Ezt a beállítást VM állásidő minimálisra csökkenti, mert a virtuális gép leállítása nélkül szinkronizálják.
4. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
5. A kezdeti adatok után szinkronizálás történik, és készen áll az Azure virtuális gépek kattintson leállítása **feladatok** > tervezett-feladatátvételi-feladat-neve > **befejezheti a feladatátvételt**. Ez az Azure virtuális gép leállása, átviszi a legutóbbi változtatásokat a helyszíni és a helyszíni virtuális gép elindul.
6. Jelentkezzen be a helyszíni virtuális gép ellenőrizni a várt módon érhető el.
7. A helyszíni virtuális gép van egy **függőben lévő véglegesítési** állapotát. Kattintson a **véglegesítése**. Ez törli az Azure virtuális gépek és a lemezek, és előkészíti a helyszíni virtuális gép fordított replikációs.
Indítsa el a helyszíni virtuális gép replikálása Azure-ba, engedélyezze a **visszirányú replikálása**. Ez elindítja a replikációs különbözeti változásainak óta az Azure virtuális gép volt kapcsolható ki.  
