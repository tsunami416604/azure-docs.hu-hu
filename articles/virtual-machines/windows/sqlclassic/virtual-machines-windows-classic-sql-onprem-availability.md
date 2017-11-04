---
title: "Always On rendelkezésre állási csoportok helyszíni kiterjesztése az Azure-bA |} Microsoft Docs"
description: "Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrást használ, és az SQL Server Management Studio (SSMS) a replika hozzáadása varázsló használata egy Always On rendelkezésre állási csoportnak replika hozzáadása az Azure-ban."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 50326a093adaf3558c56dfd0b38544f0e60be460
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Always On rendelkezésre állási csoportok helyszíni kiterjesztése az Azure-bA
Always On rendelkezésre állási csoportokat magas rendelkezésre állási adatbázis csoportjai számára másodlagos replikák hozzáadásával adja meg. Ezekre a replikákra engedélyezése feladatátvétele adatbázisok meghibásodása esetén. Ezenkívül ezek segítségével olvasási munkaterhelések vagy a biztonsági mentési feladatok kiszervezése.

Microsoft Azure kiterjesztheti egy vagy több Azure virtuális gépeken futó SQL Server-kiépítés és majd vegye fel őket replikáiként a helyi rendelkezésre állási csoport helyi rendelkezésre állási csoportok.

Ez az oktatóanyag feltételezi, hogy rendelkezik a következő:

* Aktív Azure-előfizetés. Is [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy meglévő Always On rendelkezésre állási csoportnak a helyi. További információ a rendelkezésre állási csoportok: [Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx).
* A helyszíni hálózat és az Azure virtuális hálózat közötti kapcsolat. Ez a virtuális hálózat létrehozásával kapcsolatos további információkért lásd: [(klasszikus) Azure portál használatával pont-pont kapcsolatot](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="add-azure-replica-wizard"></a>Adja hozzá a replika Azure varázsló
Ez a szakasz bemutatja, hogyan használható a **replika Azure hozzáadása varázsló** az Always On rendelkezésre állási csoportnak megoldástól tartalmazzák az Azure kiterjeszteni.

> [!IMPORTANT]
> A **replika Azure hozzáadása varázsló** csak támogatja a klasszikus telepítési modellel létrehozott virtuális gépeket. Új Virtuálisgép-telepítések az újabb Resource Manager modellt kell használja. Ha a virtuális gépek a Resource Manager használatával, majd manuálisan kell hozzáadni a másodlagos Azure replika Transact-SQL commmands (itt nem látható) használatával. Ez a varázsló az Erőforrás-kezelői forgatókönyvek nem fognak működni.

1. Az SQL Server Management Studio, bontsa ki a **magas rendelkezésre állású mindig a** > **rendelkezésre állási csoportok** > **[a következő rendelkezésre állási csoport neve]**.
2. Kattintson a jobb gombbal **rendelkezésre állási másodpéldányok**, majd kattintson a **adja hozzá a replika**.
3. Alapértelmezés szerint a **adja hozzá a replika rendelkezésre állási csoport létrehozása varázsló** jelenik meg. Kattintson a **Tovább** gombra.  Ha be van jelölve a **ne jelenjen meg többé ez a lap** lehetőséget a lap alján előző indítása során a varázsló, ez a képernyő nem jelenik meg.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Meg kell csatlakozni a meglévő összes másodlagos replikát. Rákattinthat a **Connect...** minden egyes replikának vagy mellett kattintson **összes csatlakozás...** a képernyő alján. A hitelesítés után kattintson **következő** ahhoz, hogy a következő képernyőre.
5. A a **meg replikák** lapon több lap tetején találhatók: **replikák**, **végpontok**, **biztonsági mentési beállítások**, és  **Figyelő**. Az a **replikák** lapra, majd **Azure replika hozzáadása...** az Azure-replika hozzáadása varázsló elindításához.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Válassza a helyi Windows-tárolójából egy meglévő Azure felügyeleti tanúsítványt, ha egy előtt telepítette. Válassza ki vagy adja meg Azure-előfizetés azonosítóját, ha egy előtt használt. Töltse le és telepítse az Azure felügyeleti tanúsítvánnyal, és töltse le az Azure-fiók használatával előfizetések listájából Letöltés gombra.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Az oldalon értékekkel, az Azure virtuális gép (VM), amely üzemelteti a replika létrehozásához használt minden mező fogja majd feltölteni.
   
   | Beállítás | Leírás |
   | --- | --- |
   | **Kép** |Válassza ki a kívánt operációs rendszer és az SQL Server kombinációját |
   | **Virtuálisgép-mérettel** |A virtuális Gépet, amely az üzleti igényeknek leginkább megfelelő méretének kiválasztása |
   | **Virtuális gép neve** |Adjon egyedi nevet az új virtuális gép számára. A név kell csak 3 és 15 karakter közötti lehet, is tartalmazhat, csak betűket, számokat és kötőjeleket tartalmazhat, és kell betűvel kezdődhet és betűvel vagy számmal végződhet. |
   | **Virtuális gép felhasználónév** |Adjon meg egy felhasználónevet, a rendszergazdai fiókhoz az a virtuális gép lesz |
   | **Virtuális gép rendszergazdai jelszó** |Adjon meg egy jelszót az új fiók |
   | **Jelszó megerősítése** |Erősítse meg a jelszót az új fiók |
   | **Virtuális hálózat** |Adja meg az Azure virtuális hálózat, amelyet az új virtuális Gépet kell használnia. További információ a virtuális hálózatok: [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtuális hálózati alhálózat** |Adja meg a virtuális hálózati alhálózat, amelyet az új virtuális Gépet kell használnia. |
   | **Tartomány** |Ellenőrizze a tartomány előre megadott érték helyes |
   | **Tartományi felhasználónév** |Adjon meg egy fiókot, amely a helyi Rendszergazdák csoport tagja a helyi fürt csomópontjain |
   | **Jelszó** |Adja meg a jelszavát a tartományi felhasználó nevét |
8. Kattintson a **OK** a központi telepítési beállítások érvényesítéséhez.
9. Jogi feltételek mellett jelenik meg. Olvassa el, és kattintson a **OK** Ha elfogadja az alábbi feltételeket.
10. A **meg replikák** oldal akkor jelenik meg újra. Ellenőrizze, hogy az új Azure replika beállításait a **replikák**, **végpontok**, és **biztonsági mentési beállítások** lapokon. Várja meg az üzleti követelményeknek.  A paraméterek ezeken a lapokon található további információ: [replikák lapon adja meg (új rendelkezésre állási csoport varázsló/Add replika varázsló)](https://msdn.microsoft.com/library/hh213088.aspx). Vegye figyelembe, hogy figyelői nem hozható létre, amelyek tartalmazzák az Azure-replikák rendelkezésre állási csoportok figyelő lapján. Emellett ha egy figyelő már létrejött a varázsló indítása előtt, kapni fog egy üzenet, amely azt jelzi, hogy nem támogatott az Azure-ban. A figyelők létrehozása követően áttekintjük a **hozzon létre egy rendelkezésre állási csoport figyelőjének** szakasz.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kattintson a **Tovább** gombra.
12. Válassza ki a használni kívánt adatok szinkronizálási módszert a **kezdeti adatszinkronizálás kiválasztása** lapot, és kattintson **következő**. A legtöbb esetben válassza **teljes adatszinkronizálás**. Adatok szinkronizálási módszerekkel kapcsolatos további információkért lásd: [válasszon kezdeti szinkronizálás lap (mindig a rendelkezésre állási csoport varázslók)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Tekintse át az eredményeket a a **érvényesítési** lap. Javítsa ki a fennálló problémákat, és újra futtassa az ellenőrzést, ha szükséges. Kattintson a **Tovább** gombra.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Tekintse át a beállításokat a a **összegzés** lapon, majd kattintson az **Befejezés**.
15. A kiépítési folyamat elindul. Ha a varázsló sikeresen befejeződött, kattintson **Bezárás** gombra ki a varázslóból való kilépéshez.

> [!NOTE]
> Az Azure-replika hozzáadása varázsló létrehoz egy naplófájlt az Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ez a naplófájl sikertelen Azure replika központi telepítésének hibaelhárítása használható. A varázsló nem sikerül, a művelet végrehajtása, ha az összes korábbi műveletek visszaállít a rendszer, beleértve a kiosztott virtuális gép törlése.
> 
> 

## <a name="create-an-availability-group-listener"></a>Hozzon létre egy rendelkezésre állási csoport figyelőjének
A rendelkezésre állási csoport létrehozása után készítsen egy figyelőt a következő ügyfelek csatlakoznak a replikákat. Figyelők irányítja a bejövő kapcsolatokat az elsődleges vagy egy írásvédett másodlagos replikára. A figyelők további információkért lásd: [egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Következő lépések
Használata mellett a **replika Azure hozzáadása varázsló** Azure kiterjeszteni a Always On rendelkezésre állási csoportnak, előfordulhat, hogy is helyezi át néhány SQL Server számítási feladatait teljes mértékben az Azure-bA. Első lépésként tekintse meg a [Azure SQL Server virtuális gépek kiépítése](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

