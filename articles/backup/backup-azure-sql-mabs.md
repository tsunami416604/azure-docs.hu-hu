---
title: Az SQL Server biztonsági mentése az Azure Backup Server használatával
description: Ebben a cikkben megtudhatja, hogy mi a konfiguráció az SQL Server-adatbázisok microsoft Azure Backup Server (MABS) használatával történő biztonsági mentéséhez.
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505945"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Az SQL Server biztonsági mentése az Azure-ba az Azure Backup Server használatával

Ez a cikk segítséget nyújt az SQL Server-adatbázisok biztonsági másolatainak beállításához a Microsoft Azure Backup Server (MABS) használatával.

SQL Server-adatbázis biztonsági mentése és helyreállítása az Azure-ból:

1. Hozzon létre egy biztonsági mentési szabályzatot az SQL Server-adatbázisok védelmére az Azure-ban.
1. Igény szerinti biztonsági másolatokat hozhat létre az Azure-ban.
1. Az azure-beli adatbázis helyreállítása.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, győződjön meg arról, hogy [telepítette és előkészítette az Azure Backup Server](backup-azure-microsoft-azure-backup.md)alkalmazást.

## <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása 

Az SQL Server-adatbázisok azure-beli védelméhez először hozzon létre egy biztonsági mentési szabályzatot:

1. Az Azure Backup Server ben válassza ki a **Protection** munkaterületet.
1. Védelmi csoport létrehozásához válassza az **Új** lehetőséget.

    ![Védelmi csoport létrehozása az Azure Backup Server ben](./media/backup-azure-backup-sql/protection-group.png)
1. A kezdőlapon tekintse át a védelmi csoport létrehozásáról szóló útmutatót. Ezután válassza a **Tovább**gombot.
1. A védelmi csoport típusához válassza a **Kiszolgálók**lehetőséget.

    ![Válassza ki a Kiszolgálók védelmi csoport típusát](./media/backup-azure-backup-sql/pg-servers.png)
1. Bontsa ki azt az SQL Server-gépet, amelyről a biztonsági másolatot szeretne tenni. Láthatja azokat az adatforrásokat, amelyekről biztonsági másolatot lehet kapni akiszolgálóról. Bontsa ki **az összes SQL-megosztást,** majd jelölje ki a biztonsági másolatot létrehozni kívánt adatbázisokat. Ebben a példában a ReportServer$MSDPM2012 és a ReportServer$MSDPM2012TempDB lehetőséget választjuk. Válassza a **Tovább lehetőséget.**

    ![SQL Server-adatbázis kijelölése](./media/backup-azure-backup-sql/pg-databases.png)
1. Nevezze el a védelmi csoportot, majd válassza az **Online védelem lehetőséget.**

    ![Válasszon adatvédelmi módszert – rövid távú lemezvédelem vagy online Azure-védelem](./media/backup-azure-backup-sql/pg-name.png)
1. A **Rövid távú célok megadása** lapon adja meg a lemezbiztonsági pontok létrehozásához szükséges bemeneteket.

    Ebben a példában **a megőrzési tartomány** *5 napra*van állítva. A biztonsági mentés **szinkronizálási gyakorisága** *15 percenként*egyszer van beállítva. **Az Expressz teljes biztonsági mentése** *20:00-ra*van állítva.

    ![Rövid távú célok beállítása a biztonsági mentés védelmére](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Ebben a példában egy biztonsági mentési pont jön létre minden nap 20:00 órakor. Az előző napi 20:00-as biztonsági mentési pont óta módosított adatok átvitele történik. Ezt a folyamatot **expressz teljes biztonsági mentésnek nevezzük.** Bár a tranzakciónaplók szinkronizálása 15 percenként történik, ha 21:00-kor kell helyreállítanunk az adatbázist, akkor a pont az utolsó expressz teljes biztonsági mentési pont naplóinak visszajátszásával jön létre, amely ebben a példában 20:00.
   >
   >

1. Válassza a **Tovább lehetőséget.** A MABS a rendelkezésre álló teljes tárhelyet mutatja. Azt is mutatja, a potenciális lemezterület-kihasználtság.

    ![Lemezfoglalás beállítása a MABS-ben](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint a MABS adatforrásonként egy kötetet (SQL Server adatbázis) hoz létre. A kötet a kezdeti biztonsági másolathoz használatos. Ebben a konfigurációban a Logikai lemezkezelő (LDM) a MABS-védelmet 300 adatforrásra (SQL Server-adatbázisokra) korlátozza. A korlátozás megkerüléséhez válassza **az adatok közös megkeresése lehetőséget a DPM-tárolókészletben.** Ha ezt a beállítást használja, a MABS egyetlen kötetet használ több adatforráshoz. Ez a beállítás lehetővé teszi a MABS számára, hogy akár 2000 SQL Server-adatbázist is megvédjen.

    Ha a Kötetek automatikus növelése lehetőséget **választja,** akkor a MABS a termelési adatok növekedésével a megnövekedett biztonsági mentési kötetet is figyelembe veheti. Ha nem választja a **Kötetek automatikus növekedése**lehetőséget, akkor a MABS a biztonsági mentési tárolót a védelmi csoport adatforrásaira korlátozza.
1. Ha Ön rendszergazda, választhatja ezt a kezdeti biztonsági mentést **automatikusan a hálózaton keresztül,** és kiválaszthatja az átvitel idejét. Vagy válassza a biztonsági mentés **manuális** átvitelét. Ezután válassza a **Tovább**gombot.

    ![Kópia-létrehozási módszer kiválasztása a MABS-ben](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolat a teljes adatforrás (SQL Server-adatbázis) átvitelét igényli. A biztonsági mentési adatok az éles kiszolgálóról (SQL Server-gép) a MABS-re kerül. Ha ez a biztonsági másolat nagy, akkor az adatok hálózaton keresztüli átvitele sávszélesség-torlódást okozhat. Ezért a rendszergazdák dönthetnek úgy, hogy cserélhető adathordozót használnak a kezdeti biztonsági másolat **manuális átviteléhez.** Vagy átvihetik az adatokat **automatikusan a hálózaton keresztül** egy adott időpontban.

    A kezdeti biztonsági mentés befejezése után a biztonsági mentések növekményesen folytatódnak a kezdeti biztonsági másolaton. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.
1. Adja meg, hogy mikor szeretné futtatni a konzisztencia-ellenőrzést. Ezután válassza a **Tovább**gombot.

    ![A konzisztencia-ellenőrzés futtatásának megtervezése](./media/backup-azure-backup-sql/pg-consistent.png)

    A MABS konzisztencia-ellenőrzést futtathat a biztonsági mentési pont sértetlenségén. Kiszámítja az éles kiszolgálón lévő biztonsági másolat fájljának (ebben a példában az SQL Server-gépen) és a fájl biztonsági mentési adatainak ellenőrzőösszegét a MABS-ben. Ha az ellenőrzés ütközést talál, akkor a MABS biztonsági másolatot követő fájlja sérültnek minősül. A MABS az ellenőrzőösszeg-eltérésnek megfelelő blokkok elküldésével javítja a biztonsági másolatot. Mivel a konzisztencia-ellenőrzés teljesítményigényes művelet, a rendszergazdák dönthetnek úgy, hogy ütemezik a konzisztencia-ellenőrzést, vagy automatikusan futtatják azt.
1. Válassza ki az Azure-ban védni kívánt adatforrásokat. Ezután válassza a **Tovább**gombot.

    ![Válassza ki a védeni kívánt adatforrásokat az Azure-ban](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Ha Ön rendszergazda, kiválaszthatja a szervezet szabályzatainak megfelelő biztonsági mentési ütemezéseket és adatmegőrzési házirendeket.

    ![Ütemezések és adatmegőrzési házirendek kiválasztása](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta 12:00 és 20:00 órakor készülnek.

    > [!TIP]
    > A gyors helyreállítás érdekében tartson néhány rövid távú helyreállítási pontot a lemezen. Ezek a helyreállítási pontok a működési helyreállításhoz használatosak. Az Azure jó külső helyként szolgál, magasabb SLA-kat és garantált rendelkezésre állást biztosítva.
    >
    > Az Adatvédelmi kezelő (DPM) segítségével ütemezheti az Azure Backups-t a helyi lemezbiztonsági mentések befejezése után. Ha ezt a gyakorlatot követi, a rendszer átmásolja a legújabb lemezbiztonsági mentést az Azure-ba.
    >


1. Válassza ki az adatmegőrzési házirend ütemezését. Az adatmegőrzési szabályzat működéséről az [Azure Backup használata a szalagos infrastruktúra cseréjéhez](backup-azure-backup-cloud-as-tape.md)című témakörben talál további információt.

    ![Adatmegőrzési házirend kiválasztása a MABS-ben](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentéseket naponta 12:00 és 20:00 órakor készítik. 180 napig őrzik őket.
    * A biztonsági mentés szombaton 12:00-kor tartják 104 hétig.
    * A hónap utolsó szombatjáról 12:00-kor készült biztonsági mentés60 hónapig tartatva.
    * A biztonsági mentés március utolsó szombatján 12:00-kor 10 évig tartatva.

    Az adatmegőrzési szabály kiválasztása után válassza a **Tovább**gombot.
1. Válassza ki, hogyan szeretné átvinni a kezdeti biztonsági másolat át az Azure-ba.

    * A **Hálózaton keresztül automatikusan** beállítás követi a biztonsági mentésütemezést az adatok Azure-ba való átviteléhez.
    * A kapcsolat **nélküli biztonsági mentésről**a Kapcsolat nélküli biztonsági másolat – Áttekintés című [témakörben](offline-backup-overview.md)olvashat bővebben.

    Miután kiválasztotta az átviteli mechanizmust, válassza a **Tovább gombot.**
1. Az **Összegzés** lapon tekintse át a házirend részleteit. Ezután válassza **a Csoport létrehozása**lehetőséget. A Bezárás lehetőséget **választhatja,** és megtekintheti a feladat előrehaladását a **Figyelés** munkaterületen.

    ![A védelmi csoport létrehozásának előrehaladása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>SQL Server adatbázis igény szerinti biztonsági másolatainak létrehozása

Az első biztonsági mentés kor létrejön egy helyreállítási pont. Ahelyett, hogy megvárna az ütemezés futtatására, manuálisan aktiválhatja egy helyreállítási pont létrehozását:

1. A védelmi csoportban győződjön meg arról, hogy az adatbázis állapota **rendben**van.

    ![Az adatbázis állapotát megjelenítő védelmi csoport](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kattintson a jobb gombbal az adatbázisra, és válassza **a Helyreállítási pont létrehozása parancsot.**

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. A legördülő menüben válassza az **Online védelem**lehetőséget. Ezután válassza **az OK gombot** egy helyreállítási pont létrehozásához az Azure-ban.

    ![Helyreállítási pont létrehozásának megkezdése az Azure-ban](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. A feladat előrehaladását a Figyelés munkaterületen **tekintheti** meg. 

    ![A feladat előrehaladásának megtekintése a Figyelés konzolon](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból

Védett entitás, például egy SQL Server-adatbázis helyreállítása az Azure-ból:

1. Nyissa meg a DPM-kiszolgálófelügyeleti konzolt. Nyissa meg a **Helyreállítási** munkaterületet, és tekintse meg a DPM által biztonsági mentést lefolytató kiszolgálókat. Válassza ki az adatbázist (ebben a példában ReportServer$MSDPM2012). Válassza ki az **Online**végződésű **helyreállítási időt.**

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kattintson a jobb gombbal az adatbázis nevére, és válassza a **Helyreállítás parancsot.**

    ![Adatbázis helyreállítása az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. A DPM a helyreállítási pont részleteit jeleníti meg. Válassza a **Tovább lehetőséget.** Az adatbázis felülírásához válassza a Helyreállítás az **SQL Server eredeti példányára**helyreállítási típust. Ezután válassza a **Tovább**gombot.

    ![Adatbázis helyreállítása az eredeti helyére](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi az adatbázis helyreállítását egy másik SQL Server-példányba vagy egy önálló hálózati mappába.
1. A **Helyreállítási beállítások megadása** lapon kiválaszthatja a helyreállítási beállításokat. A rendszer például a **hálózati sávszélesség-használat szabályozásának** beállításával szabályozhatja a helyreállítási sávszélességet. Ezután válassza a **Tovább**gombot.
1. Az **Összegzés** lapon látható az aktuális helyreállítási konfiguráció. Válassza **a Helyreállítás**lehetőséget.

    A helyreállítási állapot azt mutatja, hogy az adatbázis helyreállt. A Varázsló bezárásához és a **Figyelés** munkaterület állapotának megtekintéséhez válassza a **Bezárás** lehetőséget.

    ![A helyreállítási folyamat megkezdése](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Amikor a helyreállítás befejeződött, a visszaállított adatbázis konzisztens az alkalmazással.

### <a name="next-steps"></a>További lépések

További információ: [Azure Backup GYAKORI KÉRDÉSEK](backup-azure-backup-faq.md).
