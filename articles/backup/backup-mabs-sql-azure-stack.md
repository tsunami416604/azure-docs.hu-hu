---
title: Az SQL Server-munkaterhelések biztonsági mentése az Azure Stackben
description: Az Azure Backup Server használatával védi az SQL Server számítási feladatok az Azure Stacken.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493507"
---
# <a name="back-up-sql-server-on-stack"></a>SQL Server biztonsági mentése a verem
Ez a cikk segítségével konfigurálhatja a Microsoft Azure Backup Server (MABS) védelme érdekében az SQL Server-adatbázisok az Azure Stacken.

Az SQL Server adatbázis biztonsági másolatának az Azure és a helyreállítás az Azure felügyeleti három lépésből áll:

1. Az SQL Server-adatbázisok védelméhez biztonsági mentési szabályzat létrehozása
2. Igény szerinti biztonsági másolat készítése
3. Az adatbázis helyreállítása a lemezekről, és az Azure-ból

## <a name="before-you-start"></a>Előkészületek

[Telepítse és készítse elő az Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Az Azure SQL Server-adatbázisok védelmét egy biztonsági mentési szabályzat létrehozása
1. Az Azure Backup Server felhasználói felületén kattintson a **védelmi** munkaterületen.

2. Az eszközök menüszalagján kattintson **új** hozhat létre egy új védelmi csoportot.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)

    Az Azure Backup Server elindul a védelmi csoport varázslót, amely végigvezeti létrehozása egy **védelmi csoport**. Kattintson a **tovább**.

3. Az a **védelmi csoport típusának kiválasztása** képernyőn válassza ki **kiszolgálók**.

    ![Válassza ki a védelmi csoport típusának – "Kiszolgáló"](./media/backup-azure-backup-sql/pg-servers.png)

4. Az a **csoporttagok kiválasztása** képernyő, a rendelkezésre álló tagok listája a különböző adatforrásokat jeleníti meg. Kattintson a **+** bontsa ki a mappát, és az almappák felfedéséhez. Jelölje be a jelölőnégyzetet, jelölje ki az elemet.

    ![Válassza ki az SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    Az összes kijelölt elemeket a kijelölt tagok listája jelenik meg. A kiszolgálók és a védeni kívánt adatbázisokat kiválasztása után kattintson az **tovább**.

5. Az a **adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét, válassza ki a **online védelmet szeretnék** jelölőnégyzetet.

    ![Adatvédelmi módszer - rövid távú lemezes és Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Az a **rövid távú célok megadása** képernyőn, tartalmazza a szükséges bemeneti adatokat, és kattintson a biztonsági mentési pontok létrehozásához **tovább**.

    A példában **megőrzési** van **5 nap**, **szinkronizálási gyakoriság** egyszer minden **15 perc**, azaz a biztonsági mentés gyakorisága. **Expressz teljes biztonsági mentés** értékre van állítva **8:00 óráig**.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A példában látható: 8:00 Órakor naponta egy biztonsági mentési pont hozza létre a módosított adatokat visz át az előző nap 8:00 Órakor biztonsági mentési pontok. Ez a folyamat **expressz teljes biztonsági mentés**. Tranzakciós naplók szinkronizálódnak 15 percenként. Ha szeretné helyreállítani az adatbázist este 9:00-kor, a pont jön létre a naplók az elmúlt expressz teljes biztonsági mentési pont (ebben az esetben 8 óra).
   >
   >

7. Az a **tekintse át a lemezkiosztást** képernyő, ellenőrizze a teljes rendelkezésre álló tárhely és a lemezterület. Kattintson a **tovább**.

8. Az a **replika-létrehozási módszer kiválasztása**, adja meg, hogyan hozhat létre az első helyreállítási pontot. A kezdeti biztonsági mentés manuálisan (hálózat) ki átvitele elkerülése érdekében a sávszélesség torlódás vagy a hálózaton keresztül. Ha az első biztonsági mentés átviteli várakozási választja, megadhatja az idő számára a kezdeti átvitel. Kattintson a **tovább**.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolatot kell az üzemi kiszolgáló (az SQL Server-gép) a teljes adatforrásra (SQL Server-adatbázis) átvitele az Azure Backup Server. Lehet, hogy ezeket az adatokat nagy, és az adatok hálózati átvitele közben túllépheti a sávszélességet. Ebből kifolyólag választhat vihetők át a kezdeti biztonsági mentés: **Manuálisan** (a cserélhető adathordozó használatával) elkerülése érdekében a sávszélesség szűk keresztmetszet, vagy **automatikusan a hálózaton keresztül** (megadott időben).

    A kezdeti biztonsági mentés befejezése után a biztonsági mentések a többi olyan növekményes biztonsági mentések a kezdeti biztonsági másolatot. A növekményes biztonsági mentések általában kicsi, és egyszerűen továbbítja a hálózaton.

9. Válassza ki, ha azt szeretné, hogy a konzisztencia-ellenőrzés futtatása, és kattintson a **tovább**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    Az Azure Backup Server a biztonsági mentési pontok sértetlenségének konzisztencia-ellenőrzést hajt végre. Az Azure Backup Server számítja ki a biztonságimásolat-fájl az üzemi kiszolgáló (SQL Server-gép ebben a forgatókönyvben) és a mentett adatok erre a fájlra vonatkozó ellenőrzőösszeget. Ütközés van, ha feltételezzük a az Azure Backup Server biztonsági másolat fájl sérült. Az Azure Backup Server a mentett adatok rectifies az ellenőrzőösszegek megfelelő blokkok küldésével. Mivel a konzisztencia-ellenőrzések nagy teljesítményt, a konzisztencia-ellenőrzés ütemezése, vagy automatikus.

10. Az adatforrás online védelmét, jelölje ki az adatbázisok, Azure, majd kattintson a védendő **tovább**.

    ![Adatforrás kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Válassza ki a biztonsági mentési ütemezés és adatmegőrzési szabályzatok, amelyek megfelelnek a szervezet szabályzatai.

    ![Ütemezése és megőrzése](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer készít 12:00 Órakor, és 8-kor (a képernyő alsó részén)

    > [!NOTE]
    > Tanácsos rendelkezik néhány rövid távú helyreállítási pontok a lemezen, a gyors helyreállítás. Ezen helyreállítási pontok szolgálnak az operatív helyreállítás. Az Azure egy jó telephelyen kívüli helyre a magasabb SLA-k funkcionál, és garantált rendelkezésre állás.
    >
    >

    **Ajánlott eljárás**: Ha az Azure-ba, és elindítja a helyi lemezes biztonsági mentések végezze el a biztonsági mentések ütemez, a legutóbbi lemezes biztonsági mentések mindig lesz másolva az Azure.

12. Válassza ki a megőrzési házirend-ütemezést. A részletek a megtartási házirend működéséről biztosított [használata az Azure Backup cserélje le a szalagos infrastruktúra cikk](backup-azure-backup-cloud-as-tape.md).

    ![Adatmegőrzési szabályzat](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * Biztonsági mentések naponta egyszer készít 12:00 Órakor, és 8-kor (a képernyő alsó részén), és 180 napig megőrződnek.
    * A biztonsági mentés, 12:00 órakor szombaton őrzi meg a rendszer 104 hét
    * A biztonsági mentés, 12:00 órakor utolsó szombaton 60 hónapig őrződnek
    * A biztonsági mentés március 12:00 órakor utolsó szombaton 10 évig őrzi meg a rendszer
13. Kattintson a **tovább** , és válassza ki a megfelelő beállítást átvitelére a kezdeti biztonsági másolatot az Azure-bA. Választhat **automatikusan a hálózaton keresztül**

14. A szabályzat részletek áttekintése után a **összefoglalás** kattintson **csoport létrehozása** a munkafolyamat befejezéséhez. Kattinthat **Bezárás** és a figyelés munkaterület feladatok előrehaladásának figyeléséhez.

    ![Védelmi csoport folyamatban létrehozása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Igény szerinti biztonsági mentés az SQL Server-adatbázis
Amíg az előző lépésekben létrehozott egy biztonsági mentési szabályzatot, az első biztonsági mentés esetén csak egy "helyreállítási pont" jön létre. Ahelyett, hogy a Várakozás az ütemező jelentkezik, az eseményindító létrehozása egy helyreállítási az alábbi lépéseket manuálisan pont.

1. Várjon, amíg a védelmi csoport állapota **OK** az adatbázis, a helyreállítási pont létrehozása előtt.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázist, majd válassza **helyreállítási pont létrehozása**.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Válasszon **az Online védelem** a legördülő menüből, majd kattintson a **OK** elindítani egy helyreállítási pont létrehozása az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. A feladat előrehaladásának megtekintéséhez a **figyelés** munkaterületen.

    ![Figyelési konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure-beli SQL Server-adatbázis helyreállítása
A következő lépések szükségesek (SQL Server-adatbázis) védett entitás helyreállítására az Azure-ból.

1. Nyissa meg az Azure Backup Server felügyeleti konzolt. Navigáljon a **helyreállítási** munkaterületet, ahol megtekintheti a védett kiszolgálókon. Tallózással keresse meg a szükséges adatbázis (a kis ReportServer$ MSDPM2012). Válassza ki a **történő helyreállítás** megadott idő egy **Online** mutasson.

    ![Válassza ki a helyreállítási pont](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevét, és kattintson a **helyreállítása**.

    ![Azure-beli helyreállítása](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS a helyreállítási pont részleteit jeleníti meg. Kattintson a **tovább**. Az adatbázis felülírása, a helyreállítási típus kiválasztása **helyreállítás az eredeti SQL Server-példányra**. Kattintson a **tovább**.

    ![Helyreállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a MABS helyreállítja az adatbázist egy másik SQL Server-példányra, vagy egy különálló hálózati mappába.

4. Az a **adja meg a helyreállítási beállítások** képernyőn kiválaszthatja a helyreállítási beállításokat, például a sávszélesség-szabályozás recovery által használt sávszélesség szabályozása. Kattintson a **tovább**.

5. Az a **összefoglalás** képernyőn látható, hogy eddigi megadott összes helyreállítási konfiguráció. Kattintson a **helyreállítása**.

    A helyreállítási állapot jeleníti meg a helyreállítandó adatbázis. Kattinthat **bezárásához** zárja be a varázslót, és a folyamat előrehaladásának megtekintéséhez a **figyelés** munkaterületen.

    ![A helyreállítási folyamat elindításához](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezése után a visszaállított adatbázis konzisztens alkalmazás, amely.

## <a name="next-steps"></a>További lépések

Tekintse meg a [fájlok és alkalmazások biztonsági másolatának](backup-mabs-files-applications-azure-stack.md) cikk.
Tekintse meg a [a SharePoint biztonsági mentése az Azure Stacken](backup-mabs-sharepoint-azure-stack.md) cikk.
