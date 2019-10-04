---
title: Azure IaaS virtuális gépek áthelyezése másik Azure-régióba a Azure Site Recovery szolgáltatás használatával | Microsoft Docs
description: Az Azure IaaS virtuális gépek egyik Azure-régióból a másikba való áthelyezéséhez használja a Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4404f785116110d99dc242d2dae39c4a462f45e9
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376248"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Különböző helyzetekben érdemes áthelyezni a meglévő Azure IaaS virtuális gépeket (VM-EK) az egyik régióból a másikba. Tegyük fel például, hogy javítani szeretné a meglévő virtuális gépek megbízhatóságát és rendelkezésre állását, hogy javítsa a kezelhetőséget, vagy átlépjen az irányítási okokból. További információt az [Azure virtuális gépek áthelyezésének áttekintése](azure-to-azure-move-overview.md)című témakörben talál. 

A [Azure site Recovery](site-recovery-overview.md) szolgáltatással kezelheti és koordinálhatja a helyszíni gépek és az Azure-beli virtuális gépek vész-helyreállítását az üzletmenet folytonossága és a vész-helyreállítás (BCDR) érdekében. Site Recovery az Azure-beli virtuális gépek másodlagos régióba való áthelyezésének kezelésére is használható.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> 
> * Az áthelyezés előfeltételeinek ellenőrzése
> * A forrásként szolgáló virtuális gépek és a cél régió előkészítése
> * Az Adatmásolás és a replikáció engedélyezése
> * A konfiguráció tesztelése és az áthelyezés végrehajtása
> * A forrás régió erőforrásainak törlése
> 
> [!NOTE]
> Ebből az oktatóanyagból megtudhatja, hogyan helyezheti át az Azure-beli virtuális gépeket az egyik régióból a másikba. Ha javítani szeretné a rendelkezésre állást, ha a virtuális gépeket egy rendelkezésre állási csoportba helyezi egy másik régióban található rögzített virtuális gépekre, tekintse meg az Azure-beli [virtuális gépek áthelyezése Availability Zones oktatóanyagba](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli virtuális gépek abban az Azure-régióban találhatók, amelyről át kívánja helyezni.
- Győződjön meg arról, hogy a kiválasztott [forrásoldali régió-kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), és hogy tájékozott döntést hozott a régióról.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- A fiók engedélyeinek ellenőrzése. Ha létrehozta az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikálásának engedélyezéséhez és az Adatmásolás lényegében Azure Site Recovery használatával történő másolásához a következőket kell tennie:

    - Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A virtuálisgép-közreműködő beépített szerepköre rendelkezik ezekkel az engedélyekkel, amelyek a következőket tartalmazzák:
    - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
    - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
    - Engedély a kiválasztott tárfiókra történő íráshoz
    
    - Azure Site Recovery műveletek kezeléséhez szükséges engedélyek. A Site Recovery közreműködő szerepkör minden olyan engedéllyel rendelkezik, amely a Recovery Services-tárolóban lévő Site Recovery műveletek kezeléséhez szükséges.

- Győződjön meg arról, hogy az összes legújabb főtanúsítvány az áthelyezni kívánt Azure-beli virtuális gépeken található. Ha a legfelső szintű tanúsítványok nem a virtuális gépen vannak, akkor a biztonsági korlátozások megakadályozzák az Adatmásolást a célként megadott régióba.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
    
- Linux rendszerű virtuális gépek esetén kövesse a linuxos forgalmazója által biztosított útmutatást a legújabb megbízható főtanúsítványok és a tanúsítvány-visszavonási lista lekéréséhez a virtuális gépen.
- Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására.

- Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, vagy egy tűzfal proxyt használ a kimenő hozzáférés vezérléséhez, [ellenőrizze a követelményeket](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez magában foglalja a következőket: a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek nem korlátozódnak.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e a vész-helyreállítási célra használt cél régióban lévő virtuális gépek létrehozását. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a forrás virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha Site Recovery használja az Adatmásolás céljára, Site Recovery a célként megadott virtuális gép méretének vagy a legközelebbi lehetséges méretét is kiválasztja.

- Győződjön meg arról, hogy minden, a forrás hálózatkezelési elrendezésben azonosított összetevőhöz létrehoz egy célként megadott erőforrást. Ez a lépés fontos annak biztosítása érdekében, hogy a virtuális gépek rendelkezzenek a forrás régiójában lévő adott régióban lévő összes funkcióval és szolgáltatással.

     > [!NOTE] 
     > Azure Site Recovery automatikusan felfedi és létrehoz egy virtuális hálózatot, amikor engedélyezi a forrás virtuális gép replikálását. Emellett előre létrehozhat egy hálózatot, és hozzárendelheti azt a virtuális géphez a replikálás engedélyezéséhez a felhasználói folyamaton keresztül. Amint azt korábban említettük, manuálisan létre kell hoznia minden más erőforrást a céltartományban.

    A forrás virtuális gép konfigurációja alapján a leggyakrabban használt hálózati erőforrások létrehozásához tekintse meg a következő dokumentációt:
    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer)
    -  [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    - Bármely más hálózati összetevőnél tekintse meg a [hálózatkezelési dokumentációt](https://docs.microsoft.com/azure/#pivot=products&panel=network).



## <a name="prepare"></a>Előkészítés
A következő lépések bemutatják, hogyan készítheti elő a virtuális gépet a Azure Site Recovery megoldásként való áthelyezésre. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A tároló létrehozása bármely régióban, a forrásoldali régió kivételével

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
1. Válassza **az erőforrás** > létrehozása**felügyeleti eszközök** > **biztonsági mentése és site Recovery**lehetőséget.
1. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
1. Hozza létre az erőforráscsoport **ContosoRG**.
1. Válassza ki a kívánt Azure-régiót. A támogatott régiók kereséséhez tekintse meg a földrajzi elérhetőség [Azure site Recovery díjszabását](https://azure.microsoft.com/pricing/details/site-recovery/).
1. **Recovery Services**-tárolókban válassza az **Áttekintés** > **ContosoVMVault** >  **+ replikálás**lehetőséget.
1. A **Forrás** beállításnál válassza az **Azure** értéket.
1. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
1. Válassza ki a Resource Manager-alapú üzemi modellt. Ezután válassza ki a **forrás-előfizetést** és a **forrás erőforráscsoportot**.
1. A beállítások mentéséhez kattintson **az OK gombra** .

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Az Azure-beli virtuális gépek replikálásának engedélyezése és az adatok másolásának megkezdése

Site Recovery beolvassa az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.

1. A következő lépésben válassza ki az áthelyezni kívánt virtuális gépet, majd kattintson az **OK gombra**.
1. A **Beállítások**területen válassza a vész- **helyreállítás**lehetőséget.
1. A vész- **helyreállítási** > **célcsoport**konfigurálása területen válassza ki azt a régiót, amelyre a replikálást végzi.
1. A jelen oktatóanyag esetében fogadja el a többi alapértelmezett értéket.
1. Válassza a **Replikáció engedélyezése** elemet. Ezzel a lépéssel elindít egy feladatot a virtuális gép replikálásának engedélyezéséhez.

    ![Replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Áthelyezés

A következő lépések bemutatják, hogyan hajthatja végre az áthelyezést a célként megadott régióra.

1. Nyissa meg a tárolót. A **Beállítások** > **replikált elemek**területen válassza ki a virtuális gépet, majd válassza a **feladatátvétel**lehetőséget.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. A feladatok befejezése után győződjön meg arról, hogy a virtuális gép a várt módon jelenik meg a cél Azure-régióban.


## <a name="discard"></a>Elvetés 

Ha bejelölte az áthelyezett virtuális gépet, és módosítania kell a feladatátvételi pontot, vagy vissza szeretne térni egy korábbi pontra, a **replikált elemek**területen kattintson a jobb gombbal a virtuális gép > a **helyreállítási pont módosítása**lehetőségre. Ez a lépés lehetővé teszi egy másik helyreállítási pont megadását és feladatátvételét. 


## <a name="commit"></a>Véglegesítés 

Miután bejelölte az áthelyezett virtuális gépet, és készen áll a módosítás elvégzésére, a **replikált elemek**területen kattintson a jobb gombbal a virtuális gép > **véglegesítve**elemre. Ez a lépés befejezi az áthelyezési folyamatot a célként megadott régióba. Várjon, amíg a véglegesítési feladatok befejeződik.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A következő lépések végigvezetik a forrás-és az áthelyezéshez használt kapcsolódó erőforrások tisztításának lépésein.

Az áthelyezéshez használt összes erőforrás esetében:

- Nyissa meg a virtuális gépet. Válassza a **replikáció letiltása**lehetőséget. Ez a lépés leállítja a virtuális gép adatainak másolásának folyamatát.

   > [!IMPORTANT]
   > Ennek a lépésnek a végrehajtása azért fontos, hogy ne kelljen fizetnie Azure Site Recovery replikációért.

Ha nem tervezi a forrás-erőforrások újrafelhasználását, hajtsa végre a következő további lépéseket:

1. Törölje az összes olyan hálózati erőforrást a forrás régióban, amelyet az [Előfeltételek](#prerequisites)között azonosított.
1. Törölje a megfelelő Storage-fiókot a forrás régióban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gépet egy másik Azure-régióba helyezett át. Most már beállíthatja a vész-helyreállítást az áthelyezett virtuális géphez.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

