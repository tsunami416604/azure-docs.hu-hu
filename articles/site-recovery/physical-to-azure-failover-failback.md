---
title: Feladatátvétel és feladat-feladatátvétel beállítása fizikai kiszolgálókhoz a Site Recovery segítségével
description: Megtudhatja, hogy miként lehet átkerülni az Azure fizikai kiszolgálóit, és hogyan lehet visszaadni a helyszíni helyre vész-helyreállításhoz az Azure Site Recovery szolgáltatással
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497852"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Feladatátvétel és feladat-visszavétel az Azure-ba replikált fizikai kiszolgálók

Ez az oktatóanyag bemutatja, hogyan lehet átkerülni a helyszíni fizikai kiszolgálókat, amelyek replikálódnak az Azure-ba az [Azure Site Recovery szolgáltatással.](site-recovery-overview.md) Miután feladatátvételt, a feladat-visszavétel az Azure-ból a helyszíni hely, ha elérhetővé válik.

## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg](failover-failback-overview.md) a feladatátvételi folyamat a vész-helyreállítási.
- Ha több gépet szeretne átvenni, [ismerje meg,](recovery-plan-overview.md) hogyan gyűjthetgépeket egy helyreállítási tervben.
- Mielőtt egy teljes feladatátvétel, futtasson egy [vész-helyreállítási gyakorlat annak](site-recovery-test-failover-to-azure.md) érdekében, hogy minden a várt módon működik.
- Kövesse [ezeket az utasításokat,](site-recovery-failover.md#prepare-to-connect-after-failover) hogy előkészítse az Azure virtuális gépekhez való csatlakozást a feladatátvétel után.



## <a name="run-a-failover"></a>Feladatátvétel futtatása

### <a name="verify-server-properties"></a>Kiszolgáló tulajdonságainak ellenőrzése

Ellenőrizze a kiszolgáló tulajdonságait, és győződjön meg arról, hogy megfelel az Azure virtuális gépek [azure-követelményeinek.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. A **Védett elemek csoportban**kattintson **a Replikált elemek**elemre, és jelölje ki a gépet.
2. A **Replikált elem** ablaktáblán a gépadatok, az állapot és a legújabb elérhető helyreállítási pontok összegzése található. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **Compute and Network**alkalmazásban módosíthatja az Azure nevét, erőforráscsoportját, célméretét, [rendelkezésre állási készletét](../virtual-machines/windows/tutorial-availability-sets.md)és a felügyelt lemez beállításait.
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve azt a hálózatot/alhálózatot, amelyen az Azure-beli virtuális gép a feladatátvétel után lesz és a hozzá rendelt IP-címet.
5. A **Lemezek területen**a számítógép operációs rendszerével és adatlemezeivel kapcsolatos információk láthatók.

### <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

1. A**Replikált elemek** **beállításai** > párbeszédpanelen kattintson a **feladatátvétel**> gépre.
2. A **Feladatátvétel** csoportban válasszon egy **helyreállítási pontot,** amelynek feladatátvételre van. Az alábbi lehetőségek egyikét használhatja:
   - **Legújabb**: Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási időkorlátot (RPO) nyújtja, mert a feladatátvétel után létrehozott Azure-beli virtuális gép rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.
   - **Legutóbbi feldolgozás:** Ez a beállítás átadja a gépnek a Site Recovery által feldolgozott legújabb helyreállítási pontot. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
   - **Legújabb alkalmazáskonzisztens:** Ez a beállítás átadja a számítógépnek a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontot.
   - **Egyéni**: Adjon meg egy helyreállítási pontot.

3. Ha azt szeretné, hogy a Site Recovery a feladatátvétel megkezdése előtt próbálja meg leállítani a forrásgépet, válassza a **Számítógép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
4. Ha felkészült az Azure-beli virtuális géphez való kapcsolódáshoz, csatlakozzon a feladatátvétel utáni ellenőrzéshez.
5. Az ellenőrzés után **véglegesítse** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt. A feladatátvétel megkezdése előtt a gép replikációja leáll. Ha megszakítja a feladatátvételt, leáll, de a gép nem replikálódik újra.
> Fizikai kiszolgálók esetén a további feladatátvétel feldolgozása körülbelül 8–10 percet is igénybe vehet.

## <a name="automate-actions-during-failover"></a>Műveletek automatizálása feladatátvétel közben

Előfordulhat, hogy a feladatátvétel során szeretné automatizálni a műveleteket. Ehhez parancsfájlokat vagy Azure-automatizálási runbookokat használhat a helyreállítási tervekben.

- [További információ](site-recovery-create-recovery-plans.md) a helyreállítási tervek létrehozásáról és testreszabásáról, beleértve a parancsfájlok hozzáadását is.
- [Ismerje meg](site-recovery-runbook-automation.md) az Azure Automation runbookok hozzáadása helyreállítási tervek.

## <a name="configure-settings-after-failover"></a>Beállítások konfigurálása feladatátvétel után

Feladatátvétel után [konfigurálnia](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) kell az Azure-beállításokat a replikált Azure-beli virtuális gépekhez való csatlakozáshoz. Ezenkívül belső [és nyilvános](site-recovery-failover.md#set-up-ip-addressing) IP-címzést is be kell állítani.

## <a name="prepare-for-reprotection-and-failback"></a>Felkészülés az újravédelemre és a feladat-visszavételre

Miután átadta a feladataaz Azure-beli virtuális gépeket, és replikálja őket a helyszíni webhelyre. Ezt követően a replikálás után, akkor nem őket vissza a helyszíni, az Azure-ból a helyszíni helyről feladatátvétel futtatásával.

1. Az Azure-ba a Site Recovery használatával replikált fizikai kiszolgálók csak vmware virtuális gépekként tudnak visszalépni. A feladat-visszavételhez vmware-infrastruktúrára van szükség. A [cikkben](vmware-azure-prepare-failback.md) leírt lépéseket követve előkészítheti az újravédelmet és a feladat-visszavételt, beleértve egy folyamatkiszolgáló és egy helyszíni főcélkiszolgáló beállítását, valamint a helyek közötti VPN vagy expressroute-alapú privát társviszony-létesítés konfigurálását a feladat-visszavételhez.
2. Győződjön meg arról, hogy a helyszíni konfigurációs kiszolgáló fut, és csatlakozik az Azure-hoz. Az Azure-ba való feladatátvétel során előfordulhat, hogy a helyszíni hely nem érhető el, és a konfigurációs kiszolgáló nem érhető el, vagy leáll. A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel sikertelen.
3. Törölje a helyszíni főcélkiszolgálón lévő pillanatképeket. Az újravédelem nem működik, ha vannak pillanatképek.  A virtuális gép pillanatképei automatikusan egyesülnek egy újravédelmi feladat során.
4. Ha a több virtuális gép konzisztenciája érdekében egy replikációs csoportba gyűjtött virtuális gépeket újra védi, győződjön meg arról, hogy mindegyik azonos operációs rendszerrel (Windows vagy Linux) rendelkezik, és győződjön meg arról, hogy a telepített fő célkiszolgáló azonos típusú operációs rendszerrel rendelkezik. A replikációs csoportban lévő összes virtuális gépnek ugyanazt a fő célkiszolgálót kell használnia.
5. Nyissa meg a feladat-visszavételhez [szükséges portokat.](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)
6. Győződjön meg arról, hogy a vCenter-kiszolgáló csatlakoztatva van a feladat-visszavétel előtt. Ellenkező esetben a lemezek leválasztása és a virtuális géphez való visszacsatolása sikertelen.
7. Ha egy vCenter-kiszolgáló kezeli azokat a virtuális gépeket, amelyeknek a feladat-visszavétele lesz, győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. Ha csak olvasható felhasználói vCenter-felderítést hajt végre, és védi a virtuális gépeket, a védelem sikeres, és a feladatátvétel működik. Az újravédelem során azonban a feladatátvétel sikertelen, mert az adattárak nem fedezhetők fel, és nem jelennek meg az újravédelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait a [megfelelő fiókkal/engedélyekkel,](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)majd próbálkozzon újra a feladattal. 
8. Ha egy sablont használt a virtuális gépek létrehozásához, győződjön meg arról, hogy minden virtuális gép saját UUID-val rendelkezik a lemezekhez. Ha a helyszíni virtuális gép UUID ütközik a fő célkiszolgáló UUID, mert mindkettő ugyanabból a sablonból jött létre, az újravédelem sikertelen lesz. Üzembe helyezés egy másik sablonból.
9. Ha nem sikerül visszaegy másik vCenter-kiszolgáló, győződjön meg arról, hogy az új vCenter-kiszolgáló és a fő célkiszolgáló felderítése. Általában, ha nem az adattárak nem érhető el, vagy nem látható a **Védelem újra .**
10. Ellenőrizze a következő eseteket, amelyekben nem lehet visszakapni a feladat-visszavételt:
    - Ha az ESXi 5.5 ingyenes kiadást vagy a vSphere 6 Hypervisor ingyenes kiadást használja. Frissítés másik verzióra.
    - Ha Windows Server 2008 R2 SP1 fizikai kiszolgálóval rendelkezik.
    - [Az áttelepített](migrate-overview.md#what-do-we-mean-by-migration)virtuális gépek .
    - Egy virtuális gép, amely átlett helyezve egy másik erőforráscsoportba.
    - Egy replikát az Azure virtuális gép, amely törölték.
    - Egy replikát erdei Azure virtuális gép, amely nem védett (replikálása a helyszíni helyre).
10. Tekintse át a használható [feladat-visszavételi típusokat](concepts-types-of-failback.md) – az eredeti hely-helyreállítást és az alternatív hely-helyreállítást.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Az Azure-beli virtuális gépek újbóli védelme egy másik helyre

Ez az eljárás feltételezi, hogy a helyszíni virtuális gép nem érhető el.

1. A tárolóban > **beállítások** > **replikált elemeket**, kattintson a jobb gombbal a számítógép, amely nem sikerült > **Re-Protect**.
2. Az **Ismételt védelem** területen ellenőrizze, hogy be legyen jelölve az **Azure-ról a helyszíni rendszerre** lehetőség.
3. Adja meg a helyszíni fő célkiszolgálót és a folyamatkiszolgálót.
4. Az **Adattároló** területen válassza ki azt a fő cél adattárolót, amelyre a lemezeket a helyszínen helyre szeretné állítani.
       - Akkor használja ezt a beállítást, ha a helyszíni virtuális gép törölve lett, vagy nem létezik, és új lemezeket kell létrehoznia.
       - A rendszer figyelmen kívül hagyja ezt a beállítást, ha a lemezek már léteznek, de meg kell adnia egy értéket.
5. Válassza ki a fő célkiszolgáló adatmegőrzési meghajtóját. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
6. Kattintson az **OK** gombra az ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni az Azure virtuális gép a helyszíni helyen. A **Feladatok** lapon követheti nyomon a folyamat állapotát.

> [!NOTE]
> Ha meglévő helyszíni virtuális gépre szeretné helyreállítani az Azure-beli virtuális gépet, csatlakoztassa a helyszíni virtuális gép olvasási/írási hozzáféréssel rendelkező adattárolóját a fő célkiszolgáló ESXi-gazdagépén.


## <a name="fail-back-from-azure"></a>Feladat-visszavétel az Azure-ból

A következőképpen futtassa a feladatátvételt:

1. A **Replikált elemek** lapon kattintson a jobb gombbal a gépre > **Nem tervezett feladatátvétel**.
2. A **Feladatátvétel megerősítése** területen ellenőrizze, hogy az irány az Azure-ból a helyszíni rendszerre mutat.
3. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot.
    - Azt javasoljuk, hogy használja a **legújabb** helyreállítási pontot. Az alkalmazáskonzisztens pont a legutóbbi időpont mögött van, és némi adatvesztést okoz.
    - **A Legújabb** egy összeomlás-konzisztens helyreállítási pont.
    - A feladatátvétel futtatásakor a Site Recovery leállítja az Azure-beli virtuális gépeket, és elindítja a helyszíni virtuális gépet. Némi állásidővel kell számolni, ezért válasszon megfelelő időpontot.
4. Kattintson a jobb gombbal a gépre, és kattintson a **Véglegesítés** parancsra. Ez elindítja az Azure-beli virtuális gépeket eltávolító feladatot.
5. Győződjön meg arról, hogy Azure-beli virtuális gépek a várt módon álltak le.


## <a name="reprotect-on-premises-machines-to-azure"></a>Helyszíni gépek ismételt védelme az Azure-ban

Az adatoknak most a helyszíni helyen vannak, de nincsenek az Azure-ba replikálva. A következőképpen indíthatja el ismét a replikációt az Azure-ba:

1. A tárolóban > **Beállítások** >**Replikált elemek**, válassza ki azokat a virtuális gépeket, amelyekhez feladatátvételt végzett, és kattintson az **Ismételt védelem** lehetőségre.
2. Válassza ki azt a folyamatkiszolgálót, amellyel a replikált adatokat az Azure-ba küldi, majd kattintson az **OK** gombra.


## <a name="next-steps"></a>További lépések

Miután az újravédelmi feladat befejeződik, a helyszíni virtuális gép replikálja az Azure-ba. Szükség esetén [futtathat egy másik feladatátvételt](site-recovery-failover.md) az Azure-ba.
