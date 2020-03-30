---
title: SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépen
description: Ez a cikk ismerteti, hogyan állíthatja vissza az Azure-beli virtuális gépen futó ÉS az Azure Backup által biztonsági mentésre készült SQL Server-adatbázisokat.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252453"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>SQL Server-adatbázisok visszaállítása az Azure virtuális gépein

Ez a cikk ismerteti, hogyan állíthatja vissza az SQL Server-adatbázist, amely egy Azure virtuális gépen (VM) fut, amelyről az [Azure Backup](backup-overview.md) szolgáltatás biztonsági másolatot készített egy Azure Backup Recovery Services-tárolóba.

Ez a cikk az SQL Server-adatbázisok visszaállítását ismerteti. További információt az [SQL Server-adatbázisok biztonsági mentése az Azure virtuális gépeken című témakörben talál.](backup-azure-sql-database.md)

## <a name="restore-to-a-time-or-a-recovery-point"></a>Visszaállítás időpontra vagy helyreállítási pontra

Az Azure Backup az alábbiak szerint állíthatja vissza az Azure virtuális gépeken futó SQL Server-adatbázisokat:

- Visszaállítás egy adott dátumra vagy időpontra (a másodikra) a tranzakciónapló biztonsági másolatainak használatával. Az Azure Backup automatikusan meghatározza a megfelelő teljes különbözeti biztonsági mentést és a naplóbiztonsági mentések láncolatát, amelyek a kiválasztott idő alapján visszaállíthatók.
- Adott teljes vagy különbözeti biztonsági mentés visszaállítása egy adott helyreállítási pontra való visszaállításhoz.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis visszaállítása előtt vegye figyelembe az alábbiakat:

- Az adatbázis visszaállíthatja egy SQL Server egy példányát ugyanabban az Azure-régióban.
- A célkiszolgálót ugyanahhoz a tárolóhoz kell regisztrálni, mint a forrást.
- Ha egy TDE-titkosítású adatbázist vissza szeretne állítani egy másik SQL Server kiszolgálóra, először vissza kell [állítania a tanúsítványt a célkiszolgálóra](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- A "fő" adatbázis visszaállítása előtt indítsa el az SQL Server-példányt egyfelhasználós módban a **-m AzureWorkloadBackup**indítási lehetőség használatával.
  - Az **-m** értéke az ügyfél neve.
  - Csak a megadott ügyfélnév nyithatja meg a kapcsolatot.
- Az összes rendszeradatbázis (modell, főkiszolgáló, msdb) esetén állítsa le az SQL Server Agent szolgáltatást a visszaállítás aktiválása előtt.
- Zárjon be minden olyan alkalmazást, amely megpróbál kapcsolatot létesíteni ezen adatbázisok bármelyikével.
- Ha több példány fut egy kiszolgálón, az összes példánynak futnia kell, különben a kiszolgáló nem jelenik meg a célkiszolgálók listájában, hogy visszaállítsa az adatbázist.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A visszaállításhoz a következő engedélyekre van szükség:

- **Biztonságimásolat-felelős** engedélyei abban a tárolóban, ahol a visszaállítást végzi.
- **Közreműködő (írás)** a forrás virtuális gép, amely biztonsági másolatot kötött.
- **Közreműködői (írási)** hozzáférés a cél virtuális géphez:
  - Ha ugyanarra a virtuális gépre állítja vissza, ez a forrás virtuális gép.
  - Ha egy másik helyre állítja vissza, ez az új cél virtuális gép.

Visszaállítás az alábbiak szerint:

1. Nyissa meg azt a tárolót, amelyben az SQL Server virtuális gép regisztrálva van.
2. A tároló irányítópultján a **Használat**csoportban válassza a **Biztonsági másolat elemek lehetőséget.**
3. A **Backup Items (Biztonságimásolat-készítő elemek)** **csoportban**válassza az SQL elemet **az Azure VM alkalmazásban.**

    ![Sql kiválasztása az Azure Virtuális gépben](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Jelölje ki a visszaállítani kívánt adatbázist.

    ![Jelölje ki a visszaállítani kívánt adatbázist](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Tekintse át az adatbázis menüjét. Tájékoztatást nyújt az adatbázis biztonsági mentéséről, többek között a következőkről:

    - A legrégebbi és legújabb visszaállítási pontok.
    - A napló biztonsági mentési állapota az elmúlt 24 órában a teljes és tömegesen naplózott helyreállítási módban lévő, tranzakciós naplóbiztonsági mentések konfigurált adatbázisok esetében.

6. Válassza **a Visszaállítás**lehetőséget.

    ![Visszaállítás kiválasztása](./media/backup-azure-sql-database/restore-db.png)

7. A **Konfiguráció visszaállítása területen**adja meg, hogy hol (vagy hogyan) állítsa vissza az adatokat:
   - **Alternatív hely:** Állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrásadatbázist.
   - **Adatbázis felülírása**: Állítsa vissza az adatokat ugyanarra az SQL Server-példányra, mint az eredeti forrást. Ez a beállítás felülírja az eredeti adatbázist.

    > [!IMPORTANT]
    > Ha a kijelölt adatbázis egy Mindig bekapcsolva állási csoporthoz tartozik, az SQL Server nem engedélyezi az adatbázis felülírását. Csak **alternatív hely** érhető el.
    >
   - **Visszaállítás fájlként:** Ahelyett, hogy adatbázisként restaurálna, állítsa vissza azokat a biztonsági másolatfájlokat, amelyek később adatbázisként helyreállíthatók bármely olyan számítógépen, ahol a fájlok jelen vannak az SQL Server Management Studio használatával.
     ![Konfiguráció visszaállítása menü](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. A **Konfiguráció visszaállítása** menü Hol állítsa vissza a **Helyét,** válassza az **Alternatív hely lehetőséget.**
2. Jelölje ki azt az SQL Server nevet és példányt, amelyre vissza szeretné állítani az adatbázist.
3. A **Visszaállított adatbázisnév** mezőbe írja be a céladatbázis nevét.
4. Ha van ilyen, válassza a Felülírás lehetőséget, **ha az azonos nevű adatbázis már létezik a kijelölt SQL-példányon.**
5. Válassza **az OK gombot.**

    ![Értékek biztosítása a Konfiguráció visszaállítása menühöz](./media/backup-azure-sql-database/restore-configuration.png)

6. A **Visszaállítási pont kiválasztása**csoportban adja meg, hogy egy adott időpontra vagy egy adott helyreállítási pontra szeretne-e [visszaállítani](#restore-to-a-specific-point-in-time) . [restore to a specific recovery point](#restore-to-a-specific-restore-point)

    > [!NOTE]
    > A pont-az-time visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok naplóbiztonsági mentései esetén érhető el.

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

1. A **Konfiguráció visszaállítása** menü **Hol állítsa vissza,** válassza a Adatbázis írása AZ OK **felülírása** > **OK**lehetőséget.

    ![Adatbázis felülírásának kijelölése](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. A **Visszaállítási pont kiválasztása**csoportban válassza a Naplók **(időpontban)** lehetőséget [egy adott időpontra való visszaállításhoz.](#restore-to-a-specific-point-in-time) Vagy válassza **a Teljes & különbözeti lehetőséget** egy adott helyreállítási pont [visszaállításához.](#restore-to-a-specific-restore-point)

    > [!NOTE]
    > A pont-az-time visszaállítás csak a teljes és tömegesen naplózott helyreállítási módban lévő adatbázisok naplóbiztonsági mentései esetén érhető el.

### <a name="restore-as-files"></a>Visszaállítás fájlként

Ha a biztonsági másolat adatait adatbázis helyett .bak fájlként szeretné visszaállítani, válassza a **Visszaállítás fájlként**lehetőséget. Miután a fájlok egy megadott elérési útra kerültek, ezeket a fájlokat bármely olyan számítógépre viheti, ahol adatbázisként vissza szeretné állítani őket. Annak az alapján, hogy ezeket a fájlokat bármely számítógépre áthelyezheti, most már visszaállíthatja az adatokat az előfizetések és régiók között.

1. A **Konfiguráció visszaállítása** menü **Hol szeretné visszaállítani,** válassza **a Visszaállítás fájlként**lehetőséget.
2. Jelölje ki azt az SQL Server nevet, amelyre vissza szeretné állítani a biztonsági másolat fájljait.
3. A **kiszolgáló célelérési útjának** beírása a mappa elérési útját a 2. Ez az a hely, ahol a szolgáltatás kidobja az összes szükséges biztonsági mentési fájlt. Általában egy hálózati megosztási útvonal, vagy egy csatlakoztatott Azure-fájlmegosztás elérési útja, ha meg van adva a célelérési út, megkönnyíti a hozzáférést ezekhez a fájlokhoz más gépek ugyanabban a hálózatban, vagy az azonos Azure-fájlmegosztás csatlakoztatva őket.<BR>

    >Az adatbázis biztonsági mentési fájljainak visszaállításához a cél regisztrált virtuális gépre csatlakoztatott Azure-fájlmegosztáson, győződjön meg arról, hogy az NT AUTHORITY\SYSTEM hozzáfér a fájlmegosztáshoz. Az alábbi lépéseket hajthatja végre, hogy a virtuális gépre csatlakoztatott AFS-hez olvasási/írási engedélyeket adjon:
    >
    >- Futtatás `PsExec -s cmd` az NT AUTHORITY\SYSTEM rendszerhéjba való belépéshez
    >   - Hajtsa végre az `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` parancsot.
    >   - Hozzáférés ellenőrzése a`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- A visszaállítás megnyitása fájlként a `\\<storageacct>.file.core.windows.net\<filesharename>` Biztonsági másolat tárolójából elérési útként<BR>
    A Psexec letölthető a<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Válassza **az OK gombot.**

    ![Visszaállítás fájlként jelölése](./media/backup-azure-sql-database/restore-as-files.png)

5. Jelölje ki azt a **visszaállítási pontot,** amelyhez az összes rendelkezésre álló .bak fájl visszalesz állítva.

    ![Visszaállítási pont kijelölése](./media/backup-azure-sql-database/restore-point.png)

6. A kijelölt helyreállítási ponthoz társított összes biztonsági másolat a célelérési útba kerül. A fájlokat adatbázisként állíthatja vissza az SQL Server Management Studio használatával található bármely számítógépen.

    ![Visszaállított biztonságimásolat-fájlok a célelérési úton](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás egy adott időpontra

Ha **a Naplók (időpontban)** lehetőséget választotta visszaállítási típusként, tegye a következőket:

1. A **Visszaállítás dátuma/időpontja csoportban**nyissa meg a naptárat. A naptárban a helyreállítási pontokkal rendelkező dátumok félkövér betűkkel jelennek meg, és az aktuális dátum ki van emelve.
1. Válassza ki a helyreállítási pontokkal rendelkező dátumot. Nem választhat olyan dátumokat, amelyeknek nincshelyreállítási pontja.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Miután kiválasztott egy dátumot, az idővonal-diagram a rendelkezésre álló helyreállítási pontokat folyamatosan jeleníti meg.
1. Adja meg a helyreállítás idejét az idővonal-grafikonon, vagy adjon meg egy időpontot. Ezután kattintson az **OK** gombra.

    ![Visszaállítási idő kiválasztása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Ha a **speciális konfiguráció menüben** azt szeretné, hogy az adatbázis a visszaállítás után ne üzemképes maradjon, engedélyezze a **visszaállítást a NORECOVERY funkcióval.**
1. Ha módosítani szeretné a visszaállítás helyét a célkiszolgálón, adjon meg egy új célelérési utat.
1. Válassza **az OK gombot.**

    ![Speciális konfigurációs menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. A **Visszaállítás** menü **visszaállítása parancsára** kattintson a visszaállítási feladat elindításához.
1. Az **Értesítések** területen nyomon követheti a visszaállítási folyamatot, vagy nyomon követheti az adatbázis menü **Feladatok visszaállítása** parancsát.

    ![A feladat előrehaladásának visszaállítása](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Visszaállítás egy adott visszaállítási pontra

Ha visszaállítási típusként **a Teljes & különbözetet** választotta, tegye a következőket:

1. Jelöljön ki egy helyreállítási pontot a listából, és a visszaállítási pont végrehajtásához **kattintson** az OK gombra.

    ![Teljes helyreállítási pont kiválasztása](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Alapértelmezés szerint az elmúlt 30 nap helyreállítási pontjai jelennek meg. A 30 napnál régebbi helyreállítási pontokat a **Szűrő** gombra kattintva és egy egyéni tartomány kiválasztásával jelenítheti meg.

1. Ha a **speciális konfiguráció menüben** azt szeretné, hogy az adatbázis a visszaállítás után ne üzemképes maradjon, engedélyezze a **visszaállítást a NORECOVERY funkcióval.**
1. Ha módosítani szeretné a visszaállítás helyét a célkiszolgálón, adjon meg egy új célelérési utat.
1. Válassza **az OK gombot.**

    ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. A **Visszaállítás** menü **visszaállítása parancsára** kattintson a visszaállítási feladat elindításához.
1. Az **Értesítések** területen nyomon követheti a visszaállítási folyamatot, vagy nyomon követheti az adatbázis menü **Feladatok visszaállítása** parancsát.

    ![A feladat előrehaladásának visszaállítása](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Nagy számú fájlt tartalmazó adatbázisok visszaállítása

Ha az adatbázisban lévő fájlok teljes karakterláncmérete nagyobb, mint egy [adott korlát,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)az Azure Backup az adatbázisfájlok listáját egy másik gödörösszetevőben tárolja, így a visszaállítási művelet során nem tudja beállítani a cél-visszaállítási útvonalat. A fájlok visszaállnak az SQL alapértelmezett elérési útjára.

  ![Adatbázis visszaállítása nagyfájllal](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>További lépések

[Kezelés és figyelés](manage-monitor-sql-database-backup.md) Az Azure Backup által biztonsági másolatban található SQL Server-adatbázisok.
