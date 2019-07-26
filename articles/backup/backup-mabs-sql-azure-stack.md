---
title: SQL Server munkaterhelések biztonsági mentése Azure Stack
description: A Azure Backup Server használatával biztosíthatja SQL Server számítási feladatait Azure Stack.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: 11d03a9c5cc81b915f48bc66f5a0e5ab034662ed
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465162"
---
# <a name="back-up-sql-server-on-stack"></a>SQL Server biztonsági mentése a veremben
Ebből a cikkből megtudhatja, Microsoft Azure Backup-kiszolgáló (MABS) SQL Server-adatbázisok védelméhez Azure Stack.

Az Azure-ba való SQL Server adatbázis biztonsági mentésének és az Azure-ba történő helyreállításnak a kezelése három lépést tesz szükségessé:

1. Biztonsági mentési szabályzat létrehozása SQL Server adatbázisok védelméhez
2. Igény szerinti biztonsági másolatok létrehozása
3. Az adatbázis helyreállítása lemezekről és az Azure-ból

## <a name="before-you-start"></a>Előkészületek

[Azure Backup Server telepítése és előkészítése](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Biztonsági mentési szabályzat létrehozása SQL Server adatbázisok védelméhez az Azure-ban
1. A Azure Backup Server felhasználói felületen kattintson a **védelem** munkaterületre.

2. Az eszközsávon kattintson az **új** elemre egy új védelmi csoport létrehozásához.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server elindítja a védelmi csoport varázslót, amely végigvezeti a **védelmi csoport**létrehozásának lépésein. Kattintson a **Tovább** gombra.

3. A **védelmi csoport típusának kiválasztása** képernyőn válassza a **kiszolgálók**elemet.

    ![Védelmi csoport típusának kiválasztása – "kiszolgálók"](./media/backup-azure-backup-sql/pg-servers.png)

4. A csoporttagok **kiválasztása** képernyőn az elérhető tagok lista a különböző adatforrásokat jeleníti meg. Kattintson **+** ide a mappa kibontásához és az almappák megjelenítéséhez. Kattintson a jelölőnégyzetre egy elem kiválasztásához.

    ![SQL-adatbázis kiválasztása](./media/backup-azure-backup-sql/pg-databases.png)

    Az összes kijelölt elem megjelenik a kijelölt tagok listájában. A védelemmel ellátni kívánt kiszolgálók vagy adatbázisok kiválasztása után kattintson a **tovább**gombra.

5. Az **adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét, és jelölje be a szeretnék **online védelem** jelölőnégyzetet.

    ![Adatvédelmi módszer – rövid távú lemez & online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. A **rövid távú célok megadása képernyőn adja meg** a szükséges bemeneteket a lemezre történő biztonsági mentési pontok létrehozásához, majd kattintson a **tovább**gombra.

    A példában a **megőrzési** időtartam **5 nap**, a **szinkronizálás gyakorisága** **15 percenként**történik, ami a biztonsági mentés gyakorisága. Az **expressz teljes biztonsági mentés** **8:00 P. M**értékre van állítva.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A következő példában a rendszer minden nap 8:00 ÓRAKOR létrehoz egy biztonsági mentési pontot a módosított adatoknak az előző nap 8:00 PM biztonsági mentési pontjából való átadásával. Ezt a folyamatot **expressz teljes biztonsági mentésnek**nevezzük. A tranzakciós naplók 15 percenként szinkronizálhatók. Ha az adatbázist 9:00 ÓRAKOR kell helyreállítani, a pont az utolsó expressz teljes biztonsági mentési pont naplóiból jön létre (ebben az esetben 08:00).
   >
   >

7. A **lemez kiosztásának áttekintése** képernyőn ellenőrizze a rendelkezésre álló teljes tárolóhelyet, valamint a lehetséges lemezterületet. Kattintson a **Tovább** gombra.

8. A **replika-létrehozási módszer választása**területen válassza ki, hogyan hozza létre az első helyreállítási pontot. A kezdeti biztonsági mentést manuálisan (hálózaton kívül) is átviheti a sávszélesség-torlódás vagy a hálózat felett. Ha úgy dönt, hogy megvárja az első biztonsági mentést, megadhatja a kezdeti átvitel időpontját. Kattintson a **Tovább** gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolathoz a teljes adatforrást (SQL Server adatbázist) át kell vinni az üzemi kiszolgálóról (SQL Server gépről) a Azure Backup Serverre. Ezek az adatmennyiségek nagy méretűek lehetnek, és a hálózaton keresztüli adatátvitel meghaladhatja a sávszélességet. Ebből kifolyólag dönthet úgy, hogy áthelyezi a kezdeti biztonsági másolatot: **Manuálisan** (cserélhető adathordozó használatával) a sávszélesség-torlódás elkerüléséhez, vagy **automatikusan a hálózaton keresztül** (adott időpontban).

    A kezdeti biztonsági mentés befejezését követően a biztonsági mentések a kezdeti biztonsági másolaton lévő növekményes biztonsági másolatok. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.

9. Válassza ki, hogy mikor szeretné futtatni a konzisztencia-ellenőrzést, és kattintson a **tovább**gombra.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    A Azure Backup Server konzisztencia-ellenőrzést végez a biztonsági mentési pont integritásán. Azure Backup Server kiszámítja a biztonságimásolat-fájl ellenőrzőösszegét az üzemi kiszolgálón (ebben a forgatókönyvben SQL Server gépen), valamint az adott fájl biztonsági másolatait. Ütközés esetén feltételezi, hogy a biztonsági másolatban szereplő fájl sérült Azure Backup Server. Azure Backup Server helyesbíti a biztonsági másolatban szereplő adatokat úgy, hogy elküldi az ellenőrzőösszeg-eltérésnek megfelelő blokkokat. Mivel a konzisztencia-ellenőrzések teljesítmény-igényesek, a konzisztencia-ellenőrzést ütemezhet vagy automatikusan futtathatja.

10. Az adatforrások online védelmének megadásához válassza ki az Azure-ba védeni kívánt adatbázisokat, és kattintson a **tovább**gombra.

    ![Adatforrások kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Válassza ki a szervezeti szabályzatoknak megfelelő biztonsági mentési ütemterveket és adatmegőrzési házirendeket.

    ![Ütemterv és megőrzés](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer, 12:00 ÓRAKOR és 20:00 órakor (a képernyő alsó részén) jelennek meg.

    > [!NOTE]
    > Jó megoldás, ha a gyors helyreállítás érdekében néhány rövid távú helyreállítási pontot kell használnia a lemezen. Ezek a helyreállítási pontok az operatív helyreállításhoz használatosak. Az Azure jó helyen található, magasabb SLA-kat és garantált rendelkezésre állást kínál.
    >
    >

    **Ajánlott eljárás**: Ha az Azure-ba történő biztonsági mentéseket ütemezi a helyi lemez biztonsági mentése után, a rendszer mindig az Azure-ba másolja a legújabb lemezes biztonsági másolatokat.

12. Válassza ki az adatmegőrzési szabály ütemtervét. Az adatmegőrzési szabályzat működésének részletes leírását a [használati Azure Backup a szalagos infrastruktúra lecserélése című cikkben](backup-azure-backup-cloud-as-tape.md)találja.

    ![Adatmegőrzési szabályzat](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentések naponta egyszer, 12:00 és 8 ÓRAKOR (a képernyő alsó részén) jelennek meg, és 180 napig őrződnek meg.
    * A biztonsági mentés szombaton, 12:00 órakor 104 hétig megőrzött
    * A biztonsági mentés az elmúlt szombaton 12:00 órakor 60 hónapig megőrzött
    * A biztonsági mentés március 12:00 órakor, az utolsó szombaton 10 évig őrzi meg
13. Kattintson a **tovább** gombra, és válassza ki a megfelelő lehetőséget a kezdeti biztonsági másolat Azure-ba történő átviteléhez. Automatikusan kiválaszthatja **a hálózaton keresztül**

14. Ha áttekinti a szabályzat részleteit az **Összefoglalás** képernyőn, kattintson a **csoport létrehozása** elemre a munkafolyamat befejezéséhez. Kattintson a **Bezárás** gombra, és figyelje a feladatok előrehaladását a figyelés munkaterületen.

    ![A védelmi csoport létrehozása folyamatban van](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server-adatbázis igény szerinti biztonsági mentése
Míg az előző lépések létrehozott egy biztonsági mentési szabályzatot, a rendszer csak az első biztonsági mentés alkalmával hozza létre a helyreállítási pontot. Ahelyett, hogy az ütemező beindítására vár, az alábbi lépések a helyreállítási pontok manuális létrehozását indítja el.

1. Várjon, amíg a védelmi csoport állapota a helyreállítási pont létrehozása előtt a-adatbázishoz tartozó **OK** értékre mutat.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázisra, és válassza a **helyreállítási pont létrehozása**lehetőséget.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. A legördülő menüben válassza az **online védelem** lehetőséget, majd kattintson **az OK** gombra egy helyreállítási pont Azure-beli létrehozásának megkezdéséhez.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Tekintse meg a feladatok előrehaladását a **figyelés** munkaterületen.

    ![Figyelési konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból
A következő lépések szükségesek egy védett entitás (SQL Server adatbázis) Azure-ból történő helyreállításához.

1. Nyissa meg a Azure Backup Server felügyeleti konzolt. Navigáljon a **helyreállítási** munkaterületre, ahol megtekintheti a védett kiszolgálókat. Tallózással keresse meg a szükséges adatbázist (ebben az esetben a reportserver $ MSDPM2012). Válasszon ki egy **online** pontként megadott **helyreállítási** időpontot.

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevére, és kattintson a **helyreállítás**elemre.

    ![Helyreállítás az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A MABS a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább** gombra. Az adatbázis felülírásához válassza a helyreállítás típust a **SQL Server eredeti példányára**. Kattintson a **Tovább** gombra.

    ![Visszaállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a MABS egy másik SQL Server példányra vagy egy különálló hálózati mappába helyreállítja az adatbázist.

4. A **helyreállítási beállítások megadása** képernyőn kiválaszthatja a helyreállítási beállításokat, például a hálózati sávszélesség használatának szabályozását a helyreállítás által használt sávszélesség szabályozásához. Kattintson a **Tovább** gombra.

5. Az **Összefoglalás** képernyőn az eddig megadott összes helyreállítási konfiguráció látható. Kattintson a **helyreállítás**gombra.

    A helyreállítás állapota a helyreállított adatbázist mutatja. A **Bezárás** gombra kattintva zárhatja be a varázslót, és megtekintheti a folyamatot a **figyelés** munkaterületen.

    ![Helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejeződése után a visszaállított adatbázis az alkalmazás konzisztens.

## <a name="next-steps"></a>További lépések

Tekintse meg a [biztonsági másolat fájljait és](backup-mabs-files-applications-azure-stack.md) az alkalmazásról szóló cikket.
Tekintse [meg a biztonsági mentés SharePoint Azure stack](backup-mabs-sharepoint-azure-stack.md) cikkben.
