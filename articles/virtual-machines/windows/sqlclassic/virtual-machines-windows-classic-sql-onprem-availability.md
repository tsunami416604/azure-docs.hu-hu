---
title: A helyszíni Always On rendelkezésre állási csoportok kiterjesztése az Azure-bA |} A Microsoft Docs
description: Ebben az oktatóanyagban a klasszikus üzemi modellel létrehozott erőforrásokat használja, és ismerteti, hogyan lehet a replika hozzáadása varázsló segítségével az SQL Server Management Studio (SSMS) egy Always On rendelkezésre állási csoport-replika hozzáadása az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481582"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>A helyszíni Always On rendelkezésre állási csoportok kiterjesztése az Azure-bA
Always On rendelkezésre állási csoportokat magas rendelkezésre állás csoportok adatbázis másodlagos replikák hozzáadásával. Ezek a replikák engedélyezése feladatátvétele adatbázisok hiba esetén. Emellett ezek segítségével keresztmetszet vagy a biztonsági mentési feladatok kiszervezése.

A helyi rendelkezésre állási csoportok üzembe helyezésével egy vagy több Azure virtuális gépeken az SQL Server, és ezután hozzáadása azokat a helyi rendelkezésre állási csoportok replikáit kiterjesztheti a Microsoft Azure-bA.

Ez az oktatóanyag feltételezi, hogy rendelkezik az alábbiakkal:

* Aktív Azure-előfizetés. Is [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy meglévő Always On rendelkezésre állási csoport helyi. További információ a rendelkezésre állási csoportok: [Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx).
* A helyszíni hálózat és az Azure virtuális hálózatok közötti kapcsolat. Ez a virtuális hálózat létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy helyek közötti kapcsolat az Azure Portalon (klasszikus) használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="add-azure-replica-wizard"></a>Adja hozzá az Azure-replika varázsló
Ez a szakasz bemutatja, hogyan használható a **Azure replika hozzáadása varázsló** tartalmazzák az Azure az Always On rendelkezésre állási csoport-megoldás bővítése.

> [!IMPORTANT]
> A **Azure replika hozzáadása varázsló** csak a klasszikus üzemi modellel létrehozott virtuális gépeket is támogatja. Új virtuális gépek üzembe helyezése a újabb Resource Manager-modellt kell használnia. Ha a virtuális gépek és a Resource Manager használatával, majd manuálisan kell hozzáadni a Transact-SQL commmands (itt nem látható) használatával másodlagos Azure-replika. Ez a varázsló nem fog működni a Resource Manager-forgatókönyvében.

1. A belül az SQL Server Management Studióban bontsa ki **magas rendelkezésre állást mindig a** > **rendelkezésre állási csoportok** > **[a rendelkezésre állási csoport neve]**.
2. Kattintson a jobb gombbal **rendelkezésre állási másodpéldányok**, majd kattintson a **hozzáadása replika**.
3. Alapértelmezés szerint a **hozzáadása-replika rendelkezésre állási csoport varázsló** jelenik meg. Kattintson a **tovább**.  Ha be van jelölve a **többé ne jelenjen meg ez az oldal** lehetőséget az oldal alján előző indítás közben, a varázsló ezen a képernyőn nem jelenik meg.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Az összes meglévő másodlagos replikák csatlakozni kell adnia. Kattintson a **Connect...** mellett minden egyes replikának, vagy rákattinthat **összes csatlakoztatása...** a képernyő alján. A hitelesítés után kattintson a **tovább** , folytassa a következő képernyőn.
5. Az a **replikák megadása** lapon több lap tetején jelennek meg: **Replikák**, **végpontok**, **biztonsági mentési beállítások**, és **figyelő**. Az a **replikák** lapra, majd **Azure-replika hozzáadása...** az Azure-replika hozzáadása varázsló elindításához.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Ha telepítette, mielőtt, válassza ki egy meglévő Azure felügyeleti tanúsítványt a helyi Windows tanúsítványtárolóból. Válassza ki, vagy adja meg az Azure-előfizetés azonosítója, ha egy előtt használt. Kattinthat a letöltési tölthet le és telepítse az Azure felügyeleti tanúsítvánnyal, és töltse le az Azure-fiók használatával előfizetések listáját.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Minden mező az oldalon, az Azure virtuális gép (VM), amely üzemelteti a replika létrehozásához használt értékeket fogja majd feltölteni.
   
   | Beállítás | Leírás |
   | --- | --- |
   | **Rendszerkép** |Válassza ki a kívánt operációs rendszer és a SQL Server együttes használata |
   | **VM Size** |Válassza ki az üzleti igényeinek leginkább megfelelő virtuális gép méretét |
   | **Virtuális gép neve** |Adjon meg egy egyedi nevet az új virtuális gép számára. A név kell csak 3 és 15 karakter közötti lehet, is csak betűket, számokat és kötőjeleket tartalmazhat, és kell kezdődnie és betűvel vagy számmal végződhet. |
   | **Virtuális gép felhasználónevét** |Adjon meg egy felhasználónevet, amely a virtuális gépen a rendszergazda fiók lesz |
   | **Virtuális gép rendszergazdai jelszava** |Adjon meg egy jelszót az új fiók |
   | **Jelszó megerősítése** |Erősítse meg a jelszót az új fiók |
   | **Virtual Network** |Adja meg az Azure virtuális hálózat, amely az új virtuális Gépet kell használnia. A virtuális hálózatok további információkért lásd: [a Virtual Network áttekintése](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtuális hálózat alhálózatának** |Adja meg a virtuális hálózat alhálózatához, amelyet az új virtuális Gépet kell használnia. |
   | **Tartomány** |Erősítse meg a tartomány előre megadott értéke helyes-e |
   | **Tartomány felhasználónév** |Adjon meg egy fiókot, amely a helyi Rendszergazdák csoport tagja a helyi fürt csomópontjain |
   | **Jelszó** |Adja meg a jelszót a tartomány felhasználónév |
8. Kattintson a **OK** a központi telepítési beállítások ellenőrzéséhez.
9. Jogi feltételek mellett jelennek meg. Olvassa el, és kattintson a **OK** Ha elfogadja ezeket a feltételeket.
10. A **replikák megadása** lap jelenik meg újra. Ellenőrizze a beállításokat az új Azure-replika a **replikák**, **végpontok**, és **biztonsági mentési beállítások** lapokon. Módosítsa a beállításait, és az üzleti igényeknek.  A paraméterek ezeken a lapokon található további információért lásd: [replikák lapon adja meg (új rendelkezésre állási csoport varázsló/Add replika varázsló)](https://msdn.microsoft.com/library/hh213088.aspx). Vegye figyelembe, hogy figyelői nem hozható létre, amelyek tartalmazzák az Azure-replikák rendelkezésre állási csoportok számára a figyelőjének lap használatával. Emellett ha egy figyelő már létre van hozva a varázsló indítása előtt, kapni fog egy üzenet arról, hogy nem támogatott az Azure-ban. Áttekintjük, hogyan hozhat létre a figyelők az **hozzon létre egy rendelkezésre állási csoport figyelőjének** szakaszban.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kattintson a **tovább**.
12. Válassza ki a használni kívánt adatok szinkronizálási módszert a **kezdeti adatszinkronizálás kiválasztása** lapot, és kattintson **tovább**. Válassza ki a legtöbb esetben **teljes adatszinkronizálás**. Adatok szinkronizálási módszerekkel kapcsolatos további információkért lásd: [válassza ki kezdeti szinkronizálás lap (mindig a rendelkezésre állási csoport varázslók)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Tekintse át az eredményeket a a **érvényesítési** lapot. Javítsa ki a szálankénti függőben lévő problémák, és ismét futtassa az ellenőrzést, ha szükséges. Kattintson a **tovább**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Tekintse át a beállításokat a **összefoglalás** lapon, majd kattintson a **Befejezés**.
15. A kiépítési folyamat elindul. Ha a varázsló sikeresen befejeződött, kattintson **Bezárás** kattintva lépjen ki a varázsló.

> [!NOTE]
> Az Azure-replika hozzáadása varázsló létrehoz egy naplófájlt az Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ez a naplófájl használható az Azure-replika sikertelen üzembe helyezés hibaelhárítása. A varázsló a következő bármilyen művelet végrehajtása nem sikerül, ha minden előző művelet visszavonásra, beleértve az üzembe helyezett virtuális gép törlése.
> 
> 

## <a name="create-an-availability-group-listener"></a>Hozzon létre egy rendelkezésre állási csoport figyelője
A rendelkezésre állási csoport létrehozása után létre kell hoznia egy figyelőt a következő csatlakozhatnak a replikákon. Figyelők irányítja a bejövő kapcsolatokat az elsődleges és a egy írásvédett másodlagos replikára. A figyelők további információkért lásd: [Always On rendelkezésre állási csoportok ILB figyelő konfigurálása az Azure-ban](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>További lépések
Használata mellett a **Azure replika hozzáadása varázsló** az Always On rendelkezésre állási csoport kiterjesztése az Azure-ba, is áthelyezheti néhány SQL Server számítási feladatainak teljes egészében az Azure-bA. Első lépésként lásd [kiépítése egy SQL Server virtuális gépet az Azure-ban](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

