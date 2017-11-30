---
title: "Azure virtuális gépek replikálása az Azure Site Recovery távoli másodlagos régióba |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan replikáljon Azure virtuális gépeken futó egy Azure-régió, egy másik régióban, az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Az Azure virtuális gépek replikálása Azure-régió, egy másik


A cikkből megtudhatja, hogyan replikáljon az Azure virtuális gépek (VM) több Azure-régió, egy másodlagos Azure régióra, az Azure Site Recovery szolgáltatással.

>[!NOTE]
>
> Az Azure Site Recovery szolgáltatással Virtuálisgép-replikációt jelenleg előzetes verzió.

## <a name="prerequisites"></a>Előfeltételek

* Recovery Services-tároló helyen kell rendelkeznie. Azt javasoljuk, hogy a cél régióban, amelybe replikálni a virtuális gépek hozzon létre a tárolót.
* Ha hálózati biztonsági csoportok (NSG) szabályok vagy egy tűzfal proxy kimenő internetkapcsolattal az Azure virtuális gépeken való hozzáférés vezérlése érdekében használ, győződjön meg arról, hogy engedélyezi-e a szükséges URL-címek vagy IP-címek. [További információk](./site-recovery-azure-to-azure-networking-guidance.md).
* Ha egy expressroute-on vagy VPN-kapcsolat a helyszíni és az Azure, a forráshely között [megtudhatja, hogyan állíthatja be azokat](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Az Azure felhasználói fiókot kell [konkrét engedélyeket](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)egy Azure virtuális gép replikációjának engedélyezéséhez.
Az Azure-előfizetéshez engedélyezni kell a célhelyen egy vész-helyreállítási régió használni kívánt virtuális gépek létrehozásához. A szükséges kvóta engedélyezéséhez forduljon a támogatási szolgálathoz.

## <a name="enable-replication"></a>A replikáció engedélyezése

Ezzel az eljárással Kelet-Ázsia célként használatos a forráshely és a Délkelet-Ázsia.

1. Kattintson a **+ replikálás** engedélyezni szeretné a virtuális gépek replikálását a tárolóban lévő állapottal.
2. Ellenőrizze, hogy **forrás:** értéke **Azure**.
3. Állítsa be **adatforrásról** való Kelet-Ázsia.
4. A **telepítési modell**, jelölje be **klasszikus** vagy **erőforrás-kezelő**.
5. A **erőforráscsoport**, válassza ki a csoport, amelyhez a forrás virtuális gépek tartozik. A kijelölt erőforráscsoportba tartozó virtuális gépek találhatók.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és jelöljön ki minden replikálni kívánt virtuális Gépet. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Végül kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. A **beállítások** > **célhelyet**, adja meg, hogy ha a forrás virtuális gép adatainak replikálja. A Site Recovery megfelelő célrégiók, attól függően, hogy a kijelölt virtuális gépek régiója listáját tartalmazza.
8. A Site Recovery alapértelmezett tárolóbeállítások állítja be. Ezek módosítható szükség szerint.

    - **Célként megadott erőforráscsoportja**. Alapértelmezés szerint a Site Recovery a cél régióban "automatikus" utótaggal rendelkező egy új erőforráscsoportot hoz létre. Ha a létrehozott erőforrás csoport már létezik, a rendszer újra.
    - **Cél virtuális hálózati**. Alapértelmezés szerint a Site Recovery cél régióban, az "automatikus" utótaggal rendelkező létrehoz egy új virtuális hálózati. Ezt a hálózatot a forrás hálózati van leképezve. [További](site-recovery-network-mapping-azure-to-azure.md) kapcsolatos hálózatra való leképezés.
    - **Storage-fiókok cél**. Alapértelmezés szerint a Site Recovery létrehoz egy új cél tárfiókot, amely megfelel a forrás virtuális gép tárolási konfigurációt. Ha a létrehozott fiók már létezik, a rendszer újra.
    - **Storage-fiókok gyorsítótár**. Az Azure Site Recovery létrehoz egy extra gyorsítótár storage-fiókot, a forrás-régió. Összes módosítást a forrás virtuális gépeken követni, és a gyorsítótár tárfiók előtt replikációs a célhelyre küldött.
    - **A rendelkezésre állási csoport**. Alapértelmezés szerint a Site Recovery létrehoz egy új rendelkezésre állási csoportban, a cél a régióban, az "automatikus" utótaggal rendelkező. Ha a létrehozott már létezik, a rendszer újra.
    - **Replikációs szabályzat**. Helyreállítás a helyreállítási pont megőrzési előzménygyűjteményhez és alkalmazáskonzisztens pillanatkép gyakorisága beállításokat határoz meg. Alapértelmezés szerint a Site Recovery helyreállítási pontok megőrzésének ideje 24 órát, és 60 perc alkalmazáskonzisztens pillanatkép gyakorisága az alapértelmezett beállításokkal létrehoz egy új replikációs házirendet.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Kattintson a **replikáció engedélyezése** virtuális gépek védelmének megkezdéséhez.

## <a name="customize-target-resources"></a>Tároló erőforrásait testreszabása

1. Módosítsa a cél alapértelmezett értéke:

    - **Célként megadott erőforráscsoportja**. Válassza ki bármelyik erőforrás csoportot a listából a célként megadott helyen, az előfizetésen belüli összes erőforráscsoport.
    - **Cél virtuális hálózati**. Válassza ki a listából, a virtuális hálózatok a célhelyen.
    - **A rendelkezésre állási csoport** csak adhat hozzá egy készlet a forrás régióban található virtuális gépek rendelkezésre állási készletek beállítások.
    - **Storage-fiókok cél**: hozzáadása olyan fiókot, amely érhető el.

        ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Kattintson a **tároló-erőforrás létrehozása** > **Replikálásengedélyezési**. Kezdeti replikálás során a virtuális gép állapotának eltarthat egy ideig frissítéséhez. Kattintson a **frissítése** a legfrissebb állapotának beolvasása.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Miután a virtuális gépek védelmének, virtuális gép állapotának ellenőrzése **replikált elemek**.



## <a name="next-steps"></a>Következő lépések
[Ismerje meg,](../azure-to-azure-tutorial-dr-drill.md) feladatátvételi teszt futtatása.

