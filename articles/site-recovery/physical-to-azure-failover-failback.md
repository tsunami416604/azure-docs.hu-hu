---
title: "A feladatátvétel, és nem vissza fizikai kiszolgálók replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Feladatok átadása a fizikai kiszolgálók Azure-ba, és visszaadják feladataikat a helyszíni hely, az Azure Site Recovery útmutató"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 08/01/2018
ms.author: raynew
ms.openlocfilehash: 2fbad3b53319c8dc8be3480162a4fa67bab94306
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>A feladatátvétel, és vissza fizikai kiszolgálók Azure-felhőbe replikált sikertelen

Ez az oktatóanyag ismerteti, hogyan feladatátvételt fizikai kiszolgálók Azure-bA. Miután keresztül korábban meghiúsult, Ön nem a kiszolgáló vissza a helyszíni hely a rendelkezésre álló. 

## <a name="preparing-for-failover-and-failback"></a>A feladatátvétel és a feladat-visszavétel előkészítése

Fizikai kiszolgálók replikálása az Azure Site Recovery segítségével vissza, a VMware virtuális gépek csak sikertelen lehet. A feladat-visszavételt a VMware-infrastruktúra szükséges. 

Feladatátvétel és a feladat-visszavétel rendelkezik négy fázisból áll:

1. **Feladatok átadása a Azure**: feladatátadást gépek a helyszíni helyről.
2. **Állítsa az Azure virtuális gépek**: állítsa az Azure virtuális gépeket, hogy elindítja vissza a helyszíni VMware virtuális gépek replikálása.
3. **Feladatok átadása a helyszíni**: feladatátvételt, az Azure-ból sikertelen lesz.
4. **Védelem-újrabeállítási a helyszíni virtuális gépek**: után adatok visszaállítása sikertelen volt, állítsa vissza a megbukott helyszíni VMware virtuális gépeket, hogy elindítja az Azure-bA replikál.

## <a name="verify-server-properties"></a>Ellenőrizze a kiszolgáló tulajdonságai

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy megfelel [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) Azure virtuális gépekhez.

1. A **védett elemek**, kattintson a **replikált elemek**, és jelölje ki a gépet.

2. Az a **replikált elemek** ablaktáblán összegzését gép adatait, állapotát, és a legújabb elérhető helyreállítási pontok. Kattintson a **tulajdonságok** megtekintheti annak további részleteit.
3. A **számítás és hálózat**, módosíthatja a Azure nevét, az erőforráscsoport, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és [lemezbeállításokat felügyelt](#managed-disk-considerations)
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.
5. A **lemezek**, láthatja, hogy a gép operációs rendszerének és adatlemezek kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvételt végez az Azure

1. A **beállítások** > **replikált elemek** kattintson a gépre > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb** (alapértelmezett): Ez a beállítás először dolgozza fel a Site Recovery számára küldött minden adathoz. Mert az Azure virtuális gép létrehozása után feladatátvételi rendelkezik minden az adatok replikálása a Site Recovery szolgáltatásban a feladatátvétel kiváltásakor a legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) biztosít.
   - **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat a gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése), mert nincs van feldolgozásával töltött idő feldolgozatlan adatokat.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a gép a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet ahhoz, hogy kiváltsa a feladatátvételi forrásgép leállításakor. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
4. Ha az Azure virtuális Géphez való kapcsolódáshoz készített, csatlakozás érvényesíti a feladatátvétel után.
5. Miután ellenőrizte, **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> Folyamatban van a feladatátvétel nem szakítható meg. Feladatátvétel megkezdése előtt leállítja a virtuálisgép-replikációt. Ha megszakítja a feladatátvétel, leállítja, de a gép nem fog újra replikálni.
> A fizikai kiszolgálók további feladatátvételi feldolgozási percet is igénybe vehet körülbelül nyolc-tíz befejezéséhez. 

## <a name="create-a-process-server-in-azure"></a>A folyamat-kiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure virtuális Gépen, és visszaküldi azt a helyszíni hely. A kis késleltetésű hálózatra azért szükség a folyamatkiszolgáló és a védett számítógép között.

- Tesztelési célból egy Azure ExpressRoute-kapcsolat esetén használhatja a helyszíni folyamatkiszolgáló automatikusan települ a konfigurációs kiszolgálón.
- Ha a VPN-kapcsolatot, vagy feladat-visszavétel termelési környezetben futtatja, be kell állítania egy Azure virtuális gép feladat-visszavétel Azure-alapú folyamat kiszolgálóként.
- Kövesse az utasításokat a [Ez a cikk](site-recovery-vmware-setup-azure-ps-resource-manager.md) állíthatja be a folyamatkiszolgáló az Azure-ban.

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálásához

Alapértelmezés szerint a fő célkiszolgáló kap a feladat-visszavétel adatokat. Fut a helyi konfigurációs kiszolgálón.

- Ha a VMware virtuális Géphez, amelyhez Ön a feladat-visszavételt a VMware vCenter-kiszolgáló által kezelt ESXi-állomáson, a fő célkiszolgáló kell férnie a virtuális gép adattároló (VMDK), a virtuális gépek lemezei replikált adatokat írni. Győződjön meg arról, hogy a virtuális gép adattároló csatlakoztatva van-e a fő célkiszolgáló gazdagépre, olvasási/írási hozzáférést.
- Ha az ESXi-állomáson, amely nem felügyeli a vCenter-kiszolgáló, a Site Recovery szolgáltatás ismételt védelem alatt hoz létre egy új virtuális Gépet. A virtuális Gépet az ESX-gazdagépen, amelyen hoz létre a fő célkiszolgáló virtuális gép jön létre. A merevlemezt a virtuális gép számára elérhető adattárolót kell lennie a a gazdagép, amelyen fut a fő célkiszolgálón.
- Fizikai gépek, hogy a feladat-visszavételt el kell végeznie a gazdagép, amelyen a fő célkiszolgálón fut, előtt meg lehet lássa el újból védelemmel a gép felderítése.
- Egy másik lehetőség, ha a helyszíni virtuális gép már létezik a feladat-visszavétel törli-e a feladat-visszavétel előtt. Feladat-visszavétel majd létrehoz egy új virtuális Gépet a fő célkiszolgáló ESX-gazdagép ugyanazon a gazdagépen. Ha nem vissza egy másik helyre, az adatok helyreállítása ugyanazon adattár és a helyszíni fő célkiszolgáló által használt azonos ESX-gazdagépet.
- Storage vmotion szolgáltatások használata a fő célkiszolgáló nem használható. Ha így tesz, feladat-visszavétel nem fog működni, mert a lemez nem érhetők el azt. A fő célkiszolgálóra kizárása a vMotion listája.

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

