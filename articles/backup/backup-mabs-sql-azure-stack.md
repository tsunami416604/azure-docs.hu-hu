---
title: SQL Server-számítási feladatok biztonsági és biztonsági másolatot kell fokozta az Azure Stacken
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Microsoft Azure Backup Server (MABS) az SQL Server-adatbázisok védelmére az Azure Stacken.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172313"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Az SQL Server biztonsági és biztonsági és biztonsági másolatot készítő biztonsági szolgáltatása az Azure Stackben

Ezzel a cikkből konfigurálhatja a Microsoft Azure Backup Server (MABS) az Azure Stack SQL Server-adatbázisok védelmére.

Az SQL Server adatbázis-biztonsági mentésének kezelése az Azure-ba és az Azure-ból való helyreállítás három lépésből áll:

1. Biztonsági mentési házirend létrehozása az SQL Server-adatbázisok védelmére
2. Igény szerinti biztonsági másolatkészítése
3. Az adatbázis helyreállítása a lemezekről és az Azure-ból

## <a name="before-you-start"></a>Előkészületek

[Az Azure Backup Server telepítése és előkészítése.](backup-mabs-install-azure-stack.md)

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Biztonsági mentési szabályzat létrehozása az SQL Server-adatbázisok Azure-ba való védelmére

1. Az Azure Backup Server felhasználói felületén kattintson a **védelem** munkaterületre.

2. Új védelmi csoport létrehozásához kattintson az Eszköz menüszalagján **az Új** gombra.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)

    Az Azure Backup Server elindítja a Védelmi csoport varázslót, amely egy **védelmi csoport**létrehozásához vezet. Kattintson a **Tovább** gombra.

3. A **Védelem csoporttípus kiválasztása** képernyőn válassza a **Kiszolgálók**lehetőséget.

    ![Védelmi csoport típusának kiválasztása - "Kiszolgálók"](./media/backup-azure-backup-sql/pg-servers.png)

4. A **Csoporttagok kiválasztása** képernyőn a Rendelkezésre álló tagok listája megjeleníti a különböző adatforrásokat. Ide **+** kattintva kibonthat egy mappát, és felfedheti az almappákat. Elem kijelöléséhez kattintson a jelölőnégyzetre.

    ![SQL DB kijelölése](./media/backup-azure-backup-sql/pg-databases.png)

    Az összes kijelölt elem megjelenik a Kijelölt tagok listában. Miután kiválasztotta a védeni kívánt kiszolgálókat vagy adatbázisokat, kattintson a **Tovább**gombra.

5. Az **Adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét, és jelölje be az **Online védelem** jelölőnégyzetet.

    ![Adatvédelmi módszer - rövid távú lemez & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. A **Rövid távú célok megadása** képernyőn adja meg a lemezre biztonsági mentési pontok létrehozásához szükséges bemeneteket, majd kattintson a **Tovább**gombra.

    A példában **megőrzési tartomány** **5 nap,** **szinkronizálási gyakorisága** **15 percenként**egyszer, amely a biztonsági mentés gyakorisága. **Az expressz teljes biztonsági mentés** **20:00-ra**van állítva.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A példában látható, a 20:00 minden nap egy biztonsági mentési pont jön létre a módosított adatok átvitelével az előző napi 8:00 PM biztonsági mentési pont. Ezt a folyamatot **expressz teljes biztonsági mentésnek nevezzük.** A tranzakciónaplók szinkronizálása 15 percenként történik. Ha 21:00-kor kell helyreállítania az adatbázist, a pont az utolsó expressz teljes biztonsági mentési pont naplóiból jön létre (ebben az esetben 20:00).
   >
   >

7. A **Lemezfoglalás áttekintése** képernyőn ellenőrizze a teljes rendelkezésre álló tárhelyet és a lehetséges lemezterületet. Kattintson a **Tovább** gombra.

8. A **Replikalétrehozási módszer kiválasztása csoportban**válassza ki, hogyan hozza létre az első helyreállítási pontot. A kezdeti biztonsági mentést manuálisan (hálózaton kívül) is átviheti a sávszélesség-torlódás okainak elkerülése érdekében vagy a hálózaton keresztül. Ha úgy dönt, hogy megvárja az első biztonsági mentés átvitelét, megadhatja a kezdeti átvitel időpontját. Kattintson a **Tovább** gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolat a teljes adatforrást (SQL Server adatbázis) az éles kiszolgálóról (SQL Server-gép) az Azure Backup Serverre kell átvinni. Ezek az adatok nagyok lehetnek, és az adatok hálózaton keresztültörténő átvitele meghaladhatja a sávszélességet. Ezért választhatja a kezdeti biztonsági mentés átvitelét: **Manuálisan** (cserélhető adathordozó használatával) a sávszélesség-torlódások elkerülése érdekében, vagy **automatikusan a hálózaton** keresztül (egy adott időpontban).

    Miután a kezdeti biztonsági mentés befejeződött, a többi biztonsági mentés növekményes biztonsági mentés a kezdeti biztonsági másolat. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.

9. Válassza ki, hogy mikor szeretné futtatni a konzisztencia-ellenőrzést, majd kattintson a **Tovább**gombra.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    Az Azure Backup Server konzisztencia-ellenőrzést végez a biztonsági mentési pont sértetlenségén. Az Azure Backup Server kiszámítja a biztonsági másolat fájl ellenőrző összegét az éles kiszolgálón (ebben a forgatókönyvben az SQL Server-gép) és a fájl biztonsági mentési adatait. Ütközés esetén a rendszer feltételezi, hogy az Azure Backup Server biztonsági másolata sérült. Az Azure Backup Server helyesbíti a biztonsági mentési adatokat az ellenőrzőösszeg eltérésének megfelelő blokkok elküldésével. Mivel a konzisztencia-ellenőrzések teljesítményigényesek, ütemezheti a konzisztencia-ellenőrzést, vagy automatikusan futtathatja azt.

10. Az adatforrások online védelmének megadásához jelölje ki az Azure-ban védeni kívánt adatbázisokat, és kattintson a **Tovább**gombra.

    ![Adatforrások kijelölése](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Válassza ki a szervezeti szabályzatoknak megfelelő biztonsági mentési ütemezéseket és adatmegőrzési házirendeket.

    ![Ütemezés és megőrzés](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer 12:00-kor és 20:00-kor készülnek (a képernyő alsó része)

    > [!NOTE]
    > Ez egy jó gyakorlat, hogy néhány rövid távú helyreállítási pontok a lemezen, a gyors helyreállítás. Ezek a helyreállítási pontok a működési helyreállításhoz használatosak. Az Azure jó külső helyként szolgál, magasabb SLOS-okkal és garantált rendelkezésre állással.
    >
    >

    **Ajánlott eljárás:** Ha a helyi lemez biztonsági mentései befejezése után az Azure-ba való biztonsági mentéseket ütemezi, a rendszer mindig átmásolja a legújabb lemezbiztonsági mentéseket az Azure-ba.

12. Válassza ki az adatmegőrzési házirend ütemezését. Az adatmegőrzési szabályzat működésének részleteit az [Azure Backup használata a szalagos infrastruktúra-cikk cseréjéhez](backup-azure-backup-cloud-as-tape.md)tartalmazza.

    ![Adatmegőrzési házirend](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentések naponta egyszer 12:00-kor és 20:00-kor (a képernyő alsó részén) készülnek, és 180 napig megőrződnek.
    * Az erősítés szombaton 12:00-kor. 104 hétig megmarad
    * A múlt szombaton 12:00-kor. 60 hónapig őrzik
    * A mentés március utolsó szombatján 12:00-kor. 10 évig őrzik
13. Kattintson a **Tovább** gombra, és válassza ki a megfelelő beállítást a kezdeti biztonsági másolat Azure-ba való átviteléhez. Választhat **automatikusan a hálózaton**

14. Miután áttekintette a szabályzat részleteit az **Összegzés** képernyőn, kattintson a **Csoport létrehozása** gombra a munkafolyamat befejezéséhez. A **Bezárás** gombra kattinthat, és figyelheti a feladat előrehaladását a munkaterület figyelése területen.

    ![Folyamatban lévő védelmi csoport létrehozása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server adatbázis igény szerinti biztonsági másolata

Míg az előző lépések biztonsági mentési házirendet hoztak létre, a "helyreállítási pont" csak az első biztonsági mentés kor jön létre. Ahelyett, hogy megvárnák, hogy az ütemező beinduljon, az alábbi lépések manuálisan aktiválják a helyreállítási pont létrehozását.

1. A helyreállítási pont létrehozása előtt várja meg, amíg a védelmi csoport állapota **rendben jelenik** meg az adatbázis számára.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázisra, és válassza **a Helyreállítási pont létrehozása parancsot.**

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Válassza az **Online védelem** lehetőséget a legördülő menüben, és kattintson az **OK** gombra egy helyreállítási pont létrehozásának megkezdéséhez az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Tekintse meg a feladat előrehaladását a **Figyelés** munkaterületen.

    ![Monitorkonzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból

A következő lépések szükségesek egy védett entitás (SQL Server-adatbázis) helyreállításához az Azure-ból.

1. Nyissa meg az Azure Backup Server Management Console-t. Nyissa meg a **Helyreállítási** munkaterületet, ahol a védett kiszolgálók láthatók. Keresse meg a szükséges adatbázist (ebben az esetben ReportServer$MSDPM2012). Válassza ki a helyreállítást az **online** pontként megadott **időponttól.**

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevére, és válassza a **Helyreállítás (Helyreállítás) parancsra.**

    ![Helyreállítás az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A MABS a helyreállítási pont részleteit mutatja. Kattintson a **Tovább** gombra. Az adatbázis felülírásához válassza a Helyreállítás az **SQL Server eredeti példányára**helyreállítási típust. Kattintson a **Tovább** gombra.

    ![Helyreállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a MABS helyreállítja az adatbázist egy másik SQL Server-példányba vagy egy önálló hálózati mappába.

4. A **Helyreállítási beállítások megadása** képernyőn kiválaszthatja a helyreállítási lehetőségeket, például a hálózati sávszélesség-használat szabályozását a helyreállítás által használt sávszélesség szabályozásához. Kattintson a **Tovább** gombra.

5. Az **Összefoglaló** képernyőn az eddig megadott összes helyreállítási konfiguráció látható. Kattintson **a Helyreállítás gombra.**

    A Helyreállítás állapot azt mutatja, hogy az adatbázis helyreállt. A **Bezárás** gombra kattintva bezárhatja a varázslót, és megtekintheti a **Figyelés** munkaterületen elért előrehaladást.

    ![Helyreállítási folyamat kezdeményezése](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezése után a visszaállított adatbázis konzisztens.

## <a name="next-steps"></a>További lépések

Tekintse meg a [Biztonsági másolat fájlok és alkalmazások](backup-mabs-files-applications-azure-stack.md) cikket.
Tekintse meg a [Biztonsági mentés SharePoint az Azure Stack](backup-mabs-sharepoint-azure-stack.md) cikket.
