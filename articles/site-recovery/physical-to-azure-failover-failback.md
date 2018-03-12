---
title: "A feladatátvétel, és nem vissza fizikai kiszolgálók replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Feladatok átadása a fizikai kiszolgálók Azure-ba, és visszaadják feladataikat a helyszíni hely, az Azure Site Recovery útmutató"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 6334bdef73ea55ba0a0e15c5880daf03264e5c60
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>A feladatátvétel, és vissza fizikai kiszolgálók Azure-felhőbe replikált sikertelen

Ez az oktatóanyag ismerteti, hogyan feladatátvételt fizikai kiszolgálók Azure-bA. Miután keresztül korábban meghiúsult, Ön nem a kiszolgáló vissza a helyszíni hely a rendelkezésre álló. 

## <a name="preparing-for-failover-and-failback"></a>Felkészülés a feladatátvételre és a feladat-visszavételre

Fizikai kiszolgálók replikálása az Azure Site Recovery segítségével vissza, a VMware virtuális gépek csak sikertelen lehet. A feladat-visszavételt a VMware-infrastruktúra szükséges. 

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-ba**: Gépek átvétele a helyszíni helyről az Azure-ba.
2. **Állítsa az Azure virtuális gépek**: állítsa az Azure virtuális gépeket, hogy elindítja vissza a helyszíni VMware virtuális gépek replikálása.
3. **Feladatátvétel a helyszínre**: Feladatátvétel futtatása az Azure-ból való feladat-visszavételhez.
4. **Védelem-újrabeállítási a helyszíni virtuális gépek**: után adatok visszaállítása sikertelen volt, állítsa vissza a megbukott helyszíni VMware virtuális gépeket, hogy elindítja az Azure-bA replikál.

## <a name="verify-server-properties"></a>Ellenőrizze a kiszolgáló tulajdonságai

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy megfelel [Azure-követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines) Azure virtuális gépekhez.

1. A **védett elemek**, kattintson a **replikált elemek**, és jelölje ki a gépet.

2. Az a **replikált elemek** ablaktáblán összegzését gép adatait, állapotát, és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a [rendelkezésre állási csoportot](../virtual-machines/windows/tutorial-availability-sets.md) és a [felügyelt lemez beállításait](#managed-disk-considerations)
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **lemezek**, láthatja, hogy a gép operációs rendszerének és adatlemezek kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb** (alapértelmezett): Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat a gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a gép a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet ahhoz, hogy kiváltsa a feladatátvételi forrásgép leállításakor. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.
5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Folyamatban van a feladatátvétel nem szakítható meg. Feladatátvétel megkezdése előtt leállítja a virtuálisgép-replikációt. Ha megszakítja a feladatátvétel, leállítja, de a gép nem fog újra replikálni.
> A fizikai kiszolgálók további feladatátvételi feldolgozási percet is igénybe vehet körülbelül nyolc-tíz befejezéséhez. 

## <a name="create-a-process-server-in-azure"></a>Folyamatkiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure-beli virtuális gépről, és elküldi azokat a helyszíni helyre. A kis késleltetésű hálózatra azért szükség a folyamatkiszolgáló és a védett számítógép között.

- Ha Azure ExpressRoute-kapcsolata van, tesztelési célból használhatja a konfigurációs kiszolgálóra automatikusan telepített helyszíni folyamatkiszolgálót.
- Ha VPN-kapcsolata van, vagy ha éles környezetben futtat feladat-visszavételt, a feladat-visszavételhez be kell állítania egy Azure-beli virtuális gépet Azure alapú folyamatkiszolgálóként.
- Kövesse az utasításokat a [Ez a cikk](vmware-azure-set-up-process-server-azure.md) állíthatja be a folyamatkiszolgáló az Azure-ban.

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálása

Alapértelmezés szerint a fő célkiszolgáló kap a feladat-visszavétel adatokat. Fut a helyi konfigurációs kiszolgálón.

- Ha a VMware virtuális Géphez, amelyhez Ön a feladat-visszavételt a VMware vCenter-kiszolgáló által kezelt ESXi-állomáson, a fő célkiszolgáló kell férnie a virtuális gép adattároló (VMDK), a virtuális gépek lemezei replikált adatokat írni. Győződjön meg arról, hogy a virtuális gép adattárolója olvasási/írási hozzáféréssel van-e csatlakoztatva a fő célkiszolgáló gazdagépéhez.
- Ha az ESXi-állomáson, amely nem felügyeli a vCenter-kiszolgáló, a Site Recovery szolgáltatás ismételt védelem alatt hoz létre egy új virtuális Gépet. A virtuális Gépet az ESX-gazdagépen, amelyen hoz létre a fő célkiszolgáló virtuális gép jön létre. A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.
- Fizikai gépek, hogy a feladat-visszavételt el kell végeznie a gazdagép, amelyen a fő célkiszolgálón fut, előtt meg lehet lássa el újból védelemmel a gép felderítése.
- Egy másik lehetőség, ha a helyszíni virtuális gép már létezik a feladat-visszavétel törli-e a feladat-visszavétel előtt. A feladat-visszavétel ekkor új virtuális gépet hoz létre a fő célként megadott ESX-gazdagépen. Amikor másik helyre végez feladat-visszavételt, akkor az adatokat a helyszíni fő célkiszolgáló által használt adattárolóba és ESX-gazdagépre állítja helyre.
- Nem használhatja a Storage vMotion szolgáltatást a fő célkiszolgálón. Ha mégis így tesz, a feladat-visszavétel nem működik, mert nem érhetők el számára a lemezek. Zárja ki a fő célkiszolgálókat a vMotion-listából.

## <a name="reprotect-azure-vms"></a>Az Azure-beli virtuális gépek ismételt védelme

Ez az eljárás feltételezi, hogy a helyszíni virtuális gép nem érhető el, és hogy egy másik helyen végez ismételt védelmet.

1. A **Beállítások** > **Replikált elemek** területen kattintson a jobb gombbal arra a virtuális gépre, amelyhez feladatátvételt végzett > **Ismételt védelem**.
2. Az **Ismételt védelem** területen ellenőrizze, hogy be legyen jelölve az **Azure-ról a helyszíni rendszerre** lehetőség.
3. Adja meg a helyszíni fő célkiszolgálót és a folyamatkiszolgálót.

4. Az **Adattároló** területen válassza ki azt a fő cél adattárolót, amelyre a lemezeket a helyszínen helyre szeretné állítani. Akkor használja ezt a lehetőséget, ha törölték a helyszíni virtuális gépet, és új lemezeket kell létrehoznia. Ezt a beállítást a rendszer figyelmen kívül hagyja, ha a lemez már létezik, de az értéket meg kell adnia.
5. Válassza ki a fő célkiszolgáló adatmegőrzési meghajtóját. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
6. Kattintson az **OK** gombra az ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát.

> [!NOTE]
> Ha meglévő helyszíni virtuális gépre szeretné helyreállítani az Azure-beli virtuális gépet, csatlakoztassa a helyszíni virtuális gép olvasási/írási hozzáféréssel rendelkező adattárolóját a fő célkiszolgáló ESXi-gazdagépén.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Az Azure-ból a helyszíni rendszerre irányuló feladatátvétel futtatása

A helyszíni rendszerre való újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a házirend automatikusan létrejött, amikor replikációs szabályzatot hozott létre az Azure-ba végzett replikációhoz:

- A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.
- A szabályzatot nem lehet módosítani.
- A szabályzat értékei a következők:
    - Helyreállítási időkorlát küszöbértéke = 15 perc
    - Helyreállítási pont megőrzése = 24 óra
    - Alkalmazáskonzisztens pillanatkép gyakorisága = 60 perc

A következőképpen futtassa a feladatátvételt:

1. A **Replikált elemek** lapon kattintson a jobb gombbal a gépre > **Nem tervezett feladatátvétel**.
2. A **Feladatátvétel megerősítése** területen ellenőrizze, hogy az irány az Azure-ból a helyszíni rendszerre mutat.

3. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot. Az alkalmazáskonzisztens helyreállítási pont időpontja a legújabb időpont elé esik, és ez némi adatvesztést okoz. A feladatátvétel futtatásakor a Site Recovery leállítja az Azure-beli virtuális gépeket, és elindítja a helyszíni virtuális gépet. Némi állásidővel kell számolni, ezért válasszon megfelelő időpontot.
4. Kattintson a jobb gombbal a gépre, és kattintson a **Véglegesítés** parancsra. Ez elindítja az Azure-beli virtuális gépeket eltávolító feladatot.
5. Győződjön meg arról, hogy Azure-beli virtuális gépek a várt módon álltak le.


## <a name="reprotect-on-premises-machines-to-azure"></a>Helyszíni gépek ismételt védelme az Azure-ban

Az adatoknak most a helyszíni helyen kell lenniük, de nincsenek az Azure-ba replikálva. A következőképpen indíthatja el ismét a replikációt az Azure-ba:

1. A tárolóban > **Beállítások** >**Replikált elemek**, válassza ki azokat a virtuális gépeket, amelyekhez feladatátvételt végzett, és kattintson az **Ismételt védelem** lehetőségre.
2. Válassza ki azt a folyamatkiszolgálót, amellyel a replikált adatokat az Azure-ba küldi, majd kattintson az **OK** gombra.

Az ismételt védelem befejezése után a virtuális gép az Azure-ba replikál, és szükség szerint futtathat feladatátvételt.

