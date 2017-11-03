---
title: "Az SQL Server számítási feladatait a DPM használata az Azure Backup |} Microsoft Docs"
description: "Az Azure Backup szolgáltatás használatával az SQL Server-adatbázisok biztonsági másolatának bemutatása"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli
editor: 
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: c9edc066ea2edc9cd4b8453047d5584a588174dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Az Azure-ba, a DPM-munkaterhelések biztonsági mentése SQL Server
Ez a cikk végigvezeti az Azure Backup szolgáltatás használatával az SQL Server-adatbázisok biztonsági mentéséhez konfigurációs lépéseket.

Adatbázisok biztonsági mentése SQL Server az Azure-ba, az Azure-fiók szükséges. Ha nincs fiókja, csak néhány perc alatt is létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

Az SQL Server-adatbázis biztonsági másolatának Azure és az Azure-ból helyreállítási felügyeleti három lépést foglal magában:

1. Hozzon létre egy biztonsági mentési házirend, az Azure SQL Server-adatbázisok védelméhez.
2. Hozzon létre igény szerinti biztonsági másolatot az Azure-bA.
3. Az adatbázis helyreállítása az Azure-ból.

## <a name="before-you-start"></a>Előkészületek
Mielőtt elkezdené, győződjön meg arról, hogy minden a [Előfeltételek](backup-azure-dpm-introduction.md#prerequisites) a Microsoft Azure Backup szolgáltatás használatával történő védelméhez munkaterhelések teljesült. Az Előfeltételek fedik le a feladatokat, mint: a mentési tároló létrehozása, töltse le a tároló, az Azure biztonsági mentési ügynök telepítése és a kiszolgáló regisztrálása a tárolóban.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Az Azure SQL Server-adatbázisok védelmét a biztonsági mentési házirend létrehozása
1. A DPM-kiszolgálón kattintson a **védelmi** munkaterületen.
2. Az eszközök menüszalagján kattintson **új** egy új védelmi csoport létrehozásához.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)
3. DPM mutatja be a kezdőképernyőn a útmutatással létrehozása egy **védelmi csoport**. Kattintson a **Tovább** gombra.
4. Válassza ki **kiszolgálók**.

    ![Válassza ki a védelmi csoport típusa: "Kiszolgáló"](./media/backup-azure-backup-sql/pg-servers.png)
5. Bontsa ki az SQL Server-számítógépen, ahol jelen-e az adatbázisok biztonsági mentését. A DPM a biztonsági másolat készíthető, a kiszolgáló a különféle adatforrások jeleníti meg. Bontsa ki a **minden SQL-megosztás** válassza ki a (ebben az esetben azt a kiválasztott ReportServer$ MSDPM2012 és ReportServer$ MSDPM2012TempDB) adatbázisok biztonsági mentését. Kattintson a **Tovább** gombra.

    ![Válassza ki az SQL-adatbázis](./media/backup-azure-backup-sql/pg-databases.png)
6. Adja meg a védelmi csoport nevét, és válassza ki a **online védelmet szeretnék** jelölőnégyzetet.

    ![Adatvédelmi módszer - rövid távú lemez & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Az a **rövid távú célok megadása** képernyőn, a lemezes biztonsági mentési pontok létrehozásához szükséges bemeneti adatok közé tartoznak.

    Itt látható, amely **megőrzési időtartam** értéke *5 napos*, **szinkronizálási gyakoriság** van beállítva, ha minden *15 perc* Ez az a gyakoriság, amellyel a biztonsági mentés használatban van. **Expressz teljes biztonsági mentés** értéke *8:00 óráig*.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 8:00 PM (megfelelően a képernyő bemeneti), egy biztonsági mentési pont hozta létre minden nap a módosított adatokat visz át az előző napi 8:00 PM biztonsági mentési pont. Ez a folyamat **expressz teljes biztonsági mentés**. A naplók szinkronizálódnak 15 percenként tranzakció közben Ha szükség van az adatbázis helyreállításához, 9:00 PM – akkor hozta létre a pont a naplókat a legutóbbi visszajátszását expressz teljes biztonsági mentési pont (ebben az esetben 8 óra).
   >
   >

8. Kattintson a **Tovább** gombra

    A DPM a teljes tárterülete és a potenciális lemezterület-kihasználás jeleníti meg.

    ![A lemezfoglalás](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint a DPM egy kötet található az adatforráshoz (SQL Server-adatbázis), amely a kezdeti biztonsági másolatot hoz létre. Ezt a módszert használja, a logikai lemezkezelő (LDM) korlátozza a DPM védelmi 300 adatforrásokhoz (SQL Server-adatbázisok). A probléma megoldásához, válassza ki a **adatok közös elhelyezése a DPM-Tárolókészletben**, lehetőséget. Ha ezt a beállítást használja, a DPM használ csak egy kötet több adatforrást, amely lehetővé teszi a DPM legfeljebb 2000 SQL-adatbázisok védelméhez.

    Ha **a kötetek méretének automatikus növelése** lehetőséget választja, a termelési adatok növekedésével fiókot a fokozott biztonsági mentési kötet a DPM is. Ha **a kötetek méretének automatikus növelése** beállítás nincs bejelölve, a DPM csökkenti a biztonsági másolatok tárolásának az adatforrásokat a védelmi csoport használatával.
9. A rendszergazdák vagy a hálózaton keresztül történő továbbítása manuálisan (hálózati) ki a kezdeti biztonsági mentési sávszélesség torlódás elkerülése érdekében a választott kap. Ezenkívül konfigurálhatják az idő, ahol az első átvitel akkor fordulhat elő. Kattintson a **Tovább** gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolatot a teljes adatforrás (SQL Server-adatbázis) való átvitele az üzemi kiszolgáló (SQL Server-számítógépen) a DPM-kiszolgálóra van szükség. Lehet, hogy ezek az adatok nagy, és az adatok átvitele a hálózati sávszélesség meghaladhatja. Emiatt a rendszergazdák kiválaszthatják, a kezdeti biztonsági másolatot átvitele: **manuálisan** (cserélhető adathordozóval) sávszélesség torlódás elkerülése érdekében vagy **automatikusan a hálózaton keresztül** (megadott időben).

    Ha a kezdeti biztonsági mentés befejeződött, a biztonsági mentések a többi a kezdeti biztonsági másolatot a növekményes biztonsági mentések. Növekményes biztonsági mentések általában kicsi, és egyszerűen továbbítja a a hálózaton.
10. Adja meg, ha a konzisztencia-ellenőrzés futtatása, és kattintson a **következő**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    A DPM végezheti konzisztencia így ellenőrzi a biztonsági mentési pont integritását. A biztonságimásolat-fájl az üzemi kiszolgáló (SQL Server-számítógépen ebben a forgatókönyvben) és a biztonsági másolatot az adott fájlban a következő DPM ellenőrzőösszeg számítja ki. Ütközés, ha feltételezzük, hogy a biztonsági másolat fájlban a következő DPM megsérült. A DPM a biztonsági másolatot a nem egyeznek az ellenőrzőösszegek megfelelő blokkok elküldésével rectifies. Mivel a konzisztencia-ellenőrzést a teljesítmény-igényes művelet, a rendszergazdák is automatikusan fut, és a konzisztencia-ellenőrzés ütemezése lehetőség van.
11. Az adatforrás online védelmét, jelölje be az Azure, majd kattintson a védeni kívánt adatbázisokat **következő**.

    ![Válassza ki az adatforrások](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Rendszergazdák eldönthetik, hogy a mentési ütemezések és az adatmegőrzési, melyek illeszkednek a szervezet házirendjeiket.

    ![Ütemezés és a megőrzési](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer kerül 12:00 PM és 8 óra (a képernyő alsó részén)

    > [!NOTE]
    > Tanácsos rendelkezik néhány rövid távú helyreállítási pont a lemezen, a gyors helyreállítás. A helyreállítási pontok "műveleti helyreállítási" érvényesek. Azure magasabb SLA-k a helyes külső helyszíni hely funkcionál, és rendelkezésre állását garantálja.
    >
    >

    **Az ajánlott eljárás**: Győződjön meg arról, hogy Azure biztonsági mentések ütemezett helyi lemezes biztonsági mentések a DPM használatának befejezése után. Ez lehetővé teszi a legutóbbi lemezes biztonsági mentés az Azure-bA másolása.

13. Válassza ki az adatmegőrzési házirend-ütemezést. A részleteket az adatmegőrzési működése biztosított [használata Azure Backup lecseréli a szalag infrastruktúra cikk](backup-azure-backup-cloud-as-tape.md).

    ![Adatmegőrzési házirend](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * Biztonsági mentések naponta egyszer készít a 12:00 PM és 8 óra (a képernyő alsó részén), és 180 napig megőrződnek.
    * A biztonsági mentés szombaton déli 12:00 órakor 104 hétig őrződnek meg
    * A biztonsági mentés utolsó szombaton déli 12:00 órakor 60 hónapig őrzi meg
    * A biztonsági mentés utolsó szombaton déli 12:00 órakor március 10 évig őrzi meg
14. Kattintson a **következő** , és válassza ki a megfelelő beállítást átvitelére a kezdeti biztonsági másolatot az Azure-bA. Választhat **automatikusan a hálózaton keresztül** vagy **Offline biztonsági másolat**.

    * **Automatikusan a hálózaton keresztül** a biztonsági mentési adatok átvitele Azure biztonsági mentésre kiválasztott ütemezés szerint.
    * Hogyan **Offline biztonsági másolat** works van arra a [Offline biztonsági másolat munkafolyamat Azure backup](backup-azure-backup-import-export.md).

    Válassza ki a kezdeti biztonsági másolatot küldeni az Azure, majd kattintson a megfelelő adatátviteli mechanizmusát **következő**.
15. Után áttekintheti a házirend adatait a **összegzés** a kattintson a **csoport létrehozása** gombra kattintva fejezze be a munkafolyamat. Kattintson a **Bezárás** gombra, és a figyelés munkaterület feladatok előrehaladásának figyeléséhez.

    ![Védelmi csoport folyamatban létrehozása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Igény szerinti biztonsági mentés SQL Server-adatbázis
Amíg az előző lépések a biztonsági mentési házirend létrehozása, a "helyreállítási pont" jön létre, csak akkor, ha az első biztonsági mentés akkor következik be. Ahelyett, hogy az ütemezőt, hogy indítsa várakozik, az alábbi eseményindító létrehozása a helyreállítási lépéseket manuálisan mutasson.

1. Várjon, amíg a védelmi csoport állapota **OK** az adatbázis, a helyreállítási pont létrehozása előtt.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázist, és válassza ki a **helyreállítási pont létrehozása**.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Válasszon **Online védelem** a legördülő menüre, majd a **OK**. Ezzel elindítja a helyreállítási pont létrehozása az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. A feladat előrehaladását a megtekintheti a **figyelés** munkaterület találhatók egy folyamatban lévő feladat, például az egyik írja le az alábbi ábrán.

    ![Felügyeleti konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból
A következő lépések szükségesek az Azure-ból (SQL Server-adatbázis) védett entitás helyreállítására.

1. Nyissa meg a DPM-kiszolgáló kezelési konzolján. Navigáljon a **helyreállítási** munkaterületen, ahol láthatja a kiszolgálók biztonsági mentése a DPM. Keresse meg a szükséges adatbázis (Ez esetben ReportServer$ MSDPM2012). Válassza ki a **helyreállítás** amelyek végződik **Online**.

    ![Válassza ki a helyreállítási pont](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevét, és kattintson a **helyreállítása**.

    ![Az Azure-ból helyreállítása](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A DPM a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább** gombra. A helyreállítási típus kiválasztása az adatbázis felülírásához **visszaállítás az eredeti SQL Server-példányra**. Kattintson a **Tovább** gombra.

    ![Helyreállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi, hogy az adatbázis helyreállítása másik SQL Server-példányt vagy egy különálló hálózati mappába.
4. Az a **adja meg a helyreállítási beállítások** képernyő, kiválaszthatja a helyreállítási beállítások, például a hálózati sávszélesség használatának szabályozása helyreállítási által használt sávszélesség szabályozása. Kattintson a **Tovább** gombra.
5. Az a **összegzés** képernyőn látható, hogy amennyiben a megadott helyreállítási konfigurációk. Kattintson a **helyreállítása**.

    A helyreállítás állapotát jeleníti meg a helyreállítandó adatbázis. Kattinthat **bezárása** a varázsló bezárásához és az a folyamat állapotának megtekintéséhez a **figyelés** munkaterületen.

    ![A helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezése után a visszaállított adatbázis a konzisztens alkalmazás.

### <a name="next-steps"></a>További lépések:
• [Azure biztonsági mentési – gyakori kérdések](backup-azure-backup-faq.md)
