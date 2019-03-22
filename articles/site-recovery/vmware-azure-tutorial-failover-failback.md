---
title: VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele a Site Recoveryvel az Azure-ba végzett vészhelyreállítás során | Microsoft Docs
description: Megtudhatja, hogy végezheti el VMware virtuális gépek és fizikai kiszolgálók feladatátvételét az Azure-ba, illetve a feladatok visszavételét a helyszíni kiszolgálókra az Azure Site Recoveryvel végzett, Azure-ba történő vészhelyreállítás során
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 68f1c5156f4c12af33e6088d862fc12d98021fd4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310217"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Az Azure-ba replikált VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele

Ez az oktatóanyag leírja, hogyan vehetők át a VMware virtuális gépek feladatai az Azure-ba. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A VMware virtuális gép tulajdonságainak ellenőrzése abból a szempontból, hogy az megfelel-e az Azure-követelményeknek
> * Feladatátvétel futtatása az Azure-ban
> * Folyamatkiszolgáló és fő célkiszolgáló létrehozása a feladat-visszavételhez
> * Az Azure-beli virtuális gépek ismételt védelme a helyszíni helyen
> * Feladatátvétel az Azure-ból a helyszínre
> * A helyszíni virtuális gépek ismételt védelme, hogy ismét az Azure-ba replikáljon

>[!NOTE]
>Az oktatóanyagokat úgy terveztük meg, hogy az adott forgatókönyvhöz a legegyszerűbb üzembehelyezési utat mutassák be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Ha további információt szeretne a feladatátvételi teszt lépéseiről, olvassa el az [útmutatót](site-recovery-failover.md).

Ez az oktatóanyag egy sorozat ötödik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait.

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](vmware-azure-tutorial-prepare-on-premises.md)
3. [Vészhelyreállítás beállítása](vmware-azure-tutorial.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)
5. A fenti lépések mellett érdemes [áttekinteni, hogy létezik-e az architektúrában](vmware-azure-architecture.md) vészhelyreállítási forgatókönyv.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Az Azure-bA feladatátvételt**: Gépek átvétele a helyszíni helyről az Azure-bA.
2. **Az Azure virtuális gépek ismételt védelme**: Az Azure virtuális gépek ismételt védelme, hogy megkezdődhessen a replikálás térjen vissza a helyszíni VMware virtuális gépeket. A helyszíni virtuális gép ki van kapcsolva az ismételt védelem alatt. Ez elősegíti az adatok konzisztenciáját a replikáció során.
3. **Átadja a feladatokat a helyszíni**: Feladatátvételt végez, az Azure-ból sikertelen lesz.
4. **Az ismételt védelem a helyszíni virtuális gépek**: Miután adatokat vissza nem sikerült, ismételt védelme a helyszíni virtuális gépek vissza az, hogy megkezdődhessen a replikálás az Azure-bA.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

Ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel az [Azure-követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines).

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

3. A **számítás és hálózat**, módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és a felügyelt lemez beállításait

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.

2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a beállítás nem sikerül a virtuális Gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás nem sikerül a virtuális Gépet a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza a **Gép leállítása a feladatátvétel elkezdése előtt.** lehetőséget, ha azt szeretné, hogy a rendszer megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy **hosszabb a feladatátvételi teszt ideje** a 9.8-as verziónál régebbi mobilitási szolgáltatást használó VMware virtuális gépeken, a fizikai kiszolgálókon, a VMware Linux virtuális gépeken, a fizikai kiszolgálóként védett Hyper-V virtuális gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll.
> Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Csatlakozás feladatátvételi virtuális gépekhez az Azure-ban

1. Ha a feladatátvételt követően RDP vagy SSH segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket, [itt](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. A feladatátvételt követően lépjen a virtuális géphez, és [csatlakozzon](../virtual-machines/windows/connect-logon.md) hozzá érvényesítés céljából.
3. Az érvényesítést követően a **Véglegesítés** lehetőségre kattintva véglegesítse a virtuális gép feladatátvétel utáni helyreállítási pontját. A véglegesítést követően a rendszer az összes többi rendelkezésre álló helyreállítási pontot törli. Ezzel befejeződik a feladatátvételi tevékenység.

>[!TIP]
> A **Helyreállítási pont módosítása** lehetőséggel kiválaszthat egy másik helyreállítási pontot a feladatátvételt követően, ha nem elégedett a feladatátviteli virtuális géppel. A **véglegesítés** műveletét követően ez a lehetőség nem lesz elérhető.

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Azure-beli virtuális gép ismételt védelmének előkészítése

- Használhatja az **Azure ExpressRoute-kapcsolat használata esetén** a telepítés részeként a konfigurációs kiszolgálóra automatikusan telepített helyszíni folyamatkiszolgálót (beépített folyamatkiszolgálót).

> [!IMPORTANT]
> Ha a helyszíni környezet és az Azure között VPN-kapcsolat van, az ismételt védelemhez és a feladat-visszavételhez be kell állítania egy Azure-beli virtuális gépet folyamatkiszolgálóként. A folyamatkiszolgálók Azure-ban való beállításához kövesse az [ebben a cikkben](vmware-azure-set-up-process-server-azure.md) szereplő utasításokat.

Ez talál további információt a védelem-újrabeállítást és a feladat-visszavétel előfeltételeinek [szakasz](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálása

A fő célkiszolgáló fogadja és kezeli a replikációs adatokat az Azure-ból történő feladat-visszavétel során. Alapértelmezés szerint elérhető a helyszíni konfigurációs kiszolgálón. A jelen oktatóanyagban az alapértelmezett fő célkiszolgálót használjuk.

>[!NOTE]
>A Linux-alapú virtuális gépek védelméhez létre kell hozni egy különálló fő célkiszolgálót. További információért [kattintson ide](vmware-azure-install-linux-master-target.md).

Ha a virtuális gép **vCenter-kiszolgáló által kezelt ESXi-gazdagépen** van, akkor a fő célkiszolgálónak hozzá kell férnie a virtuális gép adattárolójához (VMDK), hogy a replikált adatokat a virtuális gépek lemezeire írja. Győződjön meg arról, hogy a virtuális gép adattárolója olvasási/írási hozzáféréssel van-e csatlakoztatva a fő célkiszolgáló gazdagépéhez.

Ha a virtuális gép **nem vCenter-kiszolgáló által kezelt ESXi-gazdagépen** van, akkor a Site Recovery szolgáltatás új virtuális gépet hoz létre az ismételt védelem során. A virtuális gép azon az ESX-gazdagépen jön létre, amelyen a fő célkiszolgálót hozta létre.
A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.

Ha a virtuális gép **nem használ vCenter-kiszolgálót**, el kell végeznie a fő célkiszolgálót futtató gazdagép felderítését, mielőtt ismét megvédhetné a gépet. Ez a fizikai kiszolgálók feladat-visszavételére is igaz. Ha a helyszíni virtuális gép létezik, egy másik lehetőség, ha törli azt a feladat-visszavétel előtt. A feladat-visszavétel ekkor új virtuális gépet hoz létre a fő célként megadott ESX-gazdagépen. Amikor másik helyre végez feladat-visszavételt, akkor az adatokat a helyszíni fő célkiszolgáló által használt adattárolóba és ESX-gazdagépre állítja helyre.

Nem használhatja a Storage vMotion szolgáltatást a fő célkiszolgálón. Ha mégis így tesz, a feladat-visszavétel nem működik, mert nem érhetők el számára a lemezek. Zárja ki a fő célkiszolgálókat a vMotion-listából.

>[!Warning]
>Ha másik fő célkiszolgálóval védi meg ismét a replikációs csoportokat, a kiszolgáló nem tud megállapítani közös időpontot.

## <a name="reprotect-azure-vms"></a>Az Azure-beli virtuális gépek ismételt védelme

Az Azure-beli virtuális gépek ismételt védelme az adatok helyszíni virtuális gépekre történő replikációjához vezet. Ez a lépés kötelező az Azure-ból a helyszíni virtuális gépekre irányuló feladatátvétel végrehajtása előtt. Az ismételt védelem végrehajtásához kövesse az alábbi utasításokat.

1. A **Beállítások** > **Replikált elemek** területen kattintson a jobb gombbal arra a virtuális gépre, amelyhez feladatátvételt végzett > **Ismételt védelem**.
2. Az **Ismételt védelem** területen ellenőrizze, hogy be legyen jelölve az **Azure-ról a helyszíni rendszerre** lehetőség.
3. Adja meg a helyszíni fő célkiszolgálót és a folyamatkiszolgálót.
4. Az **Adattároló** területen válassza ki azt a fő cél adattárolót, amelyre a lemezeket a helyszínen helyre szeretné állítani. Ha a virtuális gép törölve lett, a rendszer új lemezeket hoz létre ezen az adattárolón. Ezt a beállítást a rendszer figyelmen kívül hagyja, ha a lemez már létezik, de az értéket meg kell adnia.
5. Válassza ki a fő célkiszolgáló adatmegőrzési meghajtóját. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
6. Kattintson az **OK** gombra az ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát.
7. Miután a virtuális gép állapota a **Replikált elemek** között **Védett** értékre vált, a gép készen áll a helyszíni feladatátvételre.

> [!NOTE]
> Az Azure-beli virtuális gép helyreállítható egy létező helyszíni virtuális gépre vagy egy másik helyre. További információért tekintse meg [ezt a cikkeket](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Az Azure-ból a helyszíni rendszerre irányuló feladatátvétel futtatása

A helyszíni rendszerre való újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a házirend automatikusan létrejött, amikor replikációs szabályzatot hozott létre az Azure-ba végzett replikációhoz:

- A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.
- A szabályzatot nem lehet módosítani.
- A szabályzat értékei a következők:
    - Helyreállítási időkorlát küszöbértéke = 15 perc
    - Helyreállítási pont megőrzése = 24 óra
    - Alkalmazáskonzisztens pillanatkép gyakorisága = 60 perc

A következőképpen futtassa a feladatátvételt:

1. A **Replikált elemek** lapon kattintson a jobb gombbal a gépre > **Feladatátvétel**.
2. A **Feladatátvétel megerősítése** területen ellenőrizze, hogy az irány az Azure-ból a helyszíni rendszerre mutat.
    ![feladatátvétel-iránya](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot. Az alkalmazáskonzisztens helyreállítási pont időpontja a legújabb időpont elé esik, és ez némi adatvesztést okoz.

    >[!WARNING]
    >A feladatátvétel futtatásakor a Site Recovery leállítja az Azure-beli virtuális gépeket, és elindítja a helyszíni virtuális gépet. Némi állásidővel kell számolni, ezért válasszon megfelelő időpontot.

4. A feladat előrehaladását a **Helyreállítási tár** > **Figyelés és jelentéskészítés** > **Site Recovery-feladatok** menüpontban követheti nyomon.
5. A feladatátvétel teljesítését követően kattintson a jobb gombbal a gépre, majd kattintson a **Véglegesítés** parancsra. Ez elindítja az Azure-beli virtuális gépeket eltávolító feladatot.
6. Győződjön meg arról, hogy Azure-beli virtuális gépek a várt módon álltak le.

## <a name="reprotect-on-premises-machines-to-azure"></a>Helyszíni gépek ismételt védelme az Azure-ban

Az adatoknak most a helyszíni helyen vannak, de nincsenek az Azure-ba replikálva. A következőképpen indíthatja el ismét a replikációt az Azure-ba:

1. A tárolóban a **Védett elemek**  >**Replikált elemek** területen válassza ki a feladatátvételt végzett virtuális gépet, majd kattintson az **Ismételt védelem** lehetőségre.
2. Válassza ki azt a folyamatkiszolgálót, amellyel a replikált adatokat az Azure-ba küldi, majd kattintson az **OK** gombra.

Az ismételt védelem befejezése után a virtuális gép az Azure-ba replikál, és szükség szerint futtathat feladatátvételt.
