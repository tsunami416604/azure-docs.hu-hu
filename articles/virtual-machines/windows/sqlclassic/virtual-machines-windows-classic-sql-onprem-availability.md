---
title: Helyszíni always on rendelkezésre állási csoportok kiterjesztése az Azure-ra | Microsoft Docs
description: Ez az oktatóanyag a klasszikus üzemi modellel létrehozott erőforrásokat használja, és leírja, hogyan használható a replika hozzáadása varázsló SQL Server Management Studio (SSMS) szolgáltatásban az Always On rendelkezésre állási csoport replikájának hozzáadásához az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978049"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Helyszíni always on rendelkezésre állási csoportok kiterjesztése az Azure-ra
Az Always On rendelkezésre állási csoportok magas rendelkezésre állást biztosítanak az adatbázis-csoportok számára másodlagos replikák hozzáadásával. Ezek a replikák meghibásodás esetén lehetővé teszik az adatbázisok feladatátvételét. Emellett az olvasási munkaterhelések vagy a biztonsági mentési feladatok kiszervezésére is használhatók.

Kiterjesztheti a helyszíni rendelkezésre állási csoportokat úgy, hogy Microsoft Azure egy vagy több Azure-beli virtuális gép kiépítésével a SQL Server, majd replikáként hozzáadja őket a helyszíni rendelkezésre állási csoportokhoz.

Ez az oktatóanyag feltételezi, hogy rendelkezik a következőkkel:

* Aktív Azure-előfizetés. Regisztrálhat [az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy meglévő always on rendelkezésre állási csoport a helyszínen. További információ a rendelkezésre állási csoportokról: [Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx).
* Kapcsolat a helyszíni hálózat és az Azure-beli virtuális hálózat között. A virtuális hálózat létrehozásával kapcsolatos további információkért lásd: [helyek közötti kapcsolat létrehozása a Azure Portal használatával (klasszikus)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="add-azure-replica-wizard"></a>Azure-replika hozzáadása varázsló
Ebből a szakaszból megtudhatja, hogyan használhatja az **Azure replika hozzáadása varázslót** az Always On rendelkezésre állási csoport kibővítésére az Azure-replikák belefoglalásához.

> [!IMPORTANT]
> Az **Azure replika hozzáadása varázsló** csak a klasszikus üzembe helyezési modellel létrehozott virtuális gépeket támogatja. Az új virtuális gépek üzembe helyezéséhez az újabb Resource Manager-modellt kell használni. Ha a Resource Managerrel rendelkező virtuális gépeket használja, manuálisan kell hozzáadnia a másodlagos Azure-replikát a Transact-SQL-parancsokkal (itt nem látható). A varázsló nem fog működni a Resource Manager-forgatókönyvben.

1. SQL Server Management Studio belül bontsa ki az **Always on magas rendelkezésre állású** > **rendelkezésre állási csoportok** >  **[a rendelkezésre állási csoport neve]** elemet.
2. Kattintson a jobb gombbal a **rendelkezésre állási replikák**elemre, majd kattintson a **replika hozzáadása**parancsra.
3. Alapértelmezés szerint a **replika hozzáadása a rendelkezésre állási csoporthoz varázsló** jelenik meg. Kattintson a **Tovább** gombra.  Ha bejelölte a **ne jelenjen meg többé ez az oldal** lehetőség az oldal alján a varázsló előző elindítása során, akkor ez a képernyő nem jelenik meg.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Az összes meglévő másodlagos replikához csatlakoznia kell. Kattintson a **kapcsolat..** . lehetőségre. minden replika mellett kattintson **az összes csatolása** lehetőségre... a képernyő alján. A hitelesítés után kattintson a **tovább** gombra a következő képernyőre való továbblépés előtt.
5. A **replikák megadása** lapon több lap jelenik meg a felső részen: **replikák**, **végpontok**, **biztonsági mentési beállítások**és **figyelő**. A **replikák** lapon kattintson az **Azure-replika hozzáadása** lehetőségre... Az Azure-replika hozzáadása varázsló elindításához.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Válasszon ki egy meglévő Azure felügyeleti tanúsítványt a helyi Windows-tanúsítványtárolóból, ha korábban már telepített egyet. Válassza ki vagy adja meg egy Azure-előfizetés azonosítóját, ha korábban már használta. A letöltés lehetőségre kattintva letöltheti és telepítheti az Azure felügyeleti tanúsítványait, és letöltheti az előfizetések listáját egy Azure-fiók használatával.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Az oldalon lévő összes mezőt feltölti a replikát futtató Azure-beli virtuális gép (VM) létrehozásához használni kívánt értékekkel.
   
   | Beállítás | Leírás |
   | --- | --- |
   | **Rendszerkép** |Válassza ki az operációs rendszer és a SQL Server kívánt kombinációját |
   | **Virtuális gép mérete** |Az üzleti igényeknek leginkább megfelelő virtuális gép méretének kiválasztása |
   | **Virtuális gép neve** |Adja meg az új virtuális gép egyedi nevét. A névnek 3 és 15 karakter közöttinek kell lennie, és csak betűket, számokat és kötőjeleket tartalmazhat, és betűvel kell kezdődnie, és betűvel vagy számmal kell végződnie. |
   | **Virtuális gép felhasználóneve** |Adjon meg egy felhasználónevet, amely a virtuális gépen rendszergazdai fiók lesz. |
   | **VM-rendszergazda jelszava** |Új fiók jelszavának megadása |
   | **Jelszó megerősítése** |Az új fiók jelszavának megerősítése |
   | **Virtuális hálózat** |Itt adhatja meg azt az Azure-beli virtuális hálózatot, amelyet az új virtuális gépnek használnia kell. További információ a virtuális hálózatokról: [Virtual Network Overview (áttekintés](../../../virtual-network/virtual-networks-overview.md)). |
   | **Alhálózat Virtual Network** |Itt adhatja meg azt a virtuális hálózati alhálózatot, amelyet az új virtuális gépnek használnia kell |
   | **Tartomány** |Ellenőrizze, hogy helyes-e a tartomány előre megadott értéke. |
   | **Tartományi Felhasználónév** |Olyan fiókot válasszon, amely a helyi fürt csomópontjain a helyi Rendszergazdák csoportban található |
   | **Jelszó** |Adja meg a tartományi felhasználónévhez tartozó jelszót |
8. A központi telepítési beállítások érvényesítéséhez kattintson **az OK** gombra.
9. A jogi feltételek a következő címen jelennek meg. Olvassa el, majd kattintson **az OK gombra** , ha elfogadja a jelen feltételeket.
10. A **replikák meghatározása** lap ismét megjelenik. Ellenőrizze az új Azure-replika beállításait a **replikák**, a **végpontok**és a **biztonsági mentési beállítások** lapon. Módosítsa az üzleti igényeknek megfelelő beállításokat.  Az ezeken a lapokon található paraméterekkel kapcsolatos további információkért lásd: [replikák megadása lap (új rendelkezésre állási csoport varázsló/replika hozzáadása varázsló)](https://msdn.microsoft.com/library/hh213088.aspx). Vegye figyelembe, hogy a figyelők nem hozhatók létre az Azure-replikákat tartalmazó rendelkezésre állási csoportok figyelő lapján. Emellett, ha egy figyelő már létrejött a varázsló elindítása előtt, egy üzenet jelenik meg, amely jelzi, hogy az Azure-ban nem támogatott. Megvizsgáljuk, hogyan hozhat létre figyelőket a **rendelkezésre állási csoport figyelő létrehozása** szakaszban.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kattintson a **Tovább** gombra.
12. Válassza ki a **kezdeti adatszinkronizálás kiválasztása** oldalon használni kívánt adatszinkronizálási módszert, és kattintson a **tovább**gombra. A legtöbb esetben válassza a **teljes adatszinkronizálás**lehetőséget. Az adatszinkronizálási módszerekkel kapcsolatos további információkért lásd: [a kezdeti adatszinkronizálás kiválasztása lap (always on rendelkezésre állási csoport varázslók)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Tekintse át az eredményeket az **ellenőrzés** oldalon. Javítsa ki a függőben lévő problémákat, és szükség esetén futtassa újra az érvényesítést. Kattintson a **Tovább** gombra.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Tekintse át a beállításokat az **Összefoglalás** lapon, majd kattintson a **Befejezés**gombra.
15. A kiépítési folyamat megkezdődik. Ha a varázsló sikeresen befejeződik, kattintson a **Bezárás** gombra a varázslóból való kilépéshez.

> [!NOTE]
> Az Azure replika hozzáadása varázsló naplófájlt hoz létre a Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ez a naplófájl használható a sikertelen Azure replika-telepítések hibakereséséhez. Ha a varázsló nem hajt végre semmilyen műveletet, a rendszer visszaállítja az összes korábbi műveletet, beleértve a kiépített virtuális gép törlését is.
> 
> 

## <a name="create-an-availability-group-listener"></a>Rendelkezésre állási csoport figyelő létrehozása
A rendelkezésre állási csoport létrehozása után létre kell hoznia egy figyelőt, amellyel az ügyfelek csatlakoznak a replikához. A figyelők a bejövő kapcsolatokat az elsődleges vagy írásvédett másodlagos replikára irányítják. További információ a figyelőkről: [ILB-figyelő konfigurálása always on rendelkezésre állási csoportok számára az Azure-ban](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Következő lépések
Amellett, hogy az **Azure replika hozzáadása varázslót** használja az Always On rendelkezésre állási csoport kiterjesztésére az Azure-ra, az Azure-ba való teljes körű SQL Server munkaterhelések is áthelyezhetők. Első lépésként tekintse [meg SQL Server virtuális gép üzembe helyezése az Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)-ban című témakört.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

