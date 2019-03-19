---
title: Azure IaaS virtuális gépek áthelyezése egy másik Azure-régióba az Azure Site Recovery szolgáltatással |} A Microsoft Docs
description: Az Azure Site Recovery használatával az Azure IaaS virtuális gépek áthelyezése egy Azure-régióból a másikba.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7619b8831d75ce639c6f6c773c7c7d491abc93e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122029"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Nincsenek különböző forgatókönyvekben, ahol szeretne áthelyezni a meglévő Azure IaaS virtuális gépek (VM) egyik régióból a másikba. Ha például szeretné fokozható a megbízhatóság és a meglévő virtuális gépek, kezelhetőség javítása, illetve irányítási okokból áthelyezése rendelkezésre állását. További információkért lásd: a [Azure virtuális gép áthelyezése – áttekintés](azure-to-azure-move-overview.md). 

Használhatja a [Azure Site Recovery](site-recovery-overview.md) vészhelyreállításának irányítása a helyszíni gépek és Azure virtuális gépek a helyreállításhoz üzleti folytonossági és vészhelyreállítási (BCDR) és kezelheti. A Site Recovery segítségével is kezelheti az áthelyezés Azure virtuális gépek egy másodlagos régióba.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> 
> * Az áthelyezés előfeltételeinek ellenőrzése
> * A forrásoldali virtuális gép és a célrégióban előkészítése
> * Másolja az adatokat, és a replikáció engedélyezése
> * Tesztelje a konfigurációt, és hajtsa végre az áttérés
> * A forrásrégióban található erőforrások törlése
> 
> [!NOTE]
> Ez az oktatóanyag bemutatja, Azure virtuális gépek áthelyezése egyik régióból egy másikba. Ha a rendelkezésre állás javításához a virtuális gépek áthelyezése a rendelkezésre állási zónához rögzített egy másik régióban lévő virtuális gépek, lásd: a [Azure virtuális gépek áthelyezése a rendelkezésre állási zónák oktatóanyag be](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure virtuális gépek Azure-régióban, amelyről el szeretné helyezni.
- Ellenőrizze, hogy a választott [forrásrégió - cél régió kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), és tájékozott döntést arról a célrégióban.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Fiókengedélyek ellenőrzése. Az ingyenes Azure-fiókot hozta létre, ha Ön az előfizetés rendszergazdája. Ha Ön nem az előfizetés-rendszergazda, a szükséges engedélyeket a rendszergazdával együttműködve. Engedélyezze egy virtuális gép replikációját, és lényegében az adatok másolása az Azure Site Recovery használatával kell rendelkeznie:

    * Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A virtuális gépek Közreműködője beépített szerepkör rendelkezik ezekkel az engedélyekkel, többek között:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    * Engedélyek kezelése az Azure Site Recovery-műveletek. A Site Recovery-közreműködő szerepkör rendelkezik, amely a Site Recovery-műveletek Recovery Services-tárolót a kezeléséhez szükséges összes engedélyt.

## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

1. Győződjön meg arról, hogy a legújabb főtanúsítványok áthelyezni kívánt Azure virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók a virtuális gépen, biztonsági okokból megakadályozza a az adatok másolását a célrégióban.

    - A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
    - Linux rendszerű virtuális gépekhez kövesse az útmutatást, a Linux terjesztőt, hogy a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekérése a virtuális gép által biztosított.
1. Győződjön meg arról, hogy nem használ hitelesítési proxyt hálózati kapcsolatok szabályozásához áthelyezni kívánt virtuális gépek esetében.
1. Ha az áthelyezni kívánt virtuális gép nem rendelkezik internetkapcsolattal, vagy használ tűzfalproxyt a kimenő hozzáférés vezérléséhez [a követelmények ellenőrzéséhez](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Azonosítsa a forrás hálózati elrendezés és az aktuálisan használt összes erőforrást. Ez magában foglalja, de nem korlátozódik, terheléselosztók, a hálózati biztonsági csoportok (NSG), és a nyilvános IP-címek.

## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Győződjön meg arról, hogy az Azure-előfizetés lehetővé teszi a virtuális gépek létrehozását a vészhelyreállításhoz használt célrégió. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

1. Győződjön meg arról, hogy előfizetése rendelkezik-e elegendő erőforrás a forrás virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. A Site Recovery az adatok másolásához a cél használata, a Site Recovery úgy dönt, az azonos méretű vagy a cél virtuális gép a legközelebbi lehetséges méretét.

1. Győződjön meg arról, hogy a forrás hálózati elrendezés minden összetevő, amely azonosítja a cél erőforrás létrehozása. Ez a lépés azért fontos, győződjön meg arról, hogy a virtuális gépek a funkciókat és szolgáltatásokat rendelkezik, amely a forrásrégióban kellett a célrégióban.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és létrehoz egy virtuális hálózatot, amikor engedélyezi a forrásoldali virtuális gép replikálását. Előre hozzon létre egy hálózatot is, és rendelje hozzá a felhasználói folyamatot a replikáció engedélyezése a virtuális gép. Később említett kell manuálisan hozzon létre erőforrásokat a célrégióban.

     A legtöbb létrehozása gyakran használt hálózati erőforrások, amelyek kapcsolódnak az Ön a forrás virtuális gép konfigurációja alapján, a következő dokumentációban tekintheti meg:

   - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Nyilvános IP-cím](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
     Bármely más hálózati összetevők, lásd: a [dokumentáció hálózatkezelés](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Manuálisan [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha azt szeretné, a konfiguráció teszteléséhez a célrégióban a végső lépés végrehajtása előtt. Ez a lépés azt javasoljuk, mert azt biztosítja, hogy az éles hálózati környezetben minimális megérintése.

## <a name="copy-data-to-the-target-region"></a>Másolja az adatokat a célrégió
A következő lépések bemutatják, hogyan másolhat adatokat a célrégióban Azure Site Recovery használatával.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Hozza létre a tárolót a forrásrégió kivételével bármelyik régióban

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
1. Válassza ki **erőforrás létrehozása** > **kezelőeszközök** > **Backup és Site Recovery**.
1. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
1. Az erőforráscsoport létrehozásához **ContosoRG**.
1. Válassza ki a kívánt Azure-régiót. Támogatott régiók megtekintéséhez a földrajzi elérhetőség [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/).
1. A **Recovery Services-tárolók**válassza **áttekintése** > **ContosoVMVault** > **+ replikálás**.
1. A **Forrás** beállításnál válassza az **Azure** értéket.
1. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
1. Válassza ki a Resource Manager-alapú üzemi modellt. Válassza ki a **forrás-előfizetés** és **forrásként szolgáló erőforráscsoportot**.
1. Válassza ki **OK** a beállítások mentéséhez.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure virtuális gépek replikálásának engedélyezése és az adatok másolásának indítása

A Site Recovery lekéri az előfizetésben és erőforráscsoportban társított virtuális gépek listáját.

1. A következő lépésben válassza ki a virtuális gép áthelyezése, majd válassza a kívánt **OK**.
1. A **beállítások**válassza **vész-helyreállítási**.
1. A **vészhelyreállítás konfigurálása** > **célrégió**, válassza ki a célrégióban, amelyhez a replikálást végezni kívánja.
1. A jelen oktatóanyag esetében fogadja el a többi alapértelmezett értéket.
1. Válassza ki **engedélyezze a replikációt**. Ebben a lépésben elindít egy feladatot a virtuális gép replikálásának engedélyezéséhez.

    ![A replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

1. Nyissa meg a tárolóban. A **beállítások** > **replikált elemek**, válassza ki a virtuális Gépet a célrégióban, és válassza ki a kívánt **+ feladatátvételi teszt**.
1. A **feladatátvételi teszt**, válasszon egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással nem tölt időt az az adatok feldolgozásával, így egy alacsony helyreállítási időre vonatkozó célkitűzés (RTO) nyújt.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás minden virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

1. Válassza ki a cél Azure-beli virtuális hálózatot, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése.
    > [!IMPORTANT]
    > Azt javasoljuk, hogy a teszt feladatátvételhez egy különálló Azure-beli Virtuálisgép-hálózatot használja. Ne használja az éles hálózati környezetben, amely be lett állítva, ha engedélyezte a replikációt és, hogy szeretné-e be a virtuális gépek áthelyezése idővel.
1. Az áthelyezés tesztelése indításához kattintson **OK**. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
1. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a rendszer méretezése és a megfelelő hálózathoz csatlakozik.
1. Ha törölni szeretné a virtuális gép tha tesztelés az áthelyezés során jött létre, kattintson a **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, és a teszt társított kapcsolatos megfigyelések mentéséhez.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hajtsa végre az áttérés a célrégióban, és győződjön meg róla

1. Nyissa meg a tárolóban. A **beállítások** > **replikált elemek**, a virtuális Gépet, majd válassza ki és **feladatátvételi**.
1. A **Feladatátvétel** területen válassza a **Legújabb** elemet.
1. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
1. A feladat befejezése után ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
1. A **replikált elemek**, jobb oldalon válassza ki a virtuális gép > **véglegesítése**. Ez a lépés befejezi az áttelepítési folyamat a célrégióban a. Várjon, amíg a véglegesítés feladat befejeződik.

## <a name="delete-the-resource-in-the-source-region"></a>Törölje az erőforrást a forrásrégióban

Nyissa meg a virtuális Gépet. Válassza ki **tiltsa le a replikációt**. Ebben a lépésben a virtuális gép számára az adatok másolásának a folyamat leáll.

> [!IMPORTANT]
> Fontos díját számítjuk fel az Azure Site Recovery replikációjára elkerülése érdekében ez a lépés végrehajtásához.

Ha nem tervezi, hogy újból felhasználhatja a forrás-erőforrásokat, kövesse az alábbi lépéseket:

1. Törli az összes releváns hálózati erőforrást, amely a 4. lépés részeként jelenik meg a forrásrégióban [a forrás virtuális gépek előkészítése](#prepare-the-source-vms).
1. Törölje a megfelelő tárfiók a forrásrégióban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure virtuális Gépen egy másik régióba való áthelyezése. Most konfigurálhatja a virtuális gép, amely az áthelyezés után vész-helyreállítási.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

