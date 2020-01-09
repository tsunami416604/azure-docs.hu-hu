---
title: SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépen
description: Ez a cikk azt ismerteti, hogyan lehet visszaállítani az Azure-beli virtuális gépen futó SQL Server-adatbázisokat, és hogy a rendszer biztonsági mentést készít a Azure Backup használatával.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 58525069af28be250c3536db076a38fb350bc1da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390762"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy olyan SQL Server adatbázist, amely egy Azure-beli virtuális gépen (VM) fut, amelyet a [Azure Backup](backup-overview.md) szolgáltatás egy Azure Backup Recovery Services-tárolóba készített.

Ez a cikk a SQL Server adatbázisok visszaállítását ismerteti. További információ: [SQL Server adatbázisok biztonsági mentése Azure virtuális gépeken](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Visszaállítás egy időre vagy egy helyreállítási pontra

A Azure Backup az Azure-beli virtuális gépeken futó SQL Server-adatbázisokat a következőképpen állíthatja helyre:

- Állítsa vissza egy adott dátumra vagy időpontra (a másodikra) a tranzakciónaplók biztonsági másolatainak használatával. Azure Backup automatikusan meghatározza a megfelelő teljes különbözeti biztonsági mentést, valamint a visszaállításhoz szükséges naplók láncát, amely a kijelölt idő alapján állítható vissza.
- Egy adott helyreállítási pontra való visszaállításhoz állítson vissza egy adott teljes vagy különbözeti biztonsági másolatot.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis visszaállítása előtt vegye figyelembe a következőket:

- Az adatbázist visszaállíthatja egy SQL Server egy példányára ugyanabban az Azure-régióban.
- A célkiszolgálón regisztrálni kell a forrással megegyező tárolóban.
- Ha egy TDE-titkosított adatbázist szeretne visszaállítani egy másik SQL Serverre, először [vissza kell állítania a tanúsítványt a célkiszolgálóra](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- A "Master" adatbázis visszaállítása előtt indítsa el az SQL Server példányt egy egyfelhasználós módban az indítási lehetőség **– m AzureWorkloadBackup**használatával.
  - A **-m** érték az ügyfél neve.
  - A kapcsolatok csak a megadott ügyfél nevével nyithatók meg.
- Az összes rendszeradatbázis (modell, főkiszolgáló, msdb) esetében állítsa le a SQL Server Agent szolgáltatást a visszaállítás elindítása előtt.
- Zárjunk be minden olyan alkalmazást, amely megkísérelheti a kapcsolódást bármelyik adatbázishoz.
- Ha egy kiszolgálón több példány fut, akkor az összes példánynak működnie kell, különben a kiszolgáló nem jelenik meg a célkiszolgáló listájában, hogy az adatbázist visszaállítsa a (z) rendszerre.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A visszaállításhoz a következő engedélyek szükségesek:

- **Biztonsági mentési operátor** engedélyei azon a tárolón, ahol a visszaállítást végzi.
- **Közreműködő (írási)** hozzáférés a forrásként szolgáló virtuális géphez, amelyről biztonsági mentés készül.
- **Közreműködő (írási)** hozzáférés a célként megadott virtuális géphez:
  - Ha ugyanarra a virtuális gépre állítja vissza, akkor ez a forrás virtuális gép.
  - Ha másik helyre állítja vissza a visszaállítást, ez az új célként megadott virtuális gép.

Visszaállítás a következőképpen:

1. Nyissa meg azt a tárolót, amelyben a SQL Server VM regisztrálva van.
2. A tároló irányítópultján a **használat**alatt válassza a **biztonsági másolati elemek elemet**.
3. A **biztonsági másolati elemek**alatt, a **biztonsági mentési felügyelet típusa**területen válassza az **SQL lehetőséget az Azure virtuális gépen**.

    ![SQL kiválasztása az Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Válassza ki a visszaállítani kívánt adatbázist.

    ![Válassza ki a visszaállítani kívánt adatbázist](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Tekintse át az adatbázis menüt. Információt nyújt az adatbázis biztonsági másolatából, beleértve a következőket:

    - A legrégebbi és a legújabb visszaállítási pontok.
    - A napló biztonsági mentési állapota az elmúlt 24 órában olyan adatbázisok esetén, amelyek teljes és tömegesen naplózott helyreállítási módban vannak, és amelyek a tranzakciós napló biztonsági mentésére vannak konfigurálva.

6. Válassza a **visszaállítás**lehetőséget.

    ![Visszaállítás kiválasztása](./media/backup-azure-sql-database/restore-db.png)

7. A **visszaállítási konfiguráció**területen adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adatértékeket:
   - **Másik hely**: állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrás-adatbázist.
   - **Adatbázis felülírása**: állítsa vissza az adatforrást ugyanarra a SQL Server példányra, mint az eredeti forrást. Ez a beállítás felülírja az eredeti adatbázist.

    > [!IMPORTANT]
    > Ha a kiválasztott adatbázis egy always on rendelkezésre állási csoporthoz tartozik, SQL Server nem teszi lehetővé az adatbázis felülírását. Csak a **másik hely** érhető el.
    >
   - **Visszaállítás fájlként**: adatbázisként való visszaállítás helyett állítsa vissza azokat a biztonsági másolati fájlokat, amelyeket később adatbázisként helyreállíthat bármely olyan gépen, ahol a fájlok SQL Server Management Studio használatával jelennek meg.
     ![visszaállítási konfigurációs menü](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye**területen válassza a **másik hely**lehetőséget.
2. Válassza ki azt a SQL Server nevet és példányt, amelyre vissza kívánja állítani az adatbázist.
3. A **visszaállított adatbázis neve** mezőbe írja be a céladatbázis nevét.
4. Ha van ilyen, válassza **a felülírás lehetőséget, ha már létezik ilyen nevű adatbázis a kiválasztott SQL-példányon**.
5. Kattintson az **OK** gombra.

    ![Értékek megadása a konfiguráció visszaállítása menühöz](./media/backup-azure-sql-database/restore-configuration.png)

6. A **visszaállítási pont kiválasztása lapon**válassza ki, hogy [egy adott időpontra kívánja-e visszaállítani a visszaállítást](#restore-to-a-specific-point-in-time) , vagy [egy adott helyreállítási pontra kíván visszaállítani](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Az időponthoz való visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok biztonsági másolatai esetében érhető el.

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

1. A visszaállítási **konfiguráció** menüben a **visszaállítás helye**alatt válassza az **adatbázis felülírása** > **OK**elemet.

    ![Válassza az adatbázis felülírása lehetőséget](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. A **visszaállítási pont kiválasztása lapon**válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** egy [adott helyreállítási pontra](#restore-to-a-specific-restore-point)való visszaállításhoz.

    > [!NOTE]
    > Az időponthoz való visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok biztonsági másolatai esetében érhető el.

### <a name="restore-as-files"></a>Visszaállítás fájlként

Ha egy adatbázis helyett. bak fájlként szeretné visszaállítani a biztonsági mentési adatmennyiséget, válassza a **visszaállítás fájlként**lehetőséget. Ha a fájlok egy megadott elérési útra lettek kiadva, ezeket a fájlokat bármely olyan gépre elvégezheti, ahol adatbázisként szeretné visszaállítani őket. Azzal, hogy a fájlokat bármilyen gépen át tudja helyezni, most már visszaállíthatja az adatátvitelt és a régiókat.

1. A visszaállítási **konfiguráció** menüben, a **visszaállítás helye**területen válassza a **visszaállítás fájlként**lehetőséget.
2. Válassza ki azt a SQL Server nevet, amelyre vissza kívánja állítani a biztonságimásolat-fájlokat.
3. A **kiszolgáló célhelyének elérési útja** a 2. lépésben kiválasztott kiszolgálón adja meg a mappa elérési útját. Ez az a hely, ahol a szolgáltatás kiírja az összes szükséges biztonságimásolat-fájlt. A célként megadott elérési úttal megegyező hálózati megosztási elérési út vagy egy csatlakoztatott Azure-fájlmegosztás elérési útja lehetővé teszi, hogy a fájlok könnyebben hozzáférhessenek az azonos hálózatban lévő más gépekhez, illetve az azokhoz csatlakoztatott Azure-fájlmegosztáshoz.<BR>

>Ha vissza szeretné állítani az adatbázis biztonsági másolatának fájljait egy olyan Azure-fájlmegosztás számára, amely a cél regisztrált virtuális gépen van csatlakoztatva, győződjön meg arról, hogy az NT AUTHORITY\SYSTEM hozzáfér a fájlmegosztás eléréséhez. Az alábbi lépések végrehajtásával engedélyezheti az olvasási/írási engedélyeket a virtuális gépen csatlakoztatott AFS-hez:
>- `PsExec -s cmd` futtatása az NT AUTHORITY\SYSTEM-rendszerhéjba való belépéshez
>   - Hajtsa végre az `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` parancsot.
>   - Hozzáférés ellenőrzése `dir \\<storageacct>.file.core.windows.net\<filesharename>`
>- A Backup-tárolóban lévő fájlok visszaállításának elindításával `\\<storageacct>.file.core.windows.net\<filesharename>` elérési útra<BR>
A PsExec a <https://docs.microsoft.com/sysinternals/downloads/psexec> használatával tölthető le

4. Kattintson az **OK** gombra.

![Válassza a visszaállítás fájlokként lehetőséget.](./media/backup-azure-sql-database/restore-as-files.png)

5. Válassza ki azt a **visszaállítási pontot** , amelyik a rendelkezésre álló. bak összes fájlt vissza fogja állítani.

![Válasszon egy visszaállítási pontot](./media/backup-azure-sql-database/restore-point.png)

6. A rendszer a kijelölt helyreállítási ponthoz társított összes biztonságimásolat-fájlt a célhely elérési útjára írja. A fájlokat bármely olyan gépen visszaállíthatja adatbázisként, amelyen a SQL Server Management Studio használatával van jelen.

![Visszaállított biztonságimásolat-fájlok a célhely elérési útján](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás adott időpontra

Ha a **naplókat (időpontot)** a visszaállítási típusként választotta, tegye a következőket:

1. A **dátum és idő visszaállítása**alatt nyissa meg a naptárat. A naptárban a helyreállítási pontokat tartalmazó dátumok félkövér típusban jelennek meg, az aktuális dátum pedig ki van emelve.
1. Válassza ki a helyreállítási pontokat tartalmazó dátumot. Nem választhat olyan dátumokat, amelyek nem rendelkeznek helyreállítási ponttal.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Miután kiválasztott egy dátumot, az idővonal-gráf megjeleníti a rendelkezésre álló helyreállítási pontokat a folytonos tartományban.
1. Adja meg a helyreállítás időpontját az idősor-diagramon, vagy válasszon ki egy időpontot. Ezután kattintson az **OK** gombra.

    ![Visszaállítási idő kiválasztása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Ha a visszaállítás után meg szeretné őrizni az adatbázis működését, a **Speciális konfiguráció** menüben engedélyezze a **visszaállítást a derecovery**művelettel.
1. Ha módosítani szeretné a célkiszolgáló visszaállítási helyét, adjon meg egy új cél elérési utat.
1. Kattintson az **OK** gombra.

    ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.
1. A visszaállítási folyamat nyomon követése az **értesítések** területen, vagy nyomon követheti a **feladatok visszaállítása** az adatbázis menüjében.

    ![Visszaállítási feladatok állapota](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Visszaállítás adott visszaállítási pontra

Ha a **teljes & különbözetet** választotta a visszaállítási típusként, tegye a következőket:

1. Válasszon ki egy helyreállítási pontot a listából, és a visszaállítási pont eljárás befejezéséhez kattintson **az OK gombra** .

    ![Teljes helyreállítási pont kiválasztása](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Ha a visszaállítás után meg szeretné őrizni az adatbázis működését, a **Speciális konfiguráció** menüben engedélyezze a **visszaállítást a derecovery**művelettel.
1. Ha módosítani szeretné a célkiszolgáló visszaállítási helyét, adjon meg egy új cél elérési utat.
1. Kattintson az **OK** gombra.

    ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.
1. A visszaállítási folyamat nyomon követése az **értesítések** területen, vagy nyomon követheti a **feladatok visszaállítása** az adatbázis menüjében.

    ![Visszaállítási feladatok állapota](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Adatbázisok visszaállítása nagy számú fájllal

Ha egy adatbázisban lévő fájlok teljes mérete meghaladja az [adott korlátot](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), akkor Azure Backup egy másik pit-összetevőben tárolja az adatbázisfájlok listáját, hogy a visszaállítási művelet során nem fogja tudni beállítani a cél-visszaállítási útvonalat. Ehelyett a rendszer visszaállítja a fájlokat az alapértelmezett SQL-elérési útra.

  ![Adatbázis visszaállítása nagyméretű fájllal](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Következő lépések

[Kezelés és figyelés](manage-monitor-sql-database-backup.md) SQL Server a Azure Backup által biztonsági mentést tartalmazó adatbázisokat.
