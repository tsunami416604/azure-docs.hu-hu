---
title: VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recovery VMware virtuális gépek replikálásának engedélyezése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-ba, az Azure Site Recovery használata vész-helyreállítási VMware virtuális gépek replikációjának engedélyezéséhez.
author: asgang
ms.service: site-recovery
ms.date: 11/27/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 51470e9f8e0bffe18d1dc4007433246d084a5cb2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846659"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Az Azure-bA VMware virtuális gépek replikálásának engedélyezése


Ez a cikk ismerteti, hogyan engedélyezheti a helyszíni VMware virtuális gépeket az Azure-bA replikálását.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik:

1.  [A helyszíni forráskörnyezet](vmware-azure-set-up-source.md).
2.  [Az Azure-ban a célkörnyezet beállítása](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Előkészületek
VMware virtuális gépek replikálásához:

* Az Azure felhasználói fióknak rendelkeznie kell bizonyos [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) engedélyezni a replikációt egy új virtuális gép az Azure-bA.
* VMware virtuális gépeket a felderítésüket 15 percenként. 15 percbe is telhet, vagy hosszabb, hogy megjelenjenek az Azure Portalon felderítése után. Hasonlóképpen tarthat a felderítés 15 percet vagy többet egy új vCenter-kiszolgáló vagy vSphere gazdagépre hozzáadásakor.
* Környezet módosításokat a virtuális gépen (például a VMware-eszközök telepítése) is igénybe vehet, 15 percet vagy többet a portálon frissíteni kell.
* Ellenőrizheti a legutolsó felderítésének időpontját a VMware virtuális gépekhez a **utolsó, forduljon** a vCenter kiszolgálóhoz/vSphere-gazdagépen a mezőt a **konfigurációs kiszolgálók** lap.
* Az ütemezett felderítést várakozás nélkül replikáció hozzáadni, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), kattintson a **frissítése** gombra.
* A folyamatkiszolgáló replikációs, ha a gépet pedig előkészíti engedélyezi, automatikusan telepíti a mobilitási szolgáltatás rajta.


## <a name="enable-replication"></a>A replikáció engedélyezése

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. A replikálás első alkalommal történő engedélyezését követően kattintson a tárolóban elérhető **+Replikálás** elemre a további gépek replikációjának engedélyezéséhez.
2. Az a **forrás** lap > **forrás**, válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa**válassza **virtuális gépek** vagy **fizikai gépek**.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem megfelelő, ha fizikai gépeket replikál.
5. Válassza ki a folyamatkiszolgálót, amely a konfigurációs kiszolgáló neve lesz, ha még nem hozott létre minden olyan további folyamatkiszolgálók. Ezután kattintson az **OK** gombra.

    ![Engedélyezze a replikációs forrás](./media/vmware-azure-enable-replication/enable-replication2.png)

6. A **cél**, válassza ki az előfizetést és az erőforráscsoport, ahol szeretné a átvevő virtuális gépek létrehozása. Válassza ki a átvevő virtuális gépek az Azure-ban használni kívánt üzemi modellhez.

7. Válassza ki az adatok replikálásához használni kívánt Azure Storage-fiókot. 

    > [!NOTE]

    >   * Választhatja a prémium vagy standard szintű tárfiókot. Ha prémium szintű fiókot választja, adja meg a folyamatban lévő replikáció naplók egy további standard szintű tárfiók van szükség. Fiókok és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
    >   * Ha szeretne egy másik tárfiókot használja, akkor [hozzon létre egyet](../storage/common/storage-create-storage-account.md). Storage-fiók létrehozása a Resource Manager használatával, kattintson a **új létrehozása**. 

8. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. Ha a hálózat nem rendelkezik, akkor [hozzon létre egyet](#set-up-an-azure-network). Hálózat létrehozása a Resource Manager használatával, kattintson a **új létrehozása**. Válasszon egy olyan alhálózatot, ha van ilyen, és kattintson a **OK**.

    ![Replikációs cél beállítás engedélyezése](./media/vmware-azure-enable-replication/enable-rep3.png)
9. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.

    ![Replikációs válassza a virtuális gépek engedélyezése](./media/vmware-azure-enable-replication/enable-replication5.png)
10. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a fiókot használják a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.  
11. Rendszer alapértelmezés szerint minden lemezét replikálja. Lemezek kizárása a replikációból, kattintson a **az összes lemez** , és törölje az összes lemezt nem szeretné replikálni.  Ezután kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. [További](vmware-azure-exclude-disk.md) kapcsolatos lemezek kizárása.

    ![Engedélyezése replikációs tulajdonságainak konfigurálása](./media/vmware-azure-enable-replication/enable-replication6.png)

12. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva. Módosíthatja a replikációs házirend beállításainak **beállítások** > **replikációs házirendek** > (szabályzat neve) > **beállításainak szerkesztése**. Is vonatkozik egy házirend végzett módosítások gépek replikálását és új.
13. Engedélyezése **több virtuális gépre kiterjedő konzisztencia** Ha szeretne gyűjteni a gépek egy replikációs csoporthoz. Adja meg a csoport nevét, és kattintson **OK**. 

    > [!NOTE]

    >    * Egy replikációs csoportban található gépek replikálása együtt, és amikor meghiúsulnak keresztül megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokat.
    >    * Virtuális gépek és fizikai kiszolgálók gyűjtsön össze, hogy látni lehessen a számítási feladatokat. Több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a számítási feladatok teljesítményére. Csak akkor, ha a gépek ugyanazt a számítási feladatot futtat, és konzisztencia kell használni.

    ![A replikáció engedélyezése](./media/vmware-azure-enable-replication/enable-replication7.png)
14. Kattintson a **Replikáció engedélyezése** elemre. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.



## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizheti a forrásgép tulajdonságait. Ne feledje, hogy az Azure virtuális gép neve van szüksége ahhoz, hogy [Azure virtuális gépekre vonatkozó követelmények](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Kattintson a **beállítások** > **replikált elemek** >, majd válassza ki a gépet. A **Essentials** lap gép beállításaira és állapotára vonatkozó információkat jeleníti meg.
2. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
3. A **Számítás és hálózat** > **Számítási tulajdonságok** résznél adhatja meg az Azure virtuális gép nevét és a cél méretét. Módosítsa a nevét, hogy megfeleljenek az Azure követelményeinek, szükség esetén.

    ![Számítás és hálózat tulajdonságai](./media/vmware-azure-enable-replication/vmproperties.png)

4.  Kiválaszthat egy [erőforráscsoport](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) , amely egy gép részévé válik a feladatátvétel után. Ezt a beállítást, a feladatátvétel előtt bármikor módosíthatja. Közzététele a feladatátvétel, ha a gép telepít át egy másik erőforráscsoportban található, a gép break védelmi beállításait.
5. Kiválaszthat egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Ha a gép van szüksége lehet egy feladatátvétel után része. Bár a rendelkezésre állási választjuk, vegye figyelembe, hogy:

    * Csak a megadott erőforrás-csoportba tartozó rendelkezésre állási csoportok jelennek meg.  
    * Gépek más virtuális hálózatokkal nem lehet az azonos rendelkezésre állási csoport része.
    * Csak az azonos méretű virtuális gépeket egy rendelkezésre állási csoport részeként is szerepelhetnek.
5. Megtekintheti, és adja hozzá a célként megadott hálózat, alhálózat és az Azure virtuális Géphez rendelt IP-cím kapcsolatos információkat.
6. A **lemezek**, az operációs rendszer és az adatlemezek láthatja a replikálandó virtuális Gépen.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

- A cél IP-címe beállítható. Ne adjon meg egy címet, ha a átvevő gépen DHCP használja. Ha egy címet, amely nem használható feladatátadásra, a feladatátvétel nem működik. Ha a cím elérhető a feladatátvételi teszt hálózatában lévő, az azonos céloldali IP-cím használható a feladatátvételi teszthez.
- A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:
    - Ha a forrásoldali virtuális gépen a hálózati adapterek száma kisebb vagy egyenlő a célgép mérete engedélyezett adapterek számával, majd a célban adapterek azonos számú forrásaként.
    - Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot használja.
    Például ha a forrásgépen két hálózati adaptert, és a célgép mérete négy támogatja, a célgépen két adapter rendelkezik. Ha a forrásgépen két adapter működik, de a célgép mérete csak támogatja egy, a célgépen csak egy adapterrel rendelkezik.
    - Ha a virtuális gép több hálózati adapterrel rendelkezik, az összes az ugyanazon hálózathoz csatlakoznak. Ezenkívül az elsőt a listában látható lesz a *alapértelmezett* hálózati adapter az Azure-beli virtuális gépen.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft frissítési garanciával rendelkező ügyfelek használhatják a Azure Hybrid Benefit, menteni a licencelési költségeit az Azure-bA migrálta a Windows Server számítógépek esetében, vagy használhatja az Azure-vész-helyreállítási. Ha Ön jogosult a Azure Hybrid Benefit használatához, megadhatja, hogy a virtuális gépet ezzel az értékelemmel hozzárendelt egyike az Azure Site Recovery hoz létre, ha feladatátvitel történik. Ehhez tegye a következőket:
- Nyissa meg a replikált virtuális gép számítási és hálózati tulajdonságok szakaszában.
- Válaszolja meg a kérdést, amely rákérdez, hogy van-e egy Windows Server-licenc, amely lehetővé teszi, hogy az Azure Hybrid Benefit értékelemet.
- Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy a frissítési garancia, amelyek segítségével az Azure Hybrid Benefit alkalmazására a gépen a feladatátvételkor létrehozandó jogosult a Windows Server-licencem van.
- Mentse a replikált gép beállításait.

Tudjon meg többet [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Gyakori problémák

* Minden lemez kevesebb, mint 1 TB méretű lehet.
* Az operációsrendszer-lemez lehet egy egyszerű, és nem a dinamikus lemezeket.
* Generation 2/UEFI-kompatibilis virtuális gépek operációsrendszer-családnak Windows kell lennie, és a rendszerindító lemez 300 GB-nál kisebbnek kell lennie.

## <a name="next-steps"></a>További lépések

Miután védelmi befejeződik, és a gép elérte a védett állapotba, próbálkozzon egy [feladatátvételi](site-recovery-failover.md) ellenőrizze, hogy az alkalmazás betölt az Azure-ban, vagy nem.

Ha azt szeretné, hogy tiltsa le a védelmet, megtudhatja, hogyan [regisztráció és a védelem beállításainak tiszta](site-recovery-manage-registration-and-protection.md).
