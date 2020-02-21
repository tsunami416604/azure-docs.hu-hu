---
title: SQL Server biztonsági mentése az Azure-ba DPM számítási feladatokkal
description: Bevezetés SQL Server adatbázisok biztonsági mentésére az Azure Backup szolgáltatás használatával
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505930"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>SQL Server biztonsági mentése az Azure-ba DPM számítási feladatokkal

Ez a cikk végigvezeti a SQL Server adatbázisok Azure Backup használatával történő biztonsági mentésének konfigurációs lépésein.

SQL Server adatbázisok Azure-ba történő biztonsági mentéséhez Azure-fiókra van szükség. Ha még nem rendelkezik ilyennel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. További információ: [az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

SQL Server-adatbázis biztonsági mentése az Azure-ba és az Azure-ból való helyreállítás:

1. Hozzon létre egy biztonsági mentési szabályzatot SQL Server adatbázisok védelméhez az Azure-ban.
1. Hozzon létre igény szerinti biztonsági másolatokat az Azure-ban.
1. Az adatbázis helyreállítása az Azure-ból.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette az Azure Backup használatának [előfeltételeit](backup-azure-dpm-introduction.md#prerequisites-and-limitations) a munkaterhelések elleni védelemhez. Íme néhány előfeltételt jelentő feladat: 
* Hozzon létre egy backup-tárolót.
* Tár hitelesítő adatainak letöltése. 
* Telepítse a Azure Backup ügynököt.
* Regisztrálja a kiszolgálót a tárolóban.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása 

SQL Server-adatbázisok védelméhez az Azure-ban először hozzon létre egy biztonsági mentési szabályzatot:

1. A Data Protection Manager (DPM) kiszolgálón válassza a **védelem** munkaterületet.
1. A védelmi csoport létrehozásához válassza az **új** lehetőséget.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)
1. A kezdőlapon tekintse át a védelmi csoport létrehozásával kapcsolatos útmutatást. Ezután kattintson a **Tovább** gombra.
1. Válassza a **kiszolgálók**lehetőséget.

    ![A kiszolgálók védelmi csoport típusának kiválasztása](./media/backup-azure-backup-sql/pg-servers.png)
1. Bontsa ki azt a SQL Server gépet, ahol a biztonsági másolatot készíteni kívánt adatbázisok találhatók. Megtekintheti azokat az adatforrásokat, amelyekről biztonsági másolatot készíthet az adott kiszolgálóról. Bontsa ki az **összes SQL-megosztás** elemet, majd válassza ki azokat az adatbázisokat, amelyekről biztonsági másolatot szeretne készíteni. Ebben a példában a reportserver $ MSDPM2012 és a reportserver $ MSDPM2012TempDB választjuk. Ezután kattintson a **Tovább** gombra.

    ![SQL Server-adatbázis kiválasztása](./media/backup-azure-backup-sql/pg-databases.png)
1. Nevezze el a védelmi csoportot, majd válassza az **online védelmet szeretnék**lehetőséget.

    ![Adatvédelmi módszer választása – rövid távú lemezes védelem vagy online Azure-védelem](./media/backup-azure-backup-sql/pg-name.png)
1. A **rövid távú célok megadása lapon adja** meg a szükséges bemeneteket a lemezre történő biztonsági mentési pontok létrehozásához.

    Ebben a példában a **megőrzési tartomány** értéke *5 nap*. A biztonsági mentési **szinkronizálás gyakorisága** *15 percenként*egyszer van beállítva. Az **expressz teljes biztonsági mentés** *8:00 órakor*van beállítva.

    ![Rövid távú célok beállítása a biztonsági másolatok védelméhez](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Ebben a példában a rendszer minden nap 8:00 ÓRAKOR létrehoz egy biztonsági mentési pontot. Az előző nap 8:00 PM biztonsági mentési pontja óta módosított adat átvitele megtörtént. Ezt a folyamatot **expressz teljes biztonsági mentésnek**nevezzük. Bár a tranzakciónaplók szinkronizálása 15 percenként történik, ha az adatbázist 9:00 ÓRAKOR kell helyreállítani, akkor a pont a legutóbbi expressz teljes biztonsági mentési pontról, amely ebben a példában a 8:00.
   >
   >

1. Kattintson a **Tovább** gombra. A DPM megjeleníti a rendelkezésre álló teljes tárolóhelyet. Emellett megjeleníti a lemezterület lehetséges kihasználtságát is.

    ![Lemez kiosztásának beállítása](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint az DPM egy kötetet hoz létre adatforrásként (SQL Server adatbázis). A kötet a kezdeti biztonsági másolathoz használatos. Ebben a konfigurációban a logikai lemezkezelő (LDM) a 300 adatforrásokra (SQL Server adatbázisokra) korlátozza a DPM védelmét. Ennek a korlátozásnak a megkerüléséhez válassza az **DPM-tárolóban található adatkeresés**lehetőséget. Ha ezt a beállítást használja, a DPM egyetlen kötetet használ több adatforráshoz. Ez a beállítás lehetővé teszi, hogy a DPM akár 2 000 SQL Server-adatbázist is megvédjen.

    Ha a **kötetek automatikus növelését**választja, akkor a DPM képes a megnövekedett biztonsági mentési kötetre, mert az éles adatmennyiség növekszik. Ha nem választja ki **automatikusan a kötetek növekedését**, a DPM korlátozza a biztonsági mentési tárolót a védelmi csoport adatforrásaira.

1. Ha Ön rendszergazda, dönthet úgy, hogy a kezdeti biztonsági mentést **automatikusan továbbítja a hálózaton keresztül** , és kiválasztja az átvitel időpontját. Vagy válassza a biztonsági mentés **manuális** átvitelét. Ezután kattintson a **Tovább** gombra.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolat a teljes adatforrás (SQL Server adatbázis) átvitelét igényli. A biztonsági mentési adatok az üzemi kiszolgálóról (SQL Server gépről) a DPM-kiszolgálóra kerülnek. Ha a biztonsági mentés nagy méretű, akkor az adatátvitelt a hálózaton keresztül okozhatja a sávszélesség zsúfoltsága. Emiatt a rendszergazdák úgy dönthetnek, hogy cserélhető adathordozót használnak a kezdeti biztonsági mentés **manuális**átviteléhez. Az adatok pedig automatikusan átvihetők **a hálózaton keresztül** egy adott időpontban.

    A kezdeti biztonsági mentés befejeződése után a biztonsági mentések a kezdeti biztonsági másolaton fokozatosan folytatódnak. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.
    
1. Válassza ki a konzisztencia-ellenőrzés futtatásának idejét. Ezután kattintson a **Tovább** gombra.

    ![A konzisztencia-ellenőrzés futtatási idejének kiválasztása](./media/backup-azure-backup-sql/pg-consistent.png)

    A DPM képes konzisztencia-ellenőrzés futtatására a biztonsági mentési pont integritásán. Kiszámítja a biztonságimásolat-fájl ellenőrzőösszegét az üzemi kiszolgálón (ebben a példában a SQL Server gép) és az adott fájl biztonsági másolatait a DPM-ben. Ha az ellenőrzési ütközést észlel, akkor a rendszer a DPM található biztonsági másolati fájlt is megsérültnek tekinti. A DPM az ellenőrzőösszeg-eltérésnek megfelelő blokkok küldésével javítja a biztonsági másolatban szereplő adatokat. Mivel a konzisztencia-ellenőrzés teljesítmény-igényes művelet, a rendszergazdák dönthetnek úgy, hogy a konzisztencia-ellenőrzés vagy a Futtatás automatikusan történik.

1. Válassza ki az Azure-ban védetté tenni kívánt adatforrásokat. Ezután kattintson a **Tovább** gombra.

    ![Válassza ki az Azure-ban védetté tenni kívánt adatforrásokat](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Ha Ön rendszergazda, kiválaszthatja a szervezeti szabályzatoknak megfelelő biztonsági mentési ütemterveket és adatmegőrzési házirendeket.

    ![Ütemtervek és adatmegőrzési szabályzatok kiválasztása](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta 12:00 ÓRAKOR és 8:00 ÓRAKOR készültek.

    > [!TIP]
    > A gyors helyreállításhoz tartsa meg néhány rövid távú helyreállítási pontot a lemezen. Ezek a helyreállítási pontok az operatív helyreállításhoz használatosak. Az Azure jó külső helyszínen működik, és magasabb SLA-kat és garantált rendelkezésre állást biztosít.
    >
    > A DPM használatával ütemezhet Azure-alapú biztonsági mentéseket a helyi lemezes biztonsági mentések befejezését követően. Ha ezt a gyakorlatot követi, a rendszer átmásolja a lemez legújabb biztonsági mentését az Azure-ba.
    >

1. Válassza ki az adatmegőrzési szabály ütemtervét. További információ az adatmegőrzési szabályzat működéséről: [Azure Backup használata a szalagos infrastruktúra lecseréléséhez](backup-azure-backup-cloud-as-tape.md).

    ![Adatmegőrzési szabály kiválasztása](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentések naponta 12:00 ÓRAKOR és 8:00 ÓRAKOR készültek. 180 napig tartanak.
    * A szombaton 12:00 ÓRAKOR készült biztonsági mentés 104 hétig tart.
    * A hónap utolsó szombatján 12:00 ÓRAKOR történő biztonsági mentését 60 hónapig őrzi meg a rendszer.
    * A március 12:00-kor múlt Szombatjának biztonsági mentését 10 évig őrzi meg a rendszer.
    
    Miután kiválasztott egy adatmegőrzési szabályzatot, válassza a **tovább**lehetőséget.

1. Válassza ki, hogyan szeretné átvinni a kezdeti biztonsági másolatot az Azure-ba.

    * Az **automatikusan a hálózaton keresztül** lehetőség a biztonsági mentési ütemtervet követi az adatok Azure-ba történő átviteléhez.
    * További információ az **Offline biztonsági mentésről**: [az offline biztonsági mentés áttekintése](offline-backup-overview.md).

    Az átviteli mechanizmus kiválasztása után válassza a **tovább**lehetőséget.

1. Az **Összefoglalás** lapon tekintse át a szabályzat részleteit. Ezután válassza a **csoport létrehozása**lehetőséget. A **Bezárás** gombra kattintva megtekintheti a feladatok előrehaladását a **figyelés** munkaterületen.

    ![A védelmi csoport létrehozásának folyamata](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>SQL Server-adatbázis igény szerinti biztonsági másolatának létrehozása

A rendszer létrehoz egy helyreállítási pontot az első biztonsági mentés alkalmával. Ahelyett, hogy az ütemezett futtatásra várakozik, manuálisan aktiválhatja a helyreállítási pontok létrehozását:

1. Győződjön meg arról, hogy a védelmi csoportban az adatbázis állapota **OK**.

    ![Egy védelmi csoport, amely az adatbázis állapotát mutatja](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kattintson a jobb gombbal az adatbázisra, majd válassza a **helyreállítási pont létrehozása**lehetőséget.

    ![Online helyreállítási pont létrehozásának kiválasztása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. A legördülő menüben válassza az **online védelem**lehetőséget. Ezután kattintson az **OK** gombra egy helyreállítási pont Azure-beli létrehozásának megkezdéséhez.

    ![Helyreállítási pont létrehozásának megkezdése az Azure-ban](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. A feladatok előrehaladását a **figyelés** munkaterületen tekintheti meg.

    ![A feladatok előrehaladásának megtekintése a figyelési konzolon](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból

Védett entitás, például SQL Server adatbázis helyreállítása az Azure-ból:

1. Nyissa meg a DPM-kiszolgáló felügyeleti konzolját. Lépjen a **helyreállítási** munkaterületre, és tekintse meg azokat a kiszolgálókat, amelyekről a DPM biztonsági másolatot készít. Válassza ki az adatbázist (ebben a példában a reportserver $ MSDPM2012). Válasszon ki egy **online**állapotú **helyreállítási időpontot** .

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kattintson a jobb gombbal az adatbázis nevére, és válassza a **helyreállítás**lehetőséget.

    ![Adatbázis helyreállítása az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. A DPM a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább** gombra. Az adatbázis felülírásához válassza a helyreállítás típust a **SQL Server eredeti példányára**. Ezután kattintson a **Tovább** gombra.

    ![Adatbázis helyreállítása az eredeti helyükre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi az adatbázis helyreállítását egy másik SQL Server példányba vagy egy különálló hálózati mappába.
1. A **helyreállítási beállítások megadása** lapon választhatja ki a helyreállítási beállításokat. Kiválaszthatja például a **hálózati sávszélesség használatának szabályozását** a helyreállítás által használt sávszélesség szabályozásához. Ezután kattintson a **Tovább** gombra.
1. Az **Összefoglalás** lapon az aktuális helyreállítási konfiguráció látható. Válassza a **helyreállítás**lehetőséget.

    A helyreállítás állapota a helyreállított adatbázist mutatja. A **Bezárás** gombra kattintva zárhatja be a varázslót, és megtekintheti a folyamat állapotát a **figyelés** munkaterületen.

    ![A helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezésekor a visszaállított adatbázis konzisztens az alkalmazással.

## <a name="next-steps"></a>Következő lépések

További információ: [Azure Backup GYIK](backup-azure-backup-faq.md).
