---
title: SQL Server biztonsági mentése az Azure-ba DPM számítási feladatokkal
description: Bevezetés SQL Server adatbázisok biztonsági mentésére az Azure Backup szolgáltatás használatával
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b5709eb845d07e3638e0c100c857e5538e293317
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173267"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>SQL Server biztonsági mentése az Azure-ba DPM számítási feladatokkal

Ez a cikk végigvezeti a SQL Server adatbázisok Azure Backup használatával történő biztonsági mentésének konfigurációs lépésein.

SQL Server adatbázisok Azure-ba történő biztonsági mentéséhez Azure-fiókra van szükség. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy ingyenes próbaverziós fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

Az Azure-ba való SQL Server adatbázis biztonsági mentésének és az Azure-ba történő helyreállításnak a kezelése három lépést tesz szükségessé:

1. Hozzon létre egy biztonsági mentési szabályzatot SQL Server adatbázisok védelméhez az Azure-ban.
2. Hozzon létre igény szerinti biztonsági másolatokat az Azure-ba.
3. Az adatbázis helyreállítása az Azure-ból.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, győződjön meg arról, hogy teljesülnek az Microsoft Azure Backup használatának minden [előfeltétele](backup-azure-dpm-introduction.md#prerequisites-and-limitations) a munkaterhelések megóvása érdekében. Az előfeltételek olyan feladatokat foglalnak magukban, mint például a Backup-tároló létrehozása, a tár hitelesítő adatainak letöltése, a Azure Backup ügynök telepítése és a kiszolgáló regisztrálása a tárolóban.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Biztonsági mentési szabályzat létrehozása SQL Server adatbázisok védelméhez az Azure-ban

1. A DPM-kiszolgálón kattintson a **védelem** munkaterületre.
2. Az eszközsávon kattintson az **új** elemre egy új védelmi csoport létrehozásához.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)
3. A DPM a Start képernyőt jeleníti meg a **védelmi csoport**létrehozásával kapcsolatos útmutatással. Kattintson a **Tovább**gombra.
4. Válassza a **kiszolgálók**lehetőséget.

    ![Védelmi csoport típusának kiválasztása – "kiszolgálók"](./media/backup-azure-backup-sql/pg-servers.png)
5. Bontsa ki azt a SQL Server gépet, ahol a biztonsági mentéshez szükséges adatbázisok jelen vannak. A DPM különböző adatforrásokat jelenít meg, amelyekről biztonsági másolatot készíthet az adott kiszolgálóról. Bontsa ki az **összes SQL-megosztást** , és válassza ki az adatbázisokat (ebben az esetben a reportserver $ MSDPM2012 és a reportserver $ MSDPM2012TempDB) biztonsági mentését. Kattintson a **Tovább**gombra.

    ![SQL-adatbázis kiválasztása](./media/backup-azure-backup-sql/pg-databases.png)
6. Adja meg a védelmi csoport nevét, majd válassza az **online védelem szeretnék** jelölőnégyzetet.

    ![Adatvédelmi módszer – rövid távú lemez & online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. A **rövid távú célok megadása képernyőn adja meg** a szükséges bemeneteket a lemezre történő biztonsági mentési pontok létrehozásához.

    Itt láthatjuk, hogy az **adatmegőrzési tartomány** értéke *5 nap*, a **szinkronizálás gyakorisága** pedig *15 percenként*van beállítva, ami a biztonsági mentés gyakorisága. Az **expressz teljes biztonsági mentés** *8:00 P. M*értékre van állítva.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 8:00 ÓRAKOR (a képernyő bemenetének megfelelően) a rendszer naponta létrehoz egy biztonsági mentési pontot az előző nap 8:00 PM biztonsági mentési pontján módosított adatok továbbításával. Ezt a folyamatot **expressz teljes biztonsági mentésnek**nevezzük. Míg a tranzakciós naplók 15 percenként szinkronizálhatók, ha az adatbázist 9:00 ÓRAKOR kell helyreállítani – ezt követően a rendszer a naplókat az utolsó expressz teljes biztonsági mentési pontról (ebben az esetben 08:00) hozza létre.
   >
   >

8. Kattintson a **Tovább** gombra

    A DPM megjeleníti a rendelkezésre álló teljes tárterületet és a lehetséges lemezterület-kihasználtságot.

    ![Lemez kiosztása](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint a DPM egy kötetet hoz létre adatforrásként (SQL Server adatbázis), amelyet a kezdeti biztonsági másolathoz használ a rendszer. Ennek a módszernek a használatával a logikai lemezkezelő (LDM) a 300 adatforrásokra (SQL Server adatbázisokra) korlátozza a DPM védelmét. Ennek a korlátozásnak a megkerüléséhez válassza az **DPM Storage-készletben a közös elhelyezésű tárolók**lehetőséget. Ha ezt a beállítást használja, a DPM egyetlen kötetet használ több adatforráshoz, ami lehetővé teszi, hogy a DPM akár 2000 SQL-adatbázist is véd.

    Ha a kötetek méretének **automatikus** növelése lehetőség van kiválasztva, a DPM képes a megnövekedett biztonsági mentési kötetre, mert az éles adatmennyiség növekszik. Ha a kötetek méretének **automatikus** növelése lehetőség nincs bejelölve, a DPM korlátozza a védelmi csoport adatforrásaihoz használt biztonságimásolat-tárolót.
9. A rendszergazdák megválaszthatják, hogy a kezdeti biztonsági mentést manuálisan (hálózatról kikapcsolva) továbbítsák a sávszélesség zsúfoltságának vagy a hálózatnak a elkerüléséhez. Azt is megadhatják, hogy a kezdeti átvitel mikor történjen. Kattintson a **Tovább**gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolathoz a teljes adatforrást (SQL Server adatbázist) át kell vinni az üzemi kiszolgálóról (SQL Server gépről) a DPM-kiszolgálóra. Ezek az adatmennyiségek nagy méretűek lehetnek, és a hálózaton keresztüli adatátvitel meghaladhatja a sávszélességet. Emiatt a rendszergazdák dönthetnek úgy, hogy a kezdeti biztonsági mentést **manuálisan** (cserélhető adathordozó használatával) továbbítják a sávszélesség zsúfoltságának elkerüléséhez, vagy **automatikusan a hálózaton keresztül** (adott időpontban).

    A kezdeti biztonsági mentés befejezését követően a biztonsági mentések a kezdeti biztonsági másolaton lévő növekményes biztonsági másolatok. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.
10. Válassza ki, hogy mikor szeretné futtatni a konzisztencia-ellenőrzést, és kattintson a **tovább**gombra.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    A DPM konzisztencia-ellenőrzést végezhet a biztonsági mentési pont integritásának ellenőrzéséhez. Kiszámítja a biztonságimásolat-fájl ellenőrzőösszegét az üzemi kiszolgálón (SQL Server gép ebben a forgatókönyvben) és a fájl biztonsági másolatait a következő helyen: DPM. Ütközés esetén feltételezhető, hogy a DPM-on található biztonsági másolati fájl sérült. A DPM az ellenőrzőösszeg-eltérésnek megfelelő blokkok elküldésével helyesbíti a biztonsági másolatban szereplő adatokat. Mivel a konzisztencia-ellenőrzés teljesítmény-igényes művelet, a rendszergazdák lehetősége van a konzisztencia-ellenőrzés ütemezésére vagy automatikus futtatására.
11. Az adatforrások online védelmének megadásához válassza ki az Azure-ba védeni kívánt adatbázisokat, és kattintson a **tovább**gombra.

    ![Adatforrások kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. A rendszergazdák a szervezeti szabályzatoknak megfelelő biztonsági mentési ütemterveket és adatmegőrzési szabályzatokat is választhatnak.

    ![Ütemterv és megőrzés](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer, 12:00 ÓRAKOR és 20:00 órakor (a képernyő alsó részén) jelennek meg.

    > [!NOTE]
    > Jó megoldás, ha a gyors helyreállítás érdekében néhány rövid távú helyreállítási pontot kell használnia a lemezen. Ezek a helyreállítási pontok az "Operational Recovery" szolgáltatáshoz használatosak. Az Azure jó helyen található, magasabb SLA-kat és garantált rendelkezésre állást kínál.
    >
    >

    **Ajánlott eljárás**: gondoskodjon arról, hogy az Azure biztonsági mentések a helyi lemezes biztonsági mentések befejezése után a DPM használatával legyenek ütemezve. Ez lehetővé teszi a lemez legújabb biztonsági mentésének másolását az Azure-ba.

13. Válassza ki az adatmegőrzési szabály ütemtervét. Az adatmegőrzési szabályzat működésének részletes leírását a [használati Azure Backup a szalagos infrastruktúra lecserélése című cikkben](backup-azure-backup-cloud-as-tape.md)találja.

    ![Adatmegőrzési szabályzat](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentések naponta egyszer, 12:00 és 8 ÓRAKOR (a képernyő alsó részén) jelennek meg, és 180 napig őrződnek meg.
    * A biztonsági mentés szombaton, 12:00 órakor 104 hétig megőrzött
    * A biztonsági mentés az elmúlt szombaton 12:00 órakor 60 hónapig megőrzött
    * A biztonsági mentés március 12:00 órakor, az utolsó szombaton 10 évig őrzi meg
14. Kattintson a **tovább** gombra, és válassza ki a megfelelő lehetőséget a kezdeti biztonsági másolat Azure-ba történő átviteléhez. Automatikusan kiválaszthatja **a hálózaton** vagy az **Offline biztonsági mentést**.

    * A **hálózat automatikusan** továbbítja a biztonsági mentési adatmennyiséget az Azure-ba a biztonsági mentéshez kiválasztott ütemterv szerint.
    * Az **Offline biztonsági mentés** működését a [Azure Backup offline biztonsági mentési munkafolyamata](backup-azure-backup-import-export.md)ismerteti.

    Válassza ki a megfelelő adatátviteli mechanizmust a kezdeti biztonsági másolat Azure-ba való elküldéséhez, és kattintson a **tovább**gombra.
15. Ha áttekinti a szabályzat részleteit az **Összefoglalás** képernyőn, kattintson a **csoport létrehozása** gombra a munkafolyamat befejezéséhez. Kattintson a **Bezárás** gombra, és figyelje a feladatok előrehaladását a figyelés munkaterületen.

    ![A védelmi csoport létrehozása folyamatban van](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server-adatbázis igény szerinti biztonsági mentése

Míg az előző lépések létrehozott egy biztonsági mentési szabályzatot, a rendszer csak az első biztonsági mentés alkalmával hozza létre a helyreállítási pontot. Ahelyett, hogy az ütemező beindítására vár, az alábbi lépések a helyreállítási pontok manuális létrehozását indítja el.

1. Várjon, amíg a védelmi csoport állapota a helyreállítási pont létrehozása előtt a-adatbázishoz tartozó **OK** értékre mutat.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázisra, és válassza a **helyreállítási pont létrehozása**lehetőséget.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. A legördülő menüben válassza az **online védelem** lehetőséget, majd kattintson **az OK**gombra. Ezzel elindítja egy helyreállítási pont létrehozását az Azure-ban.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. A feladatok előrehaladását a **figyelés** munkaterületen tekintheti meg, ahol a következő ábrán láthatóhoz hasonló folyamatban lévő feladatok is megtalálhatók.

    ![Figyelési konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból

A következő lépések szükségesek egy védett entitás (SQL Server adatbázis) Azure-ból történő helyreállításához.

1. Nyissa meg a DPM-kiszolgáló felügyeleti konzolját. Navigáljon a **helyreállítási** munkaterületre, ahol megtekintheti a DPM által biztonsági mentés alatt lévő kiszolgálókat. Tallózással keresse meg a szükséges adatbázist (ebben az esetben a reportserver $ MSDPM2012). Válassza ki a **helyreállítást az** **online**állapottal végződő időpontból.

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevére, és kattintson a **helyreállítás**elemre.

    ![Helyreállítás az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A DPM a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább**gombra. Az adatbázis felülírásához válassza a helyreállítás típust a **SQL Server eredeti példányára**. Kattintson a **Tovább**gombra.

    ![Visszaállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi az adatbázis helyreállítását egy másik SQL Server példányba vagy egy különálló hálózati mappába.
4. A **helyreállítási beállítások megadása** képernyőn kiválaszthatja a helyreállítási beállításokat, például a hálózati sávszélesség használatának szabályozását a helyreállítás által használt sávszélesség szabályozásához. Kattintson a **Tovább**gombra.
5. Az **Összefoglalás** képernyőn az eddig megadott összes helyreállítási konfiguráció látható. Kattintson a **helyreállítás**gombra.

    A helyreállítás állapota a helyreállított adatbázist mutatja. A **Bezárás** gombra kattintva zárhatja be a varázslót, és megtekintheti a folyamatot a **figyelés** munkaterületen.

    ![Helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejeződése után a visszaállított adatbázis az alkalmazás konzisztens.

## <a name="next-steps"></a>Következő lépések

* [Azure Backup GYIK](backup-azure-backup-faq.md)
