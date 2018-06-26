---
title: SQL Server-munkaterhelések biztonsági mentése Azure veremben
description: Azure Backup Server használatával védi az SQL Server munkaterhelés Azure veremben.
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: ca7da7ab048b6f7bfdba81aac9bc7702b20ff967
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751797"
---
# <a name="back-up-sql-server-on-stack"></a>Biztonsági másolatot az SQL Server verem
Ez a cikk segítségével konfigurálhatja a Microsoft Azure Backup Server (MABS) Azure veremben SQL Server-adatbázisok védelméhez.

Az SQL Server-adatbázis biztonsági másolatának Azure és az Azure-ból helyreállítási felügyeleti három lépést foglal magában:

1. SQL Server-adatbázisok védelmét a biztonsági mentési házirend létrehozása
2. Igény szerinti biztonsági másolatot készíteni
3. Az adatbázis helyreállítása lemezről, és az Azure-ból

## <a name="before-you-start"></a>Előkészületek

[Telepítése és előkészítése az Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Az Azure SQL Server-adatbázisok védelmét a biztonsági mentési házirend létrehozása
1. A Azure Backup Server felhasználói felületén kattintson a **védelmi** munkaterületen.

2. Az eszközök menüszalagján kattintson **új** egy új védelmi csoport létrehozásához.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)

    Az Azure Backup Server elindul a védelmi csoport varázslót, amely végigvezeti Önt a létrehozása olyan **védelmi csoport**. Kattintson a **Tovább** gombra.

3. Az a **védelmi csoport típusának kiválasztása** képernyőn, jelölje be **kiszolgálók**.

    ![Válassza ki a védelmi csoport típusa: "Kiszolgáló"](./media/backup-azure-backup-sql/pg-servers.png)

4. Az a **csoporttagok kiválasztása** képernyő, a rendelkezésre álló tagok listáját jeleníti meg a különféle adatforrásokból. Kattintson a **+** bontsa ki a mappát, és az almappák felfedése. Kattintson a jelölőnégyzetbe, jelölje ki az elemet.

    ![Válassza ki az SQL-adatbázis](./media/backup-azure-backup-sql/pg-databases.png)

    Az összes kijelölt elemek jelennek meg a kiválasztott tagok listája. A kiszolgálók vagy a védeni kívánt adatbázisok kiválasztását követően kattintson **következő**.

5. Az a **adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét, válassza ki a **online védelmet szeretnék** jelölőnégyzetet.

    ![Adatvédelmi módszer - rövid távú lemezes és Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Az a **rövid távú célok megadása** tartalmazza a szükséges bemeneti adatokat, és kattintson a biztonsági mentési pontok létrehozásához kattintson a jobb **következő**.

    A példában **megőrzési időtartam** van **5 napos**, **szinkronizálási gyakoriság** egyszer minden **15 perc**, vagyis a biztonsági mentés gyakoriság. **Expressz teljes biztonsági mentés** értéke **8:00 óráig**.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A példában is látható 8:00 PM minden nap, a biztonsági mentési pontok hozta létre a módosított adatokat visz át az előző napi 8:00 PM biztonsági mentési pont. Ez a folyamat **expressz teljes biztonsági mentés**. Tranzakciós naplók szinkronizálódnak 15 percenként. Állítsa helyre az adatbázist, 9:00 PM van szüksége, ha a pont készült a napló, az utolsó expressz teljes biztonsági mentési pont (ebben az esetben 8 óra).
   >
   >

7. Az a **tekintse át a lemezkiosztást** képernyőn, ellenőrizze a teljes tárhely, valamint a lemezterület. Kattintson a **Tovább** gombra.

8. Az a **replika-létrehozási módszer kiválasztása**, válassza ki az első helyreállítási pont létrehozása. A kezdeti biztonsági másolatot, manuálisan (hálózati) ki átvitele elkerülheti a sávszélesség torlódás vagy a hálózaton keresztül. Várjon, amíg az első biztonsági mentés átvitele mellett dönt, ha a kezdeti átvitel is megadhatja a időt. Kattintson a **Tovább** gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolatot kell az üzemi kiszolgáló (SQL Server-számítógépen) a teljes adatforrás (SQL Server-adatbázis) átvitele Azure Backup Server. Lehet, hogy ezek az adatok nagy, és az adatok átvitele a hálózati sávszélesség meghaladhatja. Ezért át a kezdeti biztonsági másolatot választhatja: **manuálisan** (cserélhető adathordozóval) sávszélesség torlódás elkerülése érdekében vagy **automatikusan a hálózaton keresztül** (megadott időben).

    Ha a kezdeti biztonsági mentés befejeződött, a biztonsági mentések a többi a kezdeti biztonsági másolatot a növekményes biztonsági mentések. Növekményes biztonsági mentések általában kicsi, és egyszerűen továbbítja a a hálózaton.

9. Adja meg, ha a konzisztencia-ellenőrzés futtatása, és kattintson a **következő**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    Az Azure Backup Server integritását, a biztonsági mentési pont konzisztencia-ellenőrzést hajt végre. Az Azure Backup Server biztonságimásolat-fájl az üzemi kiszolgáló (SQL Server-számítógépen ebben a forgatókönyvben) és a biztonsági másolatot, hogy a fájl ellenőrzőösszeg számítja ki. Ütközés lép, ha feltételezzük, az Azure Backup Server biztonsági másolat fájl sérült. Az Azure Backup Server rectifies az adatok biztonsági másolatai a nem egyeznek az ellenőrzőösszegek megfelelő blokkok küldésével. Mivel a konzisztencia-ellenőrzések teljesítmény-igényes, a konzisztencia-ellenőrzés ütemezése, vagy automatikus.

10. Az adatforrás online védelmét, jelölje be az Azure, majd kattintson a védeni kívánt adatbázisokat **következő**.

    ![Válassza ki az adatforrások](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Válassza ki a biztonsági mentés ütemezése és adatmegőrzési, melyek illeszkednek a szervezet házirendek.

    ![Ütemezés és a megőrzési](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer kerül 12:00 PM és 8 óra (a képernyő alsó részén)

    > [!NOTE]
    > Tanácsos rendelkezik néhány rövid távú helyreállítási pont a lemezen, a gyors helyreállítás. A helyreállítási pontok műveleti helyreállítási érvényesek. Azure magasabb SLA-k a helyes külső helyszíni hely funkcionál, és rendelkezésre állását garantálja.
    >
    >

    **Az ajánlott eljárás**: Ha elindítása után a helyi lemezes biztonsági mentések végezze el az Azure biztonsági mentés ütemezhető, a legutóbbi lemezes biztonsági mentések a rendszer mindig másolja az Azure-bA.

12. Válassza ki az adatmegőrzési házirend-ütemezést. A részleteket az adatmegőrzési működése biztosított [használata Azure Backup lecseréli a szalag infrastruktúra cikk](backup-azure-backup-cloud-as-tape.md).

    ![Adatmegőrzési szabályzat](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * Biztonsági mentések naponta egyszer készít a 12:00 PM és 8 óra (a képernyő alsó részén), és 180 napig megőrződnek.
    * A biztonsági mentés szombaton déli 12:00 órakor 104 hétig őrződnek meg
    * A biztonsági mentés utolsó szombaton déli 12:00 órakor 60 hónapig őrzi meg
    * A biztonsági mentés utolsó szombaton déli 12:00 órakor március 10 évig őrzi meg
13. Kattintson a **következő** , és válassza ki a megfelelő beállítást átvitelére a kezdeti biztonsági másolatot az Azure-bA. Választhat **automatikusan a hálózaton keresztül**

14. Után áttekintheti a házirend adatait az **összegzés** kattintson **csoport létrehozása** a munkafolyamat befejezéséhez. Kattinthat **Bezárás** és a figyelés munkaterület feladatok előrehaladásának figyeléséhez.

    ![Védelmi csoport folyamatban létrehozása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Igény szerinti biztonsági mentés SQL Server-adatbázis
Amíg az előző lépések a biztonsági mentési házirend létrehozása, a "helyreállítási pont" jön létre, csak akkor, ha az első biztonsági mentés akkor következik be. Ahelyett, hogy az ütemezőt, hogy indítsa várakozik, az alábbi eseményindító létrehozása a helyreállítási lépéseket manuálisan mutasson.

1. Várjon, amíg a védelmi csoport állapota **OK** az adatbázis, a helyreállítási pont létrehozása előtt.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázist, és válassza ki a **helyreállítási pont létrehozása**.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Válasszon **Online védelem** a legördülő menüre, majd a **OK** elindítani egy helyreállítási pont létrehozása az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. A feladat előrehaladását a megtekintése a **figyelés** munkaterületen.

    ![Felügyeleti konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból
A következő lépések szükségesek az Azure-ból (SQL Server-adatbázis) védett entitás helyreállítására.

1. Nyissa meg az Azure biztonságimásolat-kiszolgáló kezelési konzolját. Navigáljon a **helyreállítási** munkaterületen, ahol láthatja a védett kiszolgálókon. Keresse meg a szükséges adatbázis (Ez esetben ReportServer$ MSDPM2012). Válassza ki a **helyreállítás** megadott idő egy **Online** mutasson.

    ![Válassza ki a helyreállítási pont](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevét, és kattintson a **helyreállítása**.

    ![Az Azure-ból helyreállítása](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább** gombra. A helyreállítási típus kiválasztása az adatbázis felülírásához **visszaállítás az eredeti SQL Server-példányra**. Kattintson a **Tovább** gombra.

    ![Helyreállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a MABS helyreállíthatja az adatbázist, egy másik SQL Server-példányt, vagy egy különálló hálózati mappába.

4. Az a **adja meg a helyreállítási beállítások** képernyő, kiválaszthatja a helyreállítási beállítások, például a hálózati sávszélesség használatának szabályozása helyreállítási által használt sávszélesség szabályozása. Kattintson a **Tovább** gombra.

5. Az a **összegzés** képernyőn látható, hogy amennyiben a megadott helyreállítási konfigurációk. Kattintson a **helyreállítása**.

    A helyreállítás állapotát jeleníti meg a helyreállítandó adatbázis. Kattinthat **bezárása** a varázsló bezárásához és az a folyamat állapotának megtekintéséhez a **figyelés** munkaterületen.

    ![A helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezése után a visszaállított adatbázis a konzisztens alkalmazás.

## <a name="next-steps"></a>További lépések

Tekintse meg a [biztonsági mentési fájlok és alkalmazás](backup-mabs-files-applications-azure-stack.md) cikk.
Tekintse meg a [biztonsági mentés SharePoint Azure veremben](backup-mabs-sharepoint-azure-stack.md) cikk.
