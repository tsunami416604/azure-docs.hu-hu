---
title: Az Azure Backup az SQL Server számítási feladatok Azure Backup Server használatával
description: Bevezetés az Azure Backup Server használatával az SQL Server-adatbázisok biztonsági mentése
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 72b2368979f0c9e546e1c7ef7fc462bf1d64c947
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490465"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>SQL Server biztonsági mentése az Azure az Azure Backup Serverhez
Ez a cikk végigvezeti a konfigurációs lépések, Microsoft Azure Backup Server (MABS) használatával az SQL Server-adatbázisok biztonsági mentéséhez.

Az SQL Server adatbázis biztonsági másolatának az Azure és a helyreállítás az Azure felügyeleti három lépésből áll:

1. Hozzon létre egy biztonsági mentési szabályzat az Azure SQL Server-adatbázisok védelmét.
2. Hozzon létre igény szerinti biztonsági másolatokat az Azure-bA.
3. Adatbázis helyreállítása az Azure-ból.

## <a name="before-you-start"></a>Előkészületek
Mielőtt elkezdené, győződjön meg arról, hogy [telepítve van, és az Azure Backup Server készített](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Az Azure SQL Server-adatbázisok védelmét egy biztonsági mentési szabályzat létrehozása
1. Az Azure Backup Server felhasználói felületén kattintson a **védelmi** munkaterületen.
2. Az eszközök menüszalagján kattintson **új** hozhat létre egy új védelmi csoportot.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)
3. MABS jeleníti meg a kezdőképernyőn útmutatással létrehozásával egy **védelmi csoport**. Kattintson a **tovább**.
4. Válassza ki **kiszolgálók**.

    ![Válassza ki a védelmi csoport típusának – "Kiszolgáló"](./media/backup-azure-backup-sql/pg-servers.png)
5. Bontsa ki az SQL Server-gép, ahol jelen-e az adatbázisok biztonsági mentése. MABS biztonsági mentésre alkalmas, a kiszolgáló különböző adatforrásokat jeleníti meg. Bontsa ki a **minden SQL-megosztás** , és válassza ki a (ebben az esetben kiválasztott ReportServer$ MSDPM2012 és ReportServer$ MSDPM2012TempDB) adatbázisok biztonsági mentése. Kattintson a **tovább**.

    ![Válassza ki az SQL DB](./media/backup-azure-backup-sql/pg-databases.png)
6. Adja meg a védelmi csoport nevét, és válassza ki a **online védelmet szeretnék** jelölőnégyzetet.

    ![Adatvédelmi módszer - rövid távú lemez & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Az a **rövid távú célok megadása** képernyőn, tartalmazza a szükséges bemeneti adatokat lemezes biztonsági mentési pontok létrehozásához.

    Itt láthatjuk, hogy **megőrzési** értékre van állítva *5 nap*, **szinkronizálási gyakoriság** egyszer értékre van állítva minden *15 perc* ami a a gyakoriság, amellyel biztonsági mentés készül. **Expressz teljes biztonsági mentés** értékre van állítva *8:00 óráig*.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 8:00 Órakor (megfelelően a képernyő bemeneti), egy biztonsági mentési pontok módosított adatokat visz át az előző nap 8:00 Órakor biztonsági mentési pontok úgy jön létre naponta. Ez a folyamat **expressz teljes biztonsági mentés**. Miközben a tranzakciós naplók szinkronizálódnak 15 percenként Ha szükség van az adatbázis helyreállításához bővít 9:00 Órakor – a pont a naplók az elmúlt visszajátszása alapján jön létre, majd expressz teljes biztonsági mentési pont (ebben az esetben 8 óra).
   >
   >

8. Kattintson a **Tovább** gombra

    MABS mutatja a teljes rendelkezésre álló tárhely és a lehetséges lemezterület-kihasználás.

    ![Lefoglalt lemezterület](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint a MABS létrehoz egy kötetet a kezdeti biztonsági másolatot használt adatforrásonként (SQL Server-adatbázis). Ezzel a megközelítéssel a logikai lemezkezelő (LDM) korlátozza a MABS-védelem 300 adatforrások (SQL Server-adatbázisok). A probléma megoldásához, válassza ki a **adatok közös elhelyezése a DPM-Tárolókészletben**, lehetőséget. Ha ezt a beállítást használja, MABS használ egy kötetet több adatforráson, amely lehetővé teszi a MABS legfeljebb 2000 SQL-adatbázisok védelmét.

    Ha **a kötetek méretének automatikus növelése** beállítás meg van adva, az éles adatmennyiség növekedésével fiókot a fokozott biztonsági mentési mennyiségi MABS is. Ha **a kötetek méretének automatikus növelése** nincs bejelölve, a MABS korlátozza a biztonsági mentési tár segítségével a védelmi csoportban lévő adatforrásokat.
9. A rendszergazdák a kiválasztott történő manuális (hálózat) ki a kezdeti biztonsági mentés elkerülése érdekében a sávszélesség torlódás vagy a hálózaton keresztül vannak megadva. Ezenkívül konfigurálhatják az idő, amellyel a kezdeti átvitel akkor fordulhat elő. Kattintson a **tovább**.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolatot a teljes adatforrásra (SQL Server-adatbázis) való átvitele az üzemi kiszolgáló (az SQL Server-gép), a MABS van szükség. Lehet, hogy ezeket az adatokat nagy, és az adatok hálózati átvitele közben túllépheti a sávszélességet. Ebből kifolyólag a rendszergazdák kiválaszthatják a átvitele a kezdeti biztonsági mentés: **Manuálisan** (a cserélhető adathordozó használatával) elkerülése érdekében a sávszélesség szűk keresztmetszet, vagy **automatikusan a hálózaton keresztül** (megadott időben).

    A kezdeti biztonsági mentés befejezése után a biztonsági mentések a többi olyan növekményes biztonsági mentések a kezdeti biztonsági másolatot. A növekményes biztonsági mentések általában kicsi, és egyszerűen továbbítja a hálózaton.
10. Válassza ki, ha azt szeretné, hogy a konzisztencia-ellenőrzés futtatása, és kattintson a **tovább**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS hajthat végre egy konzisztencia így ellenőrzi a biztonsági mentési pontok integritását. A biztonságimásolat-fájlt az üzemi kiszolgáló (SQL Server-gép ebben a forgatókönyvben) és a biztonsági másolat az adatokat az adott fájlban a következő MABS ellenőrzőösszege számítja ki. Ütközés esetén azt feltételezzük, hogy a biztonsági másolat fájlban a következő MABS sérült. MABS rectifies a mentett adatok az ellenőrzőösszegek megfelelő blokkok küldésével. A konzisztencia-ellenőrzést egy olyan teljesítmény-igényes művelet, mert a rendszergazdák a konzisztencia-ellenőrzés ütemezése, vagy automatikusan futó, lehetősége van.
11. Az adatforrás online védelmét, jelölje ki az adatbázisok, Azure, majd kattintson a védendő **tovább**.

    ![Adatforrás kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Rendszergazdák kiválaszthatják, ütemtervekkel és adatmegőrzési szabályzatok, amelyek megfelelnek a szervezet szabályzatai.

    ![Ütemezése és megőrzése](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer készít 12:00 Órakor, és 8-kor (a képernyő alsó részén)

    > [!NOTE]
    > Tanácsos rendelkezik néhány rövid távú helyreállítási pontok a lemezen, a gyors helyreállítás. Ezen helyreállítási pontok szolgálnak az "operatív helyreállítás". Az Azure egy jó telephelyen kívüli helyre a magasabb SLA-k funkcionál, és garantált rendelkezésre állás.
    >
    >

    **Ajánlott eljárás**: Győződjön meg arról, hogy az Azure biztonsági mentések ütemezve a helyi lemezes biztonsági mentések a DPM használatának befejezése után. Ez lehetővé teszi a legújabb lemezes biztonsági mentés az Azure-bA kell másolni.

13. Válassza ki a megőrzési házirend-ütemezést. A részletek a megtartási házirend működéséről biztosított [használata az Azure Backup cserélje le a szalagos infrastruktúra cikk](backup-azure-backup-cloud-as-tape.md).

    ![Adatmegőrzési szabályzat](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * Biztonsági mentések naponta egyszer készít 12:00 Órakor, és 8-kor (a képernyő alsó részén), és 180 napig megőrződnek.
    * A biztonsági mentés, 12:00 órakor szombaton őrzi meg a rendszer 104 hét
    * A biztonsági mentés, 12:00 órakor utolsó szombaton 60 hónapig őrződnek
    * A biztonsági mentés március 12:00 órakor utolsó szombaton 10 évig őrzi meg a rendszer
14. Kattintson a **tovább** , és válassza ki a megfelelő beállítást átvitelére a kezdeti biztonsági másolatot az Azure-bA. Választhat **automatikusan a hálózaton keresztül** vagy **Offline biztonsági mentés**.

    * **Automatikusan a hálózaton keresztül** a biztonsági mentési adatok átvitele az Azure-bA a biztonsági mentéshez megadott ütemezés alapján.
    * Hogyan **Offline biztonsági mentés** működik kifejtett [Offline Backup munkafolyamat az Azure Backup](backup-azure-backup-import-export.md).

    Válassza ki a kezdeti biztonsági másolatot küldeni az Azure, majd kattintson a megfelelő átviteli mechanizmus **tovább**.
15. A szabályzat részletek áttekintése után a **összefoglalás** lapon kattintson a a **csoport létrehozása** gombra kattintva fejezze be a munkafolyamat. Kattintson a **Bezárás** gombra, és a figyelés munkaterület feladatok előrehaladásának figyeléséhez.

    ![Védelmi csoport folyamatban létrehozása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Igény szerinti biztonsági mentés az SQL Server-adatbázis
Amíg az előző lépésekben létrehozott egy biztonsági mentési szabályzatot, az első biztonsági mentés esetén csak egy "helyreállítási pont" jön létre. Ahelyett, hogy a Várakozás az ütemező jelentkezik, az eseményindító létrehozása egy helyreállítási az alábbi lépéseket manuálisan pont.

1. Várjon, amíg a védelmi csoport állapota **OK** az adatbázis, a helyreállítási pont létrehozása előtt.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázist, majd válassza **helyreállítási pont létrehozása**.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Válasszon **az Online védelem** a legördülő menüből, majd kattintson a **OK**. Ezzel elindítja a helyreállítási pont létrehozása az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Megtekintheti a feladat előrehaladását a **figyelés** munkaterület Itt találhatja meg egy folyamatban lévő feladat például az egyik fejezet a következő ábra.

    ![Figyelési konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure-beli SQL Server-adatbázis helyreállítása
A következő lépések szükségesek (SQL Server-adatbázis) védett entitás helyreállítására az Azure-ból.

1. Nyissa meg a DPM-kiszolgáló felügyeleti konzolján. Navigáljon a **helyreállítási** munkaterületet, ahol megtekintheti a kiszolgálók biztonsági mentését a DPM. Tallózással keresse meg a szükséges adatbázis (a kis ReportServer$ MSDPM2012). Válassza ki a **történő helyreállítás** végződik ideje **Online**.

    ![Válassza ki a helyreállítási pont](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevét, és kattintson a **helyreállítása**.

    ![Azure-beli helyreállítása](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A DPM a helyreállítási pont részleteit jeleníti meg. Kattintson a **tovább**. Az adatbázis felülírása, a helyreállítási típus kiválasztása **helyreállítás az eredeti SQL Server-példányra**. Kattintson a **tovább**.

    ![Helyreállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi az adatbázis helyreállítása egy másik SQL Server-példányra, vagy egy különálló hálózati mappába.
4. Az a **adja meg a helyreállítási beállítások** képernyőn kiválaszthatja a helyreállítási beállításokat, például a sávszélesség-szabályozás recovery által használt sávszélesség szabályozása. Kattintson a **tovább**.
5. Az a **összefoglalás** képernyőn látható, hogy eddigi megadott összes helyreállítási konfiguráció. Kattintson a **helyreállítása**.

    A helyreállítási állapot jeleníti meg a helyreállítandó adatbázis. Kattinthat **bezárásához** zárja be a varázslót, és a folyamat előrehaladásának megtekintéséhez a **figyelés** munkaterületen.

    ![A helyreállítási folyamat elindításához](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezése után a visszaállított adatbázis konzisztens alkalmazás, amely.

### <a name="next-steps"></a>További lépések:
• [Az azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
