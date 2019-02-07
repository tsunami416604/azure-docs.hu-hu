---
title: Feladatátadás és feladatátvétel a Site Recovery az Azure-bA vész-helyreállítási fizikai kiszolgálók |} A Microsoft Docs
description: Útmutató az Azure-bA fizikai kiszolgálók feladatait átadja, és a feladat-visszavételhez vész-helyreállítási az Azure Site Recovery a helyszíni hely
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 4e787ab134caee1a7f9a26e46f698f2fe9807d83
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813679"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Átadása és visszavétele Azure-bA replikált fizikai kiszolgálók

Ez az oktatóanyag leírja, hogyan végezhet feladatátvételt egy fizikai kiszolgáló Azure-bA. A feladatátvétel után visszaadja a feladatokat a kiszolgáló a helyszíni hely elérhetővé válik.

## <a name="preparing-for-failover-and-failback"></a>Felkészülés a feladatátvételre és a feladat-visszavételre

Fizikai kiszolgálók replikálása az Azure Site Recovery használatával; az VMware virtuális gépek csak sikertelen lehet. VMware-infrastruktúrára van szüksége ahhoz, hogy a feladat-visszavételt.

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Az Azure-bA feladatátvételt**: Gépek átvétele a helyszíni helyről az Azure-bA.
2. **Az Azure virtuális gépek ismételt védelme**: Az Azure virtuális gépek ismételt védelme, hogy megkezdődhessen a vissza a helyszíni VMware virtuális gépek replikálása.
3. **Átadja a feladatokat a helyszíni**: Feladatátvételt végez, az Azure-ból sikertelen lesz.
4. **Az ismételt védelem a helyszíni virtuális gépek**: Miután adatokat vissza nem sikerült, ismételt védelme a helyszíni VMware virtuális gépek vissza az, hogy megkezdődhessen a replikálás az Azure-bA.

## <a name="verify-server-properties"></a>Ellenőrizze a kiszolgáló tulajdonságai

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy megfelel [Azure-követelmények](vmware-physical-azure-support-matrix.md#replicated-machines) az Azure virtuális gépek.

1. A **védett elemek**, kattintson a **replikált elemek**, és válassza ki a gépet.

2. Az a **replikált elem** ablaktáblán állapot, gép információk összegzését, és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat**, módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és a felügyelt lemez beállításait
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **lemezek**, láthatja, hogy a gép operációs rendszerének és az adatlemezek kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a beállítás átadja a feladatokat a gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a gépet a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megpróbálja forrásgép leállítása a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.
5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a feladatátvételt, folyamatban van. Feladatátvétel megkezdése előtt leállítja a gép replikálása. Ha megszakítja a feladatátvétel, leállítja, de a gép nem replikálja újra.
> A fizikai kiszolgálók esetében további feladatátvételi feldolgozási is igénybe vehet körülbelül nyolc-tíz percet végrehajtásához.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvételt követően RDP vagy SSH segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket, [itt](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="create-a-process-server-in-azure"></a>Folyamatkiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure-beli virtuális gépről, és elküldi azokat a helyszíni helyre. Egy kis késleltetésű hálózatra szükség a folyamatkiszolgáló és a védett számítógép között.

- Ha Azure ExpressRoute-kapcsolata van, tesztelési célból használhatja a konfigurációs kiszolgálóra automatikusan telepített helyszíni folyamatkiszolgálót.
- Ha VPN-kapcsolata van, vagy ha éles környezetben futtat feladat-visszavételt, a feladat-visszavételhez be kell állítania egy Azure-beli virtuális gépet Azure alapú folyamatkiszolgálóként.
- Kövesse a [Ez a cikk](vmware-azure-set-up-process-server-azure.md) állíthatja be a folyamatkiszolgáló az Azure-ban.

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálása

Alapértelmezés szerint a fő célkiszolgáló fogadja a feladat-visszavételi adatokat. A helyszíni konfigurációs kiszolgálót futtat.

- Ha a VMware virtuális gép, amelyhez visszavétel egy VMware vCenter-kiszolgáló által kezelt ESXi-gazdagépen, a fő célkiszolgáló kell férnie a virtuális gép adattárolójához (VMDK), a replikált adatokat írni a Virtuálisgép-lemezek. Győződjön meg arról, hogy a virtuális gép adattárolója olvasási/írási hozzáféréssel van-e csatlakoztatva a fő célkiszolgáló gazdagépéhez.
- Ha a vCenter-kiszolgáló, a Site Recovery szolgáltatás által kezelt ESXi-gazdagéphez egy új virtuális gép ismételt védelem során hoz létre. A virtuális gép létrehozása az ESX-gazdagépen, amelyen a fő célkiszolgáló virtuális Gépet hoz létre. A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.
- Fizikai gépek, amelyek, feladat-visszavételt, hajtsa végre, amelyen a fő célkiszolgálón fut, mielőtt ismét megvédhetné a gépet gazdagép felderítését.
- Egy másik lehetőség, ha már létezik a helyszíni virtuális gép feladat-visszavétel, hogy a feladat-visszavételhez mielőtt törölné. A feladat-visszavétel ekkor új virtuális gépet hoz létre a fő célként megadott ESX-gazdagépen. Amikor másik helyre végez feladat-visszavételt, akkor az adatokat a helyszíni fő célkiszolgáló által használt adattárolóba és ESX-gazdagépre állítja helyre.
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

Az adatoknak most a helyszíni helyen vannak, de nincsenek az Azure-ba replikálva. A következőképpen indíthatja el ismét a replikációt az Azure-ba:

1. A tárolóban > **Beállítások** >**Replikált elemek**, válassza ki azokat a virtuális gépeket, amelyekhez feladatátvételt végzett, és kattintson az **Ismételt védelem** lehetőségre.
2. Válassza ki azt a folyamatkiszolgálót, amellyel a replikált adatokat az Azure-ba küldi, majd kattintson az **OK** gombra.

Az ismételt védelem befejezése után a virtuális gép az Azure-ba replikál, és szükség szerint futtathat feladatátvételt.
