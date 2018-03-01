---
title: "Az Azure-ba replikált VMware virtuális gépek és fizikai kiszolgálók feladatátvétele és feladat-visszavétele a Site Recoveryvel | Microsoft Docs"
description: "Tekintse meg, hogy a VMware virtuális gépek és fizikai kiszolgálók feladatait hogyan veheti át az Azure-ban, hogyan veheti vissza a feladatokat a helyszíni kiszolgálókra az Azure Site Recoveryvel"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dec4beef44e4086e1b41e3c20bea6585db385c9c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
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

Ez az oktatóanyag egy sorozat ötödik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait.

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](vmware-azure-tutorial-prepare-on-premises.md)
3. [Vészhelyreállítás beállítása](vmware-azure-tutorial.md)
4. [Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Felkészülés a feladatátvételre és a feladat-visszavételre

Gondoskodjon arról, hogy a virtuális gépeken ne legyenek pillanatképek. A helyszíni virtuális gép ki van kapcsolva az ismételt védelem alatt.
Ez elősegíti az adatok konzisztenciáját a replikáció során. Ne kapcsolja be a virtuális gépet az ismételt védelem befejeződése után. Ugyanazt a fő célkiszolgálót használja a replikációs csoportok ismételt védelméhez. Ha másik fő célkiszolgálóval védi meg ismét a replikációs csoportokat, a kiszolgáló nem tud megállapítani közös időpontot.

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-ba**: Gépek átvétele a helyszíni helyről az Azure-ba.
2. **Azure-beli virtuális gépek ismételt védelme**: Az Azure-beli virtuális gépek ismételt védelme, hogy megkezdődhessen a visszareplikálás a helyszíni VMware virtuális gépekre.
3. **Feladatátvétel a helyszínre**: Feladatátvétel futtatása az Azure-ból való feladat-visszavételhez.
4. **Helyszíni virtuális gépek ismételt védelme**: Az adatok visszavétele után a visszavételben érintett helyszíni virtuális gépek ismételt védelme, hogy megkezdődhessen a replikálás az Azure-ba.

## <a name="verify-vm-properties"></a>A virtuális gép tulajdonságainak ellenőrzése

Ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.

2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.

3. A **Számítás és hálózat** területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célméretet, a [rendelkezésre állási csoportot](../virtual-machines/windows/tutorial-availability-sets.md) és a [felügyelt lemez beállításait](#managed-disk-considerations)

4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.

5. A **Lemezek** résznél láthatja a virtuális gépen lévő operációsrendszer- és adatlemezekkel kapcsolatos információkat.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.

2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb** (alapértelmezett): Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a lehetőség a virtuális gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a lehetőség a virtuális gépet a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza a **Gép leállítása a feladatátvétel elkezdése előtt.** lehetőséget, ha azt szeretné, hogy a rendszer megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.

5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll.
> Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a fizikai kiszolgálókon, a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Folyamatkiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure-beli virtuális gépről, és elküldi azokat a helyszíni helyre. Kis késleltetésű hálózatra van szükség a folyamatkiszolgáló és a védett virtuális gép között.

- Ha Azure ExpressRoute-kapcsolata van, tesztelési célból használhatja a konfigurációs kiszolgálóra automatikusan telepített helyszíni folyamatkiszolgálót.
- Ha VPN-kapcsolata van, vagy ha éles környezetben futtat feladat-visszavételt, a feladat-visszavételhez be kell állítania egy Azure-beli virtuális gépet Azure alapú folyamatkiszolgálóként.
- A folyamatkiszolgálók Azure-ban való beállításához kövesse az [ebben a cikkben](site-recovery-vmware-setup-azure-ps-resource-manager.md) szereplő utasításokat.

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálása

Alapértelmezés szerint a fő célkiszolgáló a helyszíni konfigurációs kiszolgálón fut. A jelen oktatóanyag esetén használja az alapértelmezett fő célkiszolgálót. A fő célkiszolgáló fogadja a feladat-visszavételi adatokat.

Ha a virtuális gép vCenter-kiszolgáló által kezelt ESXi-gazdagépen van, akkor a fő célkiszolgálónak hozzá kell férnie a virtuális gép adattárolójához (VMDK), hogy a replikált adatokat a virtuális gépek lemezeire írja. Győződjön meg arról, hogy a virtuális gép adattárolója olvasási/írási hozzáféréssel van-e csatlakoztatva a fő célkiszolgáló gazdagépéhez.

Ha a virtuális gép nem vCenter-kiszolgáló által kezelt ESXi-gazdagépen van, akkor a Site Recovery szolgáltatás új virtuális gépet hoz létre az ismételt védelem során. A virtuális gép azon az ESX-gazdagépen jön létre, amelyen a fő célkiszolgálót hozta létre.
A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.

Ha a virtuális gép nem használ vCenter-kiszolgálót, el kell végeznie a fő célkiszolgálót futtató gazdagép felderítését, mielőtt ismét megvédhetné a gépet. Ez a fizikai kiszolgálók feladat-visszavételére is igaz. Ha a helyszíni virtuális gép létezik, egy másik lehetőség, ha törli azt a feladat-visszavétel előtt. A feladat-visszavétel ekkor új virtuális gépet hoz létre a fő célként megadott ESX-gazdagépen. Amikor másik helyre végez feladat-visszavételt, akkor az adatokat a helyszíni fő célkiszolgáló által használt adattárolóba és ESX-gazdagépre állítja helyre.

Nem használhatja a Storage vMotion szolgáltatást a fő célkiszolgálón. Ha mégis így tesz, a feladat-visszavétel nem működik, mert nem érhetők el számára a lemezek. Zárja ki a fő célkiszolgálókat a vMotion-listából.

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
