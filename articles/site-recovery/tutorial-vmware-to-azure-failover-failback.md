---
title: "A feladatátvétel és a sikertelen biztonsági VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Feladatok átadása a VMware virtuális gépek és fizikai kiszolgálók Azure-ba, és visszaadják feladataikat a helyszíni hely, az Azure Site Recovery útmutató"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 28a14a9b28dfe9c2014add9b9f691bce6ba91a4c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>A feladatátvétel és a sikertelen biztonsági VMware virtuális gépek és fizikai kiszolgálók Azure-felhőbe replikált

Ez az oktatóanyag az Azure-bA a VMware virtuális gépek a feladatátvétel ismerteti. Miután keresztül korábban meghiúsult, hogy visszaadják feladataikat a helyszíni hely a rendelkezésre álló. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy ellenőrizze a VMware virtuális gép tulajdonságok adott a specifikációknak való Azure-követelmények
> * Feladatátvételt végez az Azure
> * A folyamatkiszolgáló és fő célkiszolgáló feladat-visszavételi létrehozása
> * A helyszíni hely Azure virtuális gépek állítsa
> * Feladatok átadása az Azure-ból a helyszíni
> * Állítsa a helyszíni virtuális gépek replikálása Azure-bA újra elindítani

Ez az a sorozat ötödik oktatóanyaga. Ez az oktatóanyag feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagok a.

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](tutorial-prepare-on-premises-vmware.md)
3. [Vészhelyreállítás beállítása](tutorial-vmware-to-azure.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>A feladatátvétel és a feladat-visszavétel előkészítése

Győződjön meg arról, hogy a virtuális gép nem pillanatképek vannak. A helyszíni virtuális gép ismételt védelem ki van kapcsolva.
Ez segít a replikáció során az adatok konzisztenciájának biztosítására. Ne kapcsolja be a virtuális gép ismételt védelem befejeződése után. Az azonos fő célkiszolgáló használatával lássa el újból védelemmel egy replikációs csoportot. Ha egy másik fő célkiszolgáló lássa el újból védelemmel egy replikációs csoportot, a kiszolgáló nem adhatók meg a közös pontja időben.

Feladatátvétel és a feladat-visszavétel rendelkezik négy fázisból áll:

1. **Feladatok átadása a Azure**: feladatátadást gépek a helyszíni helyről.
2. **Állítsa az Azure virtuális gépek**: állítsa az Azure virtuális gépeket, így replikálni vissza a helyszíni VMware virtuális gépek indítása.
3. **Feladatok átadása a helyszíni**: feladatátvételt, az Azure-ból sikertelen lesz.
4. **Védelem-újrabeállítási a helyszíni virtuális gépek**: után adatok visszaállítása sikertelen volt, állítsa a helyszíni virtuális gépek vissza a megbukott, hogy elindítja replikálása Azure-bA.

## <a name="verify-vm-properties"></a>Ellenőrizze a virtuális gép tulajdonságai

Ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel-e az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **védett elemek**, kattintson a **replikált elemek** > virtuális gép.

2. Az a **replikált elemek** ablaktáblában nincs Virtuálisgép-adatok, állapotát, összegzését, és a legújabb elérhető helyreállítási pontok. Kattintson a **tulajdonságok** megtekintheti annak további részleteit.

3. A **számítás és hálózat**, módosíthatja a Azure nevét, az erőforráscsoport, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és [lemezbeállításokat felügyelt](#managed-disk-considerations)

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.

5. A **lemezek**, az operációs rendszer és az adatlemezek információkat tekintheti meg a virtuális Gépen.

## <a name="run-a-failover-to-azure"></a>Feladatátvételt végez az Azure

1. A **beállítások** > **replikált elemek** kattintson a virtuális gép > **feladatátvételi**.

2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb** (alapértelmezett): Ez a beállítás először dolgozza fel a Site Recovery számára küldött minden adathoz. Mert az Azure virtuális gép létrehozása után feladatátvételi rendelkezik minden az adatok replikálása a Site Recovery szolgáltatásban a feladatátvétel kiváltásakor a legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) biztosít.
   - **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat a virtuális gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése), mert nincs van feldolgozásával töltött idő feldolgozatlan adatokat.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a virtuális Gépet, a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** számára engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.

4. Ha az Azure virtuális Géphez való kapcsolódáshoz készített, csatlakozás érvényesíti a feladatátvétel után.

5. Miután ellenőrizte, **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikációs le van állítva.
> Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. Bizonyára észrevette, hogy már a feladatátvételi idő a fizikai kiszolgálók, a VMware Linux gépek, a VMware virtuális gépek, amelyek nem rendelkeznek a DHCP szolgáltatás lehetővé teszi, hogy és a VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramok tesztelése: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>A folyamat-kiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure virtuális Gépen, és visszaküldi azt a helyszíni hely. A kis késleltetésű hálózatra azért szükség a folyamatkiszolgáló és a védett virtuális gép között.

- Tesztelési célból egy Azure ExpressRoute-kapcsolat esetén használhatja a helyszíni folyamatkiszolgáló automatikusan települ a konfigurációs kiszolgálón.
- Ha a VPN-kapcsolatot, vagy feladat-visszavétel termelési környezetben futtatja, be kell állítania egy Azure virtuális gép feladat-visszavétel Azure-alapú folyamat kiszolgálóként.
- A folyamatkiszolgáló az Azure-ban, kövesse az utasításokat a [Ez a cikk](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálásához

Alapértelmezés szerint a fő célkiszolgálón fut a helyi konfigurációs kiszolgálón. Ez az oktatóanyag céljából alapértelmezett fő használunk. A fő célkiszolgáló kap a feladat-visszavétel adatokat.

Ha a virtuális gép vCenter-kiszolgáló által kezelt ESXi-gazdagépen, a fő célkiszolgáló kell férnie a virtuális gép adattároló (VMDK), a virtuális gépek lemezei replikált adatokat írni. Győződjön meg arról, hogy a virtuális gép adattároló csatlakoztatva van-e a fő célkiszolgáló gazdagépre, olvasási/írási hozzáférést.

Ha a virtuális gép egy ESXi, amely nem a vCenter-kiszolgáló felügyeli, a Site Recovery szolgáltatás ismételt védelem alatt hoz létre egy új virtuális Gépet. A virtuális gép létrehozása az ESX-gazdagépen, amelyen hoz létre a fő célkiszolgálón.
A merevlemezt a virtuális gép számára elérhető adattárolót kell lennie a a gazdagép, amelyen fut a fő célkiszolgálón.

Ha a virtuális gép vCenter nem használ, el kell végeznie a gazdagép, amelyen a fő célkiszolgálón fut, előtt meg lehet lássa el újból védelemmel a gép felderítése. Ez igaz vissza fizikai kiszolgálók túl sikertelenek lesznek. Egy másik lehetőség, ha a helyszíni virtuális gép létezik, az törli-e a feladat-visszavétel előtt. Feladat-visszavétel majd létrehoz egy új virtuális Gépet a fő célkiszolgáló ESX-gazdagép ugyanazon a gazdagépen. Ha nem vissza egy másik helyre, az adatok helyreállítása ugyanazon adattár és a helyszíni fő célkiszolgáló által használt azonos ESX-gazdagépet.

Storage vmotion szolgáltatások használata a fő célkiszolgáló nem használható. Ha így tesz, feladat-visszavétel nem fog működni, mert a lemez nem érhetők el azt. A fő célkiszolgálóra kizárása a vMotion listája.

## <a name="reprotect-azure-vms"></a>Lássa el újból védelemmel az Azure virtuális gépek

Ez az eljárás feltételezi, hogy a helyszíni virtuális gép nem érhető el, és egy másik helyre van újbóli védelméhez.

1. A **beállítások** > **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, amely a feladatátvételt lett > **védelmének újbóli beállításához**.
2. A **védelmének újbóli beállításához**, ellenőrizze, hogy **Azure a helyszíni**, van kiválasztva.
3. Adja meg a helyszíni fő célkiszolgáló, és a folyamatkiszolgáló.

4. A **Datastore**, válassza ki a, amelyhez végre kívánja hajtani a lemezek a helyszíni fő célkiszolgáló adattárolót. Használja ezt a beállítást, ha a helyszíni virtuális gép törölve lett, és új lemezek létrehozásához szükséges. Ez a beállítás a rendszer figyelmen kívül hagyja, ha a lemez már létezik, de meg kell adnia értéket.
5. Válassza ki a fő célkiszolgáló adatmegőrzési meghajtó. A feladat-visszavétel házirend automatikusan ki van jelölve.
6. Kattintson a **OK** ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni hely. A előrehaladásának a a **feladatok** fülre.

> [!NOTE]
> Ha szeretné helyreállítani az Azure virtuális gép egy meglévő helyszíni virtuális gép, az olvasási/írási hozzáférést a helyszíni virtuális gép adattároló csatlakozási a fő célkiszolgáló ESXi-állomáson.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Feladatátvétel az Azure-ból a helyszíni futtatása

Vissza a helyszíni replikálni, egy feladatátvételi házirendhez szolgál. Ez a házirend automatikusan létrejön, a replikáció az Azure replikációs házirend létrehozása után:

- A házirend automatikusan tartozik a konfigurációs kiszolgáló.
- A házirend nem lehet módosítani.
- A házirend értékei a következők:
    - Helyreállítási Időkorlát küszöbértéke = 15 perc
    - Helyreállítási pontok megőrzésének ideje 24 óra =
    - Alkalmazáskonzisztens pillanatkép gyakorisága = 60 perc

A feladatátvétel futtatása az alábbiak szerint:

1. Az a **replikált elemek** lapon kattintson a jobb gombbal a gép > **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel**, ellenőrizze, hogy a feladatátvétel irányát az Azure-ból.

3. Válassza ki a feladatátvételre használni kívánt helyreállítási pontot. Időben a legutóbbi pont előtt történik az alkalmazáskonzisztens helyreállítási pontot, és az adatvesztést okoz. Feladatátvételi futtatásakor a Site Recovery állítja le az Azure virtuális gépeken, és a helyszíni virtuális gép elindul. Nem kell némi állásidővel, így válassza ki egy megfelelő időpontot.
4. Kattintson a jobb gombbal a gépet, és kattintson a **véglegesítése**. Ezzel elindítja egy feladatot, amely eltávolítja az Azure virtuális gépeken.
5. Győződjön meg arról, hogy Azure virtuális gépek be lett zárva várt módon.


## <a name="reprotect-on-premises-machines-to-azure"></a>Állítsa a helyszíni gépeket az Azure-bA

Adatok kell vissza a helyi webhelyen, de nem az Azure-bA végez replikációt. Megkezdheti az Azure-bA replikál újra az alábbiak szerint:

1. A tárolóban lévő > **beállítások** > **replikált elemek**, válassza ki a sikertelen biztonsági vissza sikertelen, és kattintson a virtuális gépek **védelmének újbóli beállításához**.
2. Válassza ki a folyamatkiszolgáló, amellyel a replikált adatok az Azure-ba, majd kattintson az **OK**.

A feladatátvételen befejezése után a virtuális gép vissza az Azure-bA replikál, és lefuttathat egy feladatátvételi szükség szerint.
