---
title: A VMware virtuális gépek engedélyezése a vész-helyreállításhoz Azure Site Recovery használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a VMware VM-replikáció a vész-helyreállításhoz a Azure Site Recovery szolgáltatás használatával
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363058"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Az Azure-ba történő replikáció engedélyezése VMware virtuális gépekhez

Ez a cikk azt ismerteti, hogyan engedélyezhető a helyszíni VMware virtuális gépek replikálása az Azure-ba.

## <a name="resolve-common-issues"></a>Gyakori problémák megoldása

* Minden lemeznek 4 TB-nál kisebbnek kell lennie.
* Az operációsrendszer-lemeznek alaplemeznek kell lennie, nem dinamikus lemeznek.
* A 2. és UEFI-kompatibilis virtuális gépek esetében az operációsrendszer-családnak Windows rendszernek kell lennie, és a rendszerindító lemeznek a 300 GB-nál kisebbnek kell lennie.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik:

- [A helyszíni forrás környezet beállítása](vmware-azure-set-up-source.md).
- [Állítsa be a célként megadott környezetet az Azure-ban](vmware-azure-set-up-target.md).
- Mielőtt elkezdené, [ellenőrizze a követelményeket és az előfeltételeket](vmware-physical-azure-support-matrix.md) . Fontos megjegyezni a következőket:
    - A replikált gépek [támogatott operációs rendszerei](vmware-physical-azure-support-matrix.md#replicated-machines) .
    - [Tárterület-/lemez-](vmware-physical-azure-support-matrix.md#storage) támogatás.
    - Az [Azure-követelmények](vmware-physical-azure-support-matrix.md#azure-vm-requirements) , amelyeknek a helyszíni számítógépeknek meg kell felelniük.


## <a name="before-you-start"></a>Előkészületek
A VMware virtuális gépek replikálásakor tartsa szem előtt ezeket az információkat:

* Az Azure-beli felhasználói fióknak bizonyos [engedélyekkel](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell rendelkeznie az új virtuális gép Azure-ba való replikálásának engedélyezéséhez.
* A VMware virtuális gépek 15 percenként észlelhetők. A virtuális gépek a felderítést követően akár 15 percet is igénybe vehetnek a Azure Portal. Hasonlóképpen, a felderítés akár 15 percet is igénybe vehet, amikor új vCenter-kiszolgálót vagy vSphere-gazdagépet ad hozzá.
* A virtuális gép (például a VMware-eszközök telepítése) a portálon való frissítéséhez 15 percet vagy hosszabb időt is igénybe vehet.
* A VMware virtuális gépek legutóbb felderített idejét a következő helyen tekintheti meg: a vCenter-kiszolgáló/vSphere-gazdagép **konfigurációs kiszolgálók** lapjának **utolsó elérhetősége** mezőjében.
* Ha virtuális gépeket szeretne hozzáadni a replikáláshoz anélkül, hogy meg kellene várni az ütemezett felderítést, jelölje ki a konfigurációs kiszolgálót (de ne kattintson rá), majd válassza a **frissítés**lehetőséget.
* Ha engedélyezi a replikációt, a virtuális gép előkészítésekor a Process Server automatikusan telepíti a Azure Site Recovery mobilitási szolgáltatást.

## <a name="enable-replication"></a>A replikáció engedélyezése

Mielőtt elkezdené az ebben a szakaszban leírt lépéseket, jegyezze fel a következő információkat:
* A Azure Site Recovery mostantól közvetlenül a felügyelt lemezekre replikálja az összes új replikálást. A Process Server a replikációs naplókat a célként megadott régióban lévő cache Storage-fiókba írja. Ezek a naplók a asrseeddisk elnevezési konvencióval rendelkező replika felügyelt lemezeken található helyreállítási pontok létrehozására szolgálnak.
* A felügyelt lemezekre történő replikálás PowerShell-támogatása az [az. recoveryservices szolgáltatónál modul Version 2.0.0-től](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) érhető el. 
* A feladatátvétel időpontjában a kiválasztott helyreállítási pont a célként kezelt lemez létrehozásához használatos.
* Azok a virtuális gépek, amelyek korábban a cél Storage-fiókokra való replikálásra vannak konfigurálva, nem érintettek.
* Az új virtuális gépekhez tartozó Storage-fiókokba való replikálás csak reprezentációs állapot-átadási (REST) API-n és Powershellen keresztül érhető el. A Storage-fiókokra való replikáláshoz használja az Azure REST API 2016-08-10-es vagy 2018-01-10-es verzióját.

A replikáció engedélyezéséhez kövesse az alábbi lépéseket:
1. Folytassa a **2. lépés: az alkalmazás** > **forrásának**replikálása című témakört. Miután az első alkalommal engedélyezte a replikálást, a tárolóban válassza a **+ replikálás** lehetőséget a további virtuális gépek replikálásának engedélyezéséhez.
2. A **forrás** oldalon > **forrás**lapon válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa**beállításnál válassza a **Virtual Machines** vagy a **fizikai gépek**lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem vonatkozik a fizikai számítógépek replikálására.
5. Válassza ki a folyamat-kiszolgálót. Ha nem hozott létre további folyamat-kiszolgálókat, a konfigurációs kiszolgáló beépített folyamat-kiszolgálója lesz elérhető a legördülő menüben. Az egyes folyamatok kiszolgálóinak állapota ajánlott korlátként és egyéb paraméterekként van megjelölve. Válassza ki az egészséges folyamat kiszolgálóját. Nem lehet kiválasztani egy [kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) Process Servert. A hibák [elhárításához és megoldásához,](vmware-physical-azure-troubleshoot-process-server.md) **illetve** a [kibővíthető folyamat kiszolgálójának](vmware-azure-set-up-process-server-scale.md)beállításához is használható.
    ![replikációs forrás engedélyezése ablak](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> A [9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)-es verziók további riasztásokat vezetnek be a Process Server állapotára vonatkozó riasztások javítására. Frissítse Site Recovery összetevőket a 9,24-es vagy újabb verzióra a riasztások generálásához.

6. A **cél**mezőben válassza ki azt az előfizetést és erőforráscsoportot, amelyben létre szeretné hozni a feladatátvétel alatt lévő virtuális gépeket. Válassza ki azt a telepítési modellt, amelyet az Azure-ban szeretne használni a feladatátvétel alatt álló virtuális gépekhez.
2. Válassza ki azt az Azure-hálózatot és-alhálózatot, amelyhez az Azure virtuális gépek csatlakozni fognak a feladatátvételt követően. A hálózatnak ugyanabban a régióban kell lennie, mint a Site Recovery Service Vault.

   Válassza a **beállítás most a kijelölt gépekhez** lehetőséget, hogy a hálózati beállítást a védelemre kiválasztott összes virtuális gépre alkalmazza. Válassza a **Konfigurálás később** lehetőséget az Azure-hálózat virtuális gépen való kiválasztásához. Ha nincs hálózata, létre kell hoznia egyet. Ha Azure Resource Manager használatával szeretne hálózatot létrehozni, válassza az **új létrehozása**lehetőséget. Válassza ki az alhálózatot, ha van ilyen, majd kattintson **az OK gombra**.
   
   ![Replikálási cél ablakának engedélyezése](./media/vmware-azure-enable-replication/enable-rep3.png)

1. **Virtuális gépeknél** > **válassza a virtuális gépek lehetőséget**, és válassza ki a replikálni kívánt virtuális gépeket. Csak olyan virtuális gépeket választhat, amelyeken engedélyezhető a replikáció. Ezután kattintson az **OK** gombra. Ha nem látja vagy nem jelöl ki egy adott virtuális gépet, a probléma megoldásához tekintse meg [a forrásoldali gép nem szerepel a Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) .

    ![Replikáció engedélyezése – virtuális gépek kiválasztása ablak](./media/vmware-azure-enable-replication/enable-replication5.png)

1. A **tulajdonságok** > tulajdonságok **konfigurálása**területen válassza ki azt a fiókot, amelyet a feldolgozó kiszolgáló a site Recovery mobilitási szolgáltatás automatikus telepítéséhez használ a virtuális gépen. Azt is válassza ki, hogy milyen típusú felügyelt lemezt szeretne replikálni az adatváltozási minták alapján.
10. Alapértelmezés szerint a forrásként szolgáló virtuális gép összes lemeze replikálódik. Ha ki szeretné zárni a lemezeket a replikációból, törölje a **Belefoglalás** jelölőnégyzetet minden olyan lemez esetében, amelyet nem szeretne replikálni. Ezután kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. További információ a [lemezek kizárásáról](vmware-azure-exclude-disk.md).

    ![Replikálási tulajdonságok konfigurálásának engedélyezése ablak](./media/vmware-azure-enable-replication/enable-replication6.png)

1. A replikációs **beállítások** > a **replikációs beállítások konfigurálása**területen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. A replikációs házirend beállításait a **beállítások** > **replikációs házirendek** > ***Házirend neve*** > beállítások **szerkesztése**lehetőségre módosíthatja. A házirendre alkalmazott módosítások a replikálás és az új virtuális gépek esetében is érvényesek.
1. Engedélyezze a **több virtuális gépre kiterjedő konzisztenciát** , ha virtuális gépeket szeretne összegyűjteni egy replikációs csoportba. Adja meg a csoport nevét, majd kattintson **az OK gombra**.

    > [!NOTE]
    >    * A replikációs csoportban lévő virtuális gépek együtt replikálódnak, és megosztott összeomlás-konzisztens és alkalmazás-konzisztens helyreállítási pontokat biztosítanak a feladatátvétel során.
    >    * Összegyűjtheti a virtuális gépeket és a fizikai kiszolgálókat, hogy azok tükrözze a számítási feladatokat. A több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a munkaterhelés teljesítményére. Ezt csak akkor hajtsa végre, ha a virtuális gépek ugyanazt a számítási feladatot futtatják, és konzisztensnek kell lenniük.

    ![Replikálási ablak engedélyezése](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Válassza ki a **Replikáció engedélyezése** elemet. A **védelem engedélyezése** feladat előrehaladását a **beállítások** > **feladatok** > **site Recovery feladatok**részén követheti nyomon. A **védelem véglegesítése** feladatok futtatása után a virtuális gép készen áll a feladatátvételre.

## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizze a forrás virtuális gép tulajdonságait. Ne feledje, hogy az Azure-beli virtuális gép nevének meg kell felelnie az Azure-beli [virtuális gépek követelményeinek](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Lépjen a **beállítások** > **replikált elemek**elemre, majd válassza ki a virtuális gépet. Az **Essentials** oldal a virtuális gép beállításairól és állapotáról jelenít meg információkat.
1. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
1. A **számítási és hálózati** > **számítási tulajdonságok**között több virtuálisgép-tulajdonságot is módosíthat. 

    ![Számítási és hálózati Tulajdonságok ablak](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure-beli virtuális gép neve: ha szükséges, módosítsa a nevet az Azure-követelmények teljesítéséhez.
    * Cél virtuálisgép-méret vagy virtuálisgép-típus: az alapértelmezett virtuálisgép-méretet a rendszer néhány olyan paraméter alapján választja ki, amely tartalmazza a lemezek darabszámát, a hálózati adapterek darabszámát, a CPU-mag darabszámát, a memóriát és a rendelkezésre álló virtuálisgép-szerepkörök méreteit Azure Site Recovery kiválasztja az első elérhető virtuálisgép-méretet, amely megfelel az összes feltételnek. A feladatátvétel előtt bármikor kiválaszthat egy másik virtuálisgép-méretet az igényei szerint. Vegye figyelembe, hogy a virtuális gép lemezének mérete a forrás lemez méretétől is függ, és csak a feladatátvétel után módosítható. További információk a [Windows rendszerű virtuálisgép-lemezek méretezhetőségére és teljesítményére vonatkozó](../virtual-machines/windows/disk-scalability-targets.md)IOPS-méretekről és a díjszabásról.

    *  Erőforráscsoport: kiválaszthat egy [erőforráscsoportot](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), amelyből a virtuális gép a feladatátvétel utáni részévé válik. Ez a beállítás a feladatátvétel előtt bármikor módosítható. Ha a feladatátvételt követően áttelepíti a virtuális gépet egy másik erőforráscsoporthoz, az adott virtuális gép védelmi beállításai is megszakadnak.
    * Rendelkezésre állási Csoport: kiválaszthatja a [rendelkezésre állási készletet](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) , ha a virtuális gépnek egy feladatátvétel utáni résznek kell lennie. A rendelkezésre állási csoport kiválasztásakor tartsa szem előtt a következő információkat:

        * Csak a megadott erőforráscsoporthoz tartozó rendelkezésre állási csoportok szerepelnek a felsorolásban.  
        * A különböző virtuális hálózatokon lévő virtuális gépek nem lehetnek ugyanannak a rendelkezésre állási csoportnak a részei.
        * Csak azonos méretű virtuális gépek lehetnek egy rendelkezésre állási csoport részei.
1. Az Azure-beli virtuális géphez hozzárendelt célként megadott hálózattal, alhálózattal és IP-címmel kapcsolatos információkat is hozzáadhat.
2. A **lemezeken**a replikálni kívánt virtuális gépen található operációs rendszer és adatlemezek láthatók.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

A cél IP-címe beállítható. Ha nem ad meg címet, a feladatátvételen átadott virtuális gép DHCP-t használ. Ha olyan címeket állít be, amely nem érhető el a feladatátvétel során, a feladatátvétel nem működik. Ha a cím elérhető a feladatátvételi teszt hálózaton, ugyanazt a cél IP-címet használhatja a feladatátvételi teszthez.

A hálózati adapterek számát a cél virtuális géphez megadott méret határozza meg, a következőképpen:

- Ha a forrás virtuális gépen lévő hálózati adapterek száma kisebb vagy egyenlő, mint a célként megadott virtuális gép méretéhez engedélyezett adapterek száma, a célként megadott számú adapter megegyezik a forrással.
- Ha a forrás virtuális géphez tartozó adapterek száma meghaladja a cél virtuális gép méretéhez engedélyezett számot, a rendszer a célként megadott maximális méretet használja. Ha például a forrás virtuális gép két hálózati adapterrel rendelkezik, és a cél virtuális gép mérete négyet támogat, a célként megadott virtuális gép két adapterrel rendelkezik. Ha a forrásoldali virtuális gép két adapterrel rendelkezik, de a célként megadott méret csak egyet támogat, a célként megadott virtuális gépnek csak egy adaptere van.
- Ha a virtuális gépnek több hálózati adaptere van, akkor mind ugyanahhoz a hálózathoz csatlakoznak. Emellett a listában látható első adapter lesz az *alapértelmezett* hálózati adapter az Azure-beli virtuális gépen. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft frissítési garanciával rendelkező ügyfelei az Azure-ba migrált Windows Server rendszerű számítógépek licencelési költségeinek mentését Azure Hybrid Benefit használhatják. Az előny az Azure vész-helyreállításra is vonatkozik. Ha Ön jogosult, hozzárendelheti az előnyt ahhoz a virtuális géphez, amelyet Site Recovery hoz létre, ha van feladatátvétel. Ehhez kövesse az alábbi lépéseket:
1. Nyissa meg a replikált virtuális gép **számítógép-és hálózati tulajdonságait** .
2. Válasz, ha a rendszer megkérdezi, hogy van-e olyan Windows Server-licence, amely jogosult a Azure Hybrid Benefitre.
3. Győződjön meg arról, hogy jogosult Windows Server-licenccel rendelkezik frissítési garanciával, amely a feladatátvétel során létrehozandó virtuális gépre való juttatás alkalmazására használható.
4. Mentse a replikált virtuális gép beállításait.

További információ a [Azure Hybrid Benefitról](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép elérte a védett állapotot, próbálja meg a [feladatátvételt](site-recovery-failover.md) , és győződjön meg arról, hogy az alkalmazás megjelenik az Azure-ban.

* Megtudhatja, hogyan törölheti a [regisztrációt és a védelmi beállításokat](site-recovery-manage-registration-and-protection.md) a replikáció letiltásához.
* Ismerje meg, hogyan [automatizálhatja a virtuális gépek replikálását a PowerShell használatával](vmware-azure-disaster-recovery-powershell.md).
