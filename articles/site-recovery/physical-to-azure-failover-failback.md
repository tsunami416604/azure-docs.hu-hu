---
title: Fizikai kiszolgálók feladatátvételének és feladat-visszavételének beállítása Site Recovery
description: Ismerje meg, hogyan hajthatja végre a fizikai kiszolgálók feladatátvételét az Azure-ba, és hogyan térhet vissza a helyszíni helyre a vész-helyreállításhoz Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 2c0d2e57a34286f65be45a95403a32de42c51908
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084575"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Az Azure-ba replikált fizikai kiszolgálók feladatátvétele és feladatátvétele

Ez az oktatóanyag leírja, hogyan végezhet feladatátvételt egy fizikai kiszolgálón az Azure-ban. A feladatátvételt követően a kiszolgáló a rendelkezésre állása után visszatérhet a helyszíni helyhez.

## <a name="preparing-for-failover-and-failback"></a>Felkészülés a feladatátvételre és a feladat-visszavételre

Az Azure-ba replikált fizikai kiszolgálók Site Recovery csak a VMware virtuális gépeken tudnak visszatérni. A feladat-visszavétel érdekében VMware-infrastruktúrára van szükség.

A feladatátvétel és a feladat-visszavétel négy fázisból áll:

1. **Feladatátvétel az Azure-ba**: Gépek átvétele a helyszíni helyről az Azure-ba.
2. **Azure-beli virtuális gépek újravédése**: az Azure-beli virtuális gépek ismételt védetté tételével megkezdheti a replikálást a helyszíni VMWare virtuális gépekre.
3. **Feladatátvétel a helyszínre**: Feladatátvétel futtatása az Azure-ból való feladat-visszavételhez.
4. Helyszíni **virtuális gépek ismételt védelme**: az adatkezelést követően újra kell védetté tenni azokat a helyszíni VMWare virtuális gépeket, amelyekről a művelet sikertelen volt, így az Azure-ba történő replikáció megkezdődik.

## <a name="verify-server-properties"></a>Kiszolgáló tulajdonságainak ellenőrzése

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy az megfelel az Azure-beli virtuális gépekre [vonatkozó Azure-követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines) .

1. A **védett elemek**területen kattintson a **replikált elemek**elemre, majd válassza ki a gépet.

2. A **replikált elem** ablaktáblán a számítógép adatai, az állapot és a legújabb elérhető helyreállítási pontok összegzése látható. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat**területen módosíthatja az Azure-nevet, az erőforráscsoportot, a célként megadott méretet, a [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md)csoportot és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **lemezekben**a számítógép operációs rendszerével és az adatlemezekkel kapcsolatos információk láthatók.

## <a name="run-a-failover-to-azure"></a>Feladatátvétel futtatása az Azure-ban

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbb feldolgozott**: Ez a beállítás feladatátvételt hajt végre a gépen a site Recovery által feldolgozott legutóbbi helyreállítási pontra. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a gépet a site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a site Recovery a feladatátvétel elindítása előtt megpróbálja leállítani a forrásoldali gépet. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.
5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel megkezdése előtt a gép replikációja leáll. Ha megszakítja a feladatátvételt, leáll, de a gép nem replikálódik újra.
> Fizikai kiszolgálók esetében a feladatátvétel további feldolgozása nyolc – tíz percet vesz igénybe.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvételt követően RDP vagy SSH segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket, [itt](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="create-a-process-server-in-azure"></a>Folyamatkiszolgáló létrehozása az Azure-ban

A folyamatkiszolgáló adatokat fogad az Azure-beli virtuális gépről, és elküldi azokat a helyszíni helyre. Kis késleltetésű hálózatra van szükség a Process kiszolgáló és a védett gép között.

- Ha Azure ExpressRoute-kapcsolata van, tesztelési célból használhatja a konfigurációs kiszolgálóra automatikusan telepített helyszíni folyamatkiszolgálót.
- Ha VPN-kapcsolata van, vagy ha éles környezetben futtat feladat-visszavételt, a feladat-visszavételhez be kell állítania egy Azure-beli virtuális gépet Azure alapú folyamatkiszolgálóként.
- Az [ebben a cikkben](vmware-azure-set-up-process-server-azure.md) található útmutatást követve állítson be egy Process Servert az Azure-ban.

## <a name="configure-the-master-target-server"></a>A fő célkiszolgáló konfigurálása

Alapértelmezés szerint a fő célkiszolgáló fogadja a feladat-visszavételi adatokból. A helyszíni konfigurációs kiszolgálón fut.

- Ha a VMware virtuális gép, amelyre a feladatátvételt végzi, VMware vCenter Server által felügyelt ESXi-gazdagépen található, a fő célkiszolgáló számára elérhetőnek kell lennie a virtuális gép adattárához (VMDK), hogy a replikált adatok a virtuálisgép-lemezekre legyenek írhatók. Győződjön meg arról, hogy a virtuális gép adattárolója olvasási/írási hozzáféréssel van-e csatlakoztatva a fő célkiszolgáló gazdagépéhez.
- Ha a vCenter-kiszolgáló által nem kezelt ESXi-gazdagépen a Site Recovery szolgáltatás új virtuális gépet hoz létre az ismételt védelem során. A virtuális gép az ESX-gazdagépen jön létre, amelyen létrehozta a fő célként megadott virtuális gépet. A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.
- A nem visszaadott fizikai gépek esetén a számítógép ismételt védetté megkezdése előtt el kell végeznie annak a gazdagépnek a felderítését, amelyen a fő célkiszolgáló fut.
- Egy másik lehetőség, ha a helyszíni virtuális gép már létezik a feladat-visszavételhez, törölje a feladat-visszavétel előtt. A feladat-visszavétel ekkor új virtuális gépet hoz létre a fő célként megadott ESX-gazdagépen. Amikor másik helyre végez feladat-visszavételt, akkor az adatokat a helyszíni fő célkiszolgáló által használt adattárolóba és ESX-gazdagépre állítja helyre.
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
