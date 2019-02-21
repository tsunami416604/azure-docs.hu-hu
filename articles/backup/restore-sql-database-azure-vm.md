---
title: Visszaállítás biztonsági másolat az SQL Server-adatbázisok az Azure Backup-beli virtuális gépen |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan készül biztonsági másolat az Azure Backup-beli virtuális gépen futó SQL Server adatbázisok visszaállítása
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445440"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Az Azure virtuális gépeken futó SQL Server-adatbázisok visszaállítása 


Ez a cikk ismerteti, hogy a rendszer biztonsági másolatot a egy Azure Backup helyreállítási tárat az Azure virtuális Gépeken futó SQL Server-adatbázis visszaállítása a [Azure Backup](backup-overview.md) szolgáltatás.


> [!NOTE]
> Az Azure Backup-beli virtuális gépen futó SQl Server-adatbázisok biztonsági mentése jelenleg nyilvános előzetes verzióban érhető el.


Ez a cikk ismerteti, hogyan állíthatja vissza az SQL Server-adatbázisok. Ha még nem konfigurálta az adatbázisok biztonsági mentése, kövesse a [Ez a cikk](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>Adatbázisok helyreállítása

Az Azure Backup a következő Azure virtuális gépeken futó SQL Server-adatbázisok is visszaállítása:

- Állítsa vissza az adott dátum és idő (így a második) a tranzakciónapló biztonsági mentései használatával. Az Azure Backup automatikusan meghatározza, hogy a megfelelő teljes különbségi biztonsági másolat, és a kijelölt idő alapján a lánc naplók biztonsági másolatainak, visszaállításához szükséges.
- Egy adott teljes vagy különbözeti biztonsági másolat visszaállítása állíthatja vissza egy adott helyreállítási pontot, nem pedig egy adott időpontban.


### <a name="prerequisites"></a>Előfeltételek

Adatbázis visszaállítása előtt vegye figyelembe a következőket:

- Az adatbázist visszaállíthatja egy példányát olyan SQL Server ugyanazon Azure-régióban.
- A célkiszolgáló ugyanazzal a tárral, mint a forrás regisztrálni kell.
- A TDE titkosított adatbázis visszaállítása egy másik SQL Serverre, először szüksége [állítsa vissza a tanúsítványt a célkiszolgálóra](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Mielőtt a "fő" adatbázis visszaállítási indít el, indítsa el az SQL Server-példány egyfelhasználós módban indítási lehetőséget **-m AzureWorkloadBackup**.
    - Az érték **-m** az ügyfél neve.
    - Csak a megadott ügyfél nevet is megadhat a kapcsolat megnyitásához.
- Az összes rendszer adatbázisban (modell master, msdb) állítsa le az SQL Agent szolgáltatást a visszaállítás elindítása előtt.
- Zárja be az olyan alkalmazásokat, amelyek próbálnak kapcsolatot sem ezeknek az adatbázisoknak ellopni.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

Visszaállítása a következő engedélyek szükségesek:

* **Biztonsági mentési operátor** engedélyeket a tároló, milyen a visszaállítás során.
* **Közreműködő (írás)** a forrás virtuális gép biztonsági mentésének elérésére.
* **Közreműködő (írás)** a cél virtuális Gépen való hozzáférést:
    - Ha Ön helyreállítása ugyanazon a virtuális Gépen, ez lesz a forrás virtuális Gépen.
    - Ha Ön visszaállítása másik helyre ez lesz az új cél virtuális gép. 

Állítsa vissza a következőképpen:
1. Nyissa meg a tároló, amelyben az SQL Server rendszerű virtuális gép regisztrálva lett.
2. A tároló irányítópultjának alatt **használati**válassza **biztonsági másolati elemek**.

    ![A biztonsági másolati elemek menü megnyitásához](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. A **biztonsági másolati elemek**alatt **biztonságimásolat-felügyeleti típussal**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Válassza ki az adatbázis visszaállításához.

    ![Válassza ki a visszaállítandó adatbázis](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Tekintse át az adatbázis menü. Ismerteti az adatbázis biztonsági másolata, többek között: 

    * A legrégebbi és a legújabb visszaállítási pontot.
    * Keresse meg az elmúlt 24 órában, az adatbázisok teljes és a tömegesen naplózott helyreállítási módban, biztonsági mentés állapota, ha a tranzakciós napló biztonsági mentés konfigurálva.

6. Kattintson a **Restore DB**. 

    ![Válassza ki a Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

7. A **konfiguráció visszaállítása**, adja meg, ahol szeretné visszaállítani az adatokat:
    - **Másik helyre**: Állítsa vissza az adatbázist egy másik helyre, és az eredeti adatbázis megőrzése.
    - **Adatbázis felülírása**: Állítsa vissza az adatokat az eredeti forrás, ugyanazon SQL Server-példányon. Ez a beállítás hatását, hogy az eredeti adatbázis felülírásához.

    > [!Important]
    > Ha a kijelölt adatbázis mindig a rendelkezésre állási csoporthoz tartozik, az SQL Server felülírja az adatbázis nem teszi lehetővé. Csak **máshová** érhető el.
    >

    ![Visszaállítási konfiguráció menü](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Az a **konfiguráció visszaállítása** menü alatt **helyreállítás helye**válassza **másik helyre**.
2. Válassza ki az SQL Server nevének és példányának, amelyhez le szeretné állítani az adatbázist.
3. A **visszaállított adatbázis neve** mezőbe írja be a céladatbázis neve.
4. Ha a alkalmazni, válassza ki a **felülírása, ha a kiválasztott SQL-példányon már létezik a DB ugyanazzal a névvel**.
5. Kattintson az **OK** gombra.

    ![A konfiguráció visszaállítása menü értékeinek megadása](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. A **visszaállítási pont kiválasztása**válassza e [adott időpontra való visszaállítása](#restore-to-a-specific-point-in-time), vagy állítsa vissza egy [adott helyreállítási pontot](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A pont – történő visszaállítás csak olyan adatbázisok esetén a teljes és a tömegesen naplózott helyreállítási módban a naplóalapú biztonsági mentések érhető el. 


### <a name="restore-and-overwrite"></a>Visszaállítás és felülírása

1. Az a **konfiguráció visszaállítása** menü alatt **visszaállítás helyének**, jelölje be **felülírása DB** > **OK**.

    ![Válassza ki az adatbázis felülírása](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. A **visszaállítási pont kiválasztása**válassza ** naplók (idő), [adott időpontra való visszaállítása](#restore-to-a-specific-point-in-time), vagy **teljes és különbségi** állíthatja vissza egy [adott helyreállítási pont](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A pont – történő visszaállítás csak olyan adatbázisok esetén a teljes és a tömegesen naplózott helyreállítási modell a naplóalapú biztonsági mentések érhető el. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Adott időpontra való visszaállítása

Ha a kiválasztott **naplók (idő)** visszaállítási típusként, tegye a következőket:

1.  A **dátum és idő visszaállításához**, válassza ki a mini naptárban. Az a **naptár**, félkövérrel szedett dátumokra rendelkezik a helyreállítási pontok, és az aktuális dátumot ki van jelölve.
2. Jelölje ki a helyreállítási pontok dátumát. Dátumok helyreállítási pontok nélkül nem lehet kiválasztani.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Miután kiválasztotta a dátumot, az ütemterv graph folyamatos számos jeleníti meg a rendelkezésre álló helyreállítási pontok.
4. Adjon meg egy időpontot a helyreállításhoz az ütemterv graph használatával, vagy válasszon ki egy időpontot. Ezután kattintson az **OK** gombra.

    ![A naptár megnyitása](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. Az a **speciális konfiguráció** menü, ha meg szeretné tartani az adatbázis nem működő a visszaállítás után engedélyezze **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL**.
5. Ha szeretné a célkiszolgálón a visszaállítás helyének módosításához, adja meg az új cél elérési út.
6. Kattintson az **OK** gombra.

    ![Speciális konfiguráció menü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához.
8. A visszaállítás állapotának nyomon követéséhez a **értesítések** terület, vagy válassza azt **visszaállítási feladatok** adatbázis menüben.

    ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Állítsa vissza egy adott visszaállítási pont

Ha a kiválasztott **teljes és különbségi** visszaállítási típusként, tegye a következőket:


1. Válasszon ki egy helyreállítási pontot a listából, majd kattintson **OK** a visszaállítási pont eljárást.

    ![Válasszon egy teljes helyreállítási pontot](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. Az a **speciális konfiguráció** menü, ha meg szeretné tartani az adatbázis nem működő a visszaállítás után engedélyezze **visszaállítás a NORECOVERY BEÁLLÍTÁSSAL**.
3. Ha szeretné a célkiszolgálón a visszaállítás helyének módosításához, adja meg az új cél elérési út. 
4. Kattintson az **OK** gombra.

    ![Speciális konfigurációs menüje](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. Az a **visszaállítása** menüjében válassza **visszaállítása** a visszaállítási feladat elindításához.
8. A visszaállítás állapotának nyomon követéséhez a **értesítések** terület, vagy válassza azt **visszaállítási feladatok** adatbázis menüben.

    ![Visszaállítási feladat állapota](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>További lépések

[Kezelése és figyelése](manage-monitor-sql-database-backup.md) SQL Server-adatbázisok biztonsági mentése az Azure Backup által.
