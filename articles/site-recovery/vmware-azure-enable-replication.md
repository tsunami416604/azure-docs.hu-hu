---
title: Vészhelyreállítás az Azure-bA az Azure Site Recovery VMware virtuális gépek replikálásának engedélyezése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-bA vész-helyreállítási VMware virtuális gépek engedélyezése az Azure Site Recovery használatával.
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/6/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 472ff7810852bd03ef322cd5eb647c3d61f09b01
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418108"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Az Azure-bA VMware virtuális gépek replikálásának engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a helyszíni VMware virtuális gépeket az Azure-bA replikálását.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik:

- [A helyszíni forráskörnyezet beállítása](vmware-azure-set-up-source.md).
- [Az Azure-ban a célkörnyezet beállítása](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Előkészületek
Ha VMware virtuális gépeket replikál, tartsa szem előtt ezeket az információkat:

* Az Azure felhasználói fióknak rendelkeznie kell bizonyos [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) engedélyezni a replikációt egy új virtuális gép az Azure-bA.
* VMware virtuális gépeket a felderítésüket 15 percenként. 15 percig is eltarthat, vagy hosszabb, a virtuális gépek felderítése után jelennek meg az Azure Portalon. Hasonlóképpen, felderítési 15 percbe is telhet, vagy hosszabb, amikor hozzáad egy új vCenter-kiszolgáló vagy vSphere gazdagépre.
* 15 percig is eltarthat, vagy hosszabb ideig környezetet a virtuális gépek változásaival kapcsolatos (például a VMware-eszközök telepítése) frissíteni kell a portálon.
* A legutóbb felfedezett ideje a VMware virtuális gépekhez ellenőrizheti: Tekintse meg a **utolsó forduljon,** mezőjében a **konfigurációs kiszolgálók** lapját a vCenter-kiszolgáló vagy vSphere-gazdagépen.
* Virtuális gépek replikációjának az ütemezett felderítést várakozás nélkül hozzáadásához jelölje ki a konfigurációs kiszolgáló (de ne kattintson rá), és válassza ki **frissítése**.
* Replikációs, ha a virtuális gépet pedig előkészíti engedélyezésekor a folyamatkiszolgáló automatikusan telepíti az Azure Site Recovery mobilitási szolgáltatás rajta.

## <a name="enable-replication"></a>A replikáció engedélyezése

Mielőtt végrehajtaná a jelen szakaszban ismertetett lépéseket, vegye figyelembe a következő információkat:
* Az Azure Site Recovery most már közvetlenül az összes új replikációk felügyelt lemezek replikálja. A folyamatkiszolgáló replikációs naplók ír a gyorsítótárfiók a célrégióban. Ezek a naplók segítségével hozza létre a replikált felügyelt lemezeken lévő helyreállítási pontokat.
* A feladatátvétel időpontjában a kiválasztott helyreállítási pont szolgál a célként szolgáló felügyelt lemez létrehozása.
* Virtuális gépek replikálása céltárfiókokat korábban beállított szabályzat nem vonatkozik.
* Egy új virtuális gép tárfiókokba történő replikálást csak akkor használható, a Representational State Transfer (REST) API-t és a Powershell használatával. Azure REST API-verzió 2016-08-10-es vagy a 2018-01-10 használja a storage-fiókokhoz való replikálásához.

1. Lépjen a **2. lépés: Alkalmazás replikálása** > **forrás**. Először a replikáció engedélyezése után válassza **+ replikálás** további virtuális gépek replikációjának engedélyezéséhez a tárolóban.
1. Az a **forrás** lap > **forrás**, válassza ki a konfigurációs kiszolgálót.
1. A **gép típusa**válassza **virtuális gépek** vagy **fizikai gépek**.
1. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem megfelelő, ha fizikai számítógépeket replikál.
1. Válassza ki a folyamatkiszolgálót, amely a konfigurációs kiszolgáló lesz, ha még nem hozott létre minden olyan további folyamatkiszolgálók. Ezután kattintson az **OK** gombra.

    ![Engedélyezze a replikációs forrás ablak](./media/vmware-azure-enable-replication/enable-replication2.png)

1. A **cél**, válassza ki az előfizetést és erőforráscsoportot csoportot, ahol szeretné a átvevő virtuális gépek létrehozása. Válassza ki a feladatátviteli virtuális gépeket az Azure-ban használni kívánt központi telepítési modelljét.

1. Válassza ki az Azure-hálózatot és alhálózatot, amelyet az Azure virtuális gépek csatlakozni fognak a feladatátvétel után. A hálózat és a Site Recovery szolgáltatás-tárolónak ugyanabban a régióban kell lennie.

   Válassza ki **beállítás most a kijelölt gépekhez** a hálózati beállítások alkalmazása az összes virtuális gép, amely ki védelemre. Válassza ki **beállítás később** jelölje be az Azure-hálózat virtuális gépenként. Ha nem rendelkezik a hálózathoz, létre kell hoznia egyet. Hálózat létrehozása az Azure Resource Managerrel, jelölje be a **új létrehozása**. Egy alhálózatot, ha van ilyen, majd válassza ki és **OK**.
   
   ![Replikációs cél ablak engedélyezése](./media/vmware-azure-enable-replication/enable-rep3.png)

1. A **virtuális gépek** > **válassza ki a virtuális gépek**, válassza ki a replikálni kívánt virtuális gépek árát. Válassza ki a virtuális gépek replikációs engedélyezheti csak. Ezután kattintson az **OK** gombra. Ha nem talál, vagy jelölje ki bármely adott virtuális gép, tekintse meg [forrásgép nem szerepel a listán az Azure Portalon](https://aka.ms/doc-plugin-VM-not-showing) a probléma megoldásához.

    ![Válassza ki a virtuális gépek ablak replikáció engedélyezése](./media/vmware-azure-enable-replication/enable-replication5.png)

1. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a fiókot, amely a folyamatkiszolgálót használja automatikusan telepíteni a Site Recovery mobilitási szolgáltatás a virtuális gépen. Ezenkívül válassza ki a cél felügyelt lemez adatváltozás-minták az adatok alapján történő replikálásához.
10. Rendszer alapértelmezés szerint a forrás virtuális gép minden lemezét replikálja. Lemezek kizárása a replikációból, törölje a jelet a **Belefoglalás** jelölőnégyzetet az összes lemezt, amelyet replikálni szeretne. Ezután kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. Tudjon meg többet [lemezek kizárása](vmware-azure-exclude-disk.md).

    ![Engedélyezése replikáció konfigurálása a Tulajdonságok ablak](./media/vmware-azure-enable-replication/enable-replication6.png)

1. A **replikációs beállítások** > **replikációs beállítások konfigurálása**, győződjön meg arról, hogy a megfelelő replikációs szabályzat van kiválasztva. Módosíthatja a replikációs házirend beállításainak: **beállítások** > **replikációs házirendek** > ***házirendnév***  >  **Beállításainak szerkesztése**. Ha megváltoztatja egy házirend is vonatkozik a virtuális gépek replikálását és új.
1. Engedélyezése **több virtuális gépre kiterjedő konzisztencia** Ha szeretne gyűjteni a virtuális gépek egy replikációs csoporthoz. Adja meg a csoport nevét, és válassza **OK**.

    > [!NOTE]
    >    * Egy replikációs csoportban lévő virtuális gépek replikálása együtt, és amikor meghiúsulnak keresztül megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontok.
    >    * Virtuális gépek és fizikai kiszolgálók gyűjtsön össze, hogy látni lehessen a számítási feladatokat. Több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a számítási feladatok teljesítményére. Erre csak akkor, ha a virtuális gépek ugyanazt a számítási feladatot futtat, és a konzisztencia van szüksége.

    ![Replikációs ablak engedélyezése](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Válassza ki a **Replikáció engedélyezése** elemet. Az előrehaladását nyomon követheti a **védelem engedélyezése** feladat **beállítások** > **feladatok** > **Site Recovery-feladatok**. Miután a **védelem véglegesítése** feladat fut, a virtuális gép nem áll készen a feladatátvételre.

## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizze a forrás virtuális gép tulajdonságait. Ne feledje, hogy az Azure virtuális gép neve van szüksége ahhoz, hogy [Azure virtuális gépekre vonatkozó követelmények](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Lépjen a **beállítások** > **replikált elemek**, majd válassza ki a virtuális gépet. A **Essentials** lapon a virtuális gép beállításaira és állapotára vonatkozó információkat jeleníti meg.
1. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
1. A **számítás és hálózat** > **számítási tulajdonságok**, több virtuális gép tulajdonságait módosíthatja. 

    ![Számítási és hálózati tulajdonságok ablak](./media/vmware-azure-enable-replication/vmproperties.png)

    * Az Azure virtuális gép neve: Szükség esetén módosítsa a nevét, az Azure követelményeinek.
    * Virtuális célgép méretét vagy a virtuális gép típusa: A virtuális gépek alapértelmezett mérete a forrás virtuális gép méretét akkor kell kiválasztani a rendszer. Kiválaszthat egy másik Virtuálisgép-méretet, a feladatátvétel előtt bármikor igényei alapján. Vegye figyelembe, hogy a Virtuálisgép-lemez mérete a forráslemez mérete is alapul, és azt csak a feladatátvételt követően módosítható. További tudnivalók a lemezek és IOPS-díjak, [Windows virtuális gép lemezeinek méretezhetőségi és teljesítménycéljai](../virtual-machines/windows/disk-scalability-targets.md).

    *  Erőforráscsoport: Kiválaszthat egy [erőforráscsoport](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), amely egy virtuális gépet egy része lesz a feladatátvétel után a. Ezt a beállítást, a feladatátvétel előtt bármikor módosíthatja. A feladatátvételt követően a virtuális gépet telepít át egy másik erőforráscsoportban, ha a virtuális gép védelmi beállításait szünet.
    * Rendelkezésre állási csoporthoz: Kiválaszthat egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Ha a virtuális gép van szüksége lehet egy feladatátvétel után egy része. Rendelkezésre állási csoport kiválasztásakor tartsa szem előtt az alábbi adatokat:

        * Egyetlen, az adott erőforráscsoportba tartozó rendelkezésre állási csoportok jelennek meg.  
        * A különböző virtuális hálózatokon lévő virtuális gépeket nem lehet az azonos rendelkezésre állási csoport része.
        * Csak az azonos méretű virtuális gépeket egy rendelkezésre állási csoport részeként is szerepelhetnek.
1. A célként megadott hálózat, alhálózat és az Azure virtuális géphez rendelt IP-cím kapcsolatos információkat is hozzáadhat.
2. A **lemezek**, az operációs rendszer és az adatlemezek láthatja a replikálandó virtuális gépen.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

A cél IP-címe beállítható. Ne adjon meg egy címet, ha a átvevő virtuális gép DHCP használja. Ha egy címet, amely nem használható feladatátadásra, a feladatátvétel nem működik. Ha a cím elérhető a feladatátvételi teszt hálózatában, használhatja az azonos céloldali IP-cím a feladatátvételi teszthez.

A hálózati adapterek száma szabja meg méretét, hogy Ön adja meg a cél virtuális gép a következő:

- Ha a forrás virtuális gép hálózati adaptereinek száma kisebb vagy egyenlő a céloldali virtuális gép mérete engedélyezett adapterek számával, a célban adapterek azonos számú forrásaként.
- Ha a forrás virtuális gépek adaptereinek száma meghaladja az engedélyezett a céloldali virtuális gép méretét, a cél maximális használja. Például ha a forrás virtuális gép két hálózati adapterrel rendelkezik, és a céloldali virtuális gép mérete támogatja négy, a cél virtuális gépen, a két adapter. Ha a forrásoldali virtuális gép két adapterrel rendelkezik, de a támogat egy, a cél virtuális gép csak egy adapterrel rendelkezik.
- Ha a virtuális gép több hálózati adapterrel rendelkezik, az összes az ugyanazon hálózathoz csatlakoznak. Az első adapter, amely a listában látható lesz is, a *alapértelmezett* hálózati adapter az Azure-beli virtuális gépen. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft frissítési garanciával rendelkező ügyfelek az Azure Hybrid Benefit segítségével megtakarítást a Windows Server rendszerű számítógépek, amelyek az Azure-bA migrálta a licencelési költségei. Az ajánlat Azure-beli vészhelyreállításához is vonatkozik. Ha Ön jogosult, hozzárendelheti a juttatás a virtuális géphez, amely a Site Recovery hoz létre, ha feladatátvitel történik. Ehhez kövesse az alábbi lépéseket:
1. Nyissa meg a **számítógép és a hálózat tulajdonságai** a replikált virtuális gép.
2. Ha a rendszer kéri, hogy van-e egy Windows Server-licenc, amely lehetővé teszi, hogy az Azure Hybrid Benefit értékelemet választ.
3. Győződjön meg arról, hogy a frissítési garancia, amelyek segítségével a feladatátvétel során létrehozott virtuális gépre érvényes jogosult a Windows Server-licencem van.
4. A replikált virtuális gép a beállítások mentéséhez.

Tudjon meg többet [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>A leggyakoribb hibák elhárításához

* Mindegyik lemez 4 TB-nál kisebbnek kell lennie.
* Az operációsrendszer-lemezt alaplemez, nem a dinamikus lemezeket kell lennie.
* Generation 2/UEFI-kompatibilis virtuális gépek operációsrendszer-családnak Windows kell lennie, és a rendszerindító lemez 300 GB-nál kisebbnek kell lennie.

## <a name="next-steps"></a>További lépések

Miután a virtuális gép eléri a védett állapotba, próbálja meg egy [feladatátvételi](site-recovery-failover.md) ellenőrizze, hogy az alkalmazás megjelenik az Azure-ban.

* Ismerje meg, hogyan [regisztráció és a védelem beállításainak tiszta](site-recovery-manage-registration-and-protection.md) , tiltsa le a replikációt.
* Ismerje meg, hogyan [automatizálhatja a virtuális gépek replikálása Powershell-lel](vmware-azure-disaster-recovery-powershell.md).
