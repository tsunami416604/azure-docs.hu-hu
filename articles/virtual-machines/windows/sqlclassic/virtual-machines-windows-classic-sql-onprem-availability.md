---
title: A helyszíni, mindig rendelkezésre állási csoportok kiterjesztése az Azure-ra | Microsoft dokumentumok
description: Ez az oktatóanyag a klasszikus központi telepítési modellel létrehozott erőforrásokat használja, és ismerteti, hogyan használhatja a Replika hozzáadása varázslót az SQL Server Management Studio (SSMS) rendszerben egy mindig rendelkezésre állási csoport replikájának hozzáadásához az Azure-ban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978049"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Helyi AlwaysOn rendelkezésre állási csoportok kiterjesztése az Azure-ra
Mindig a rendelkezésre állási csoportok magas rendelkezésre állású adatbáziscsoportok másodlagos replikák hozzáadásával. Ezek a replikák lehetővé teszik az adatbázisok fel- és feleslegének fel- és feleslegét. Emellett az olvasási munkaterhelések vagy a biztonsági mentési feladatok kiszervezésére is használhatók.

A helyszíni rendelkezésre állási csoportokat kiterjesztheti a Microsoft Azure-ra, ha egy vagy több Azure-beli virtuális gépet hoz létre az SQL Server kiszolgálóval, majd replikákként hozzáadja őket a helyszíni rendelkezésre állási csoportokhoz.

Ez az oktatóanyag feltételezi, hogy a következő:

* Aktív Azure-előfizetés. Akkor [iratkozzon fel egy ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A rendelkezésre állásban lévő meglévő csoport a helyszínen. A rendelkezésre állási csoportokról a [Mindig rendelkezésre állási csoportok című](https://msdn.microsoft.com/library/hh510230.aspx)témakörben talál további információt.
* A helyszíni hálózat és az Azure virtuális hálózat közötti kapcsolat. A virtuális hálózat létrehozásáról további információt a Helyek közötti [kapcsolat létrehozása az Azure Portalon (klasszikus) című](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)témakörben talál.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="add-azure-replica-wizard"></a>Azure Replica varázsló hozzáadása
Ez a szakasz bemutatja, hogyan **használhatja** az Azure Replica varázsló t a Mindig rendelkezésre állási csoport on-t tartalmazó megoldás Azure-replikákra való kiterjesztéséhez.

> [!IMPORTANT]
> Az **Azure Replica hozzáadása varázsló** csak a klasszikus üzembe helyezési modellel létrehozott virtuális gépeket támogatja. Az új virtuálisgép-telepítések az újabb Erőforrás-kezelő modellt kell használniuk. Ha virtuális gépeket használ az Erőforrás-kezelővel, akkor manuálisan hozzá kell adnia a másodlagos Azure-replika transact-SQL-parancsokkal (nem jelenik meg itt). Ez a varázsló nem fog működni az Erőforrás-kezelő forgatókönyvben.

1. Az SQL Server Management Studio alkalmazásból bontsa ki **a Mindig rendelkezésre állási rendelkezésre állási** > **csoportokon (A** > **rendelkezésre állási csoport neve) csomópontot.**
2. Kattintson a jobb gombbal **az Elérhetőségi replikák**elemre, majd kattintson **a Replika hozzáadása parancsra.**
3. Alapértelmezés szerint megjelenik a **Replika hozzáadása az elérhetőségi csoporthoz varázsló.** Kattintson a **Tovább** gombra.  Ha a varázsló korábbi indításakor a Lap alján a **Ne jelenjen meg újra** lehetőséget választotta, ez a képernyő nem jelenik meg.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Az összes meglévő másodlagos replikához csatlakoznia kell. Akkor kattintson a **Connect ...** mellett minden replika, vagy kattintson **az Összes csatlakoztatása ...** a képernyő alján. A hitelesítés után kattintson a **Tovább** gombra a következő képernyőre lépéshez.
5. A **Replikák megadása** lapon több lap jelenik meg a fent: **Replikák**, **Végpontok**, **Biztonsági másolat beállításai**és **Figyelő**. A **Replikák** lapon kattintson az **Azure Replica hozzáadása gombra...** az Azure Replica hozzáadása varázsló elindításához.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Ha már telepített egy meglévő Azure-tanúsítványt a helyi Windows-tanúsítványtárolóból, válasszon ki egy meglévőt. Válassza ki vagy adja meg az Azure-előfizetés azonosítóját, ha korábban már használt egyet. A Letöltés gombra kattintva letöltheti és telepítheti az Azure-kezelési tanúsítványt, és letöltheti az előfizetések listáját egy Azure-fiók használatával.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Az oldal minden mezőjét feltölti az okkal, amelyek az Azure virtuális gép (VM) létrehozásához fognak használni, amely a replikát fogja üzemeltetni.
   
   | Beállítás | Leírás |
   | --- | --- |
   | **Kép** |Az operációs rendszer és az SQL Server kívánt kombinációjának kiválasztása |
   | **Virtuális gép mérete** |Válassza ki az üzleti igényeinek leginkább megfelelő virtuális gép méretét |
   | **Virtuális gép neve** |Adja meg az új virtuális gép egyedi nevét. A névnek 3 és 15 karakter közötti karaktert kell tartalmaznia, csak betűket, számokat és kötőjeleket tartalmazhat, és betűvel kell kezdődnie, és betűvel vagy számmal kell végződnie. |
   | **Virtuális gép felhasználóneve** |Adja meg azt a felhasználónevet, amely a virtuális gép rendszergazdai fiókja lesz |
   | **Virtuális gép rendszergazdai jelszava** |Jelszó megadása az új fiókhoz |
   | **Jelszó megerősítése** |Az új fiók jelszavának megerősítése |
   | **Virtuális hálózat** |Adja meg az Azure virtuális hálózat, amely az új virtuális gép kell használni. A virtuális hálózatokról a Virtuális hálózat áttekintése című témakörben olvashat [bővebben.](../../../virtual-network/virtual-networks-overview.md) |
   | **Virtuális hálózat alhálózat** |Adja meg azt a virtuális hálózati alhálózatot, amelyet az új virtuális gépnek használnia kell |
   | **Tartomány** |A tartomány előre kitöltött értékének ellenőrzése |
   | **Tartomány felhasználóneve** |Adjon meg egy fiókot, amely a helyi fürtcsomópontok helyi rendszergazdák csoportjában található |
   | **Jelszó** |A tartomány felhasználónevének megadása |
8. Kattintson az **OK** gombra a telepítési beállítások érvényesítéséhez.
9. A következő jogi feltételek jelennek meg. Olvassa el és kattintson **az OK** gombra, ha elfogadja ezeket a feltételeket.
10. A **Replikák megadása** lap ismét megjelenik. Ellenőrizze az új Azure-kópia beállításait a **Replikák**, **végpontok**és **biztonsági mentési beállítások** lapon. Módosítsa a beállításokat, hogy megfeleljen az üzleti követelményeknek.  Az ezeken a lapokon található paraméterekről a [Replikák lapja megadása (Új rendelkezésre állási csoport varázsló/Replika hozzáadása varázsló) című témakörben](https://msdn.microsoft.com/library/hh213088.aspx)talál további információt. Vegye figyelembe, hogy a figyelők nem hozhatók létre a Figyelő lapon az Azure-replikákat tartalmazó rendelkezésre állási csoportok. Emellett ha a figyelő már létre a varázsló elindítása előtt, kap egy üzenetet, amely jelzi, hogy az Azure-ban nem támogatott. Megvizsgáljuk, hogyan hozhat létre figyelők a **rendelkezésre állási csoport figyelőjének létrehozása** szakaszban.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kattintson a **Tovább** gombra.
12. Jelölje ki a használni kívánt adatszinkronizálási módszert a Kezdeti adatszinkronizálás kiválasztása lapon, majd kattintson a **Tovább** **gombra.** A legtöbb esetben válassza a **Teljes adatszinkronizálás lehetőséget.** Az adatszinkronizálási módszerekről a [Kezdeti adatszinkronizálási lap kiválasztása (Mindig rendelkezésre álláscsoport varázslók) című](https://msdn.microsoft.com/library/hh231021.aspx)témakörben talál további információt.
13. Tekintse át az eredményeket az **Érvényesítés** lapon. Javítsa ki a fennálló problémákat, és szükség esetén futtassa újra az ellenőrzést. Kattintson a **Tovább** gombra.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Tekintse át az **Összegzés** lap beállításait, majd kattintson a **Befejezés gombra.**
15. A kiépítési folyamat megkezdődik. Ha a varázsló sikeresen befejeződött, kattintson a **Bezárás** gombra a varázslóból való kilépéshez.

> [!NOTE]
> Az Azure Replica hozzáadása varázsló naplófájlt hoz létre a Users\User Name\AppData\Local\SQL Server\AddReplicawizard mappában. Ez a naplófájl a sikertelen Azure replika-telepítések hibaelhárítására használható. Ha a varázsló nem hajt végre semmilyen műveletet, az összes korábbi művelet visszalesz állítva, beleértve a kiosztott virtuális gép törlését is.
> 
> 

## <a name="create-an-availability-group-listener"></a>Rendelkezésre állási csoportfigyelő létrehozása
A rendelkezésre állási csoport létrehozása után létre kell hoznia egy figyelőt az ügyfelek számára a replikákhoz való csatlakozáshoz. A figyelők közvetlen bejövő kapcsolatokat az elsődleges vagy egy csak olvasható másodlagos replika. A figyelőkről további információt az [ILB-figyelő konfigurálása az Azure-ban rendelkezésre állási csoportokhoz című témakörben talál.](../classic/ps-sql-int-listener.md)

## <a name="next-steps"></a>További lépések
Az **Azure Replica hozzáadása varázsló** használatával az Azure-ban mindig rendelkezésre állási csoport Azure-ra való kiterjesztéséhez is áthelyezhet néhány SQL Server-számítási feladatot teljesen az Azure-ba. Első lépések: [Sql Server virtuális gép kiépítése az Azure-ban](../sql/virtual-machines-windows-portal-sql-server-provision.md)című témakörben.

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témaköröket [az SQL Server azure-beli virtuális gépeken című témakörben talál.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

