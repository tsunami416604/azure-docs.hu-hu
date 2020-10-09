---
title: Műveletek végrehajtása a Backup Center használatával
description: Ez a cikk azt ismerteti, hogyan hajtható végre műveletek a Backup Center használatával
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 65ac6374b1a9571d0415bc097a6ee957874d3d44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854426"
---
# <a name="perform-actions-using-backup-center"></a>Műveletek végrehajtása a Backup Center használatával

A Backup Center lehetővé teszi, hogy a kulcsfontosságú biztonsági másolatokkal kapcsolatos műveleteket egy központi interfészből hajtsa végre, anélkül, hogy egy különálló tárolóra kellene navigálnia. A biztonsági mentési központból elvégezhető műveletek a következők:

* Az adatforrások biztonsági mentésének konfigurálása
* Biztonsági mentési példány visszaállítása
* Új tároló létrehozása
* Új biztonsági mentési szabályzat létrehozása
* Igény szerinti biztonsági mentés indítása biztonsági mentési példányhoz
* Biztonsági mentés leállítása biztonsági mentési példány esetén

## <a name="supported-scenarios"></a>Támogatott esetek

* A biztonsági mentési központ jelenleg támogatott az Azure-beli virtuális gépek biztonsági mentéséhez és a Azure Database for PostgreSQL Server biztonsági mentéshez.
* A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Attól függően, hogy milyen adatforrást szeretne biztonsági másolatot készíteni, kövesse az alábbi útmutatást.

### <a name="configure-backup-for-azure-virtual-machines"></a>Azure-beli virtuális gépek biztonsági mentésének konfigurálása

1. Navigáljon a biztonsági mentési központhoz, és válassza a **+ biztonsági mentés** lehetőséget az **Áttekintés** lap tetején.

    ![A Backup Center áttekintése](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Válassza ki a biztonsági mentéshez használni kívánt adatforrás típusát (ebben az esetben az Azure-beli virtuális gép).

    ![A virtuális gép biztonsági mentésének konfigurálásához válassza az adatforrás lehetőséget](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Válasszon egy Recovery Services-tárolót, és válassza a **Folytatás**lehetőséget. Ez olyan biztonsági mentési konfigurációt eredményez, amely megegyezik egy Recovery Services-tárolóból elérhetővel. [További információ az Azure-beli virtuális gépek biztonsági mentésének Recovery Services-tárolóval való konfigurálásáról](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL-kiszolgáló biztonsági mentésének konfigurálása

1. Navigáljon a biztonsági mentési központhoz, és válassza a **+ biztonsági mentés** lehetőséget az **Áttekintés** lap tetején.
2. Válassza ki a biztonsági mentéshez használni kívánt adatforrás típusát (ebben az esetben Azure Database for PostgreSQL-kiszolgáló).

    ![Válassza ki az adatforrást Azure Database for PostgreSQL kiszolgáló biztonsági mentésének konfigurálásához](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Válassza a **Folytatás**lehetőséget. Ez a biztonsági mentési konfigurációt a Backup-tárolóból elérhetőnek tekinti. [További információ arról, hogyan konfigurálhatja a biztonsági mentést Azure Database for PostgreSQL-kiszolgálóra egy backup-tárolóval](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Biztonsági mentési példány visszaállítása

A visszaállítani kívánt adatforrás típusától függően kövesse az alább leírtak szerint megjelenő utasításokat.

### <a name="if-youre-restoring-an-azure-virtual-machine"></a>Ha egy Azure-beli virtuális gépet állít helyre

1. Navigáljon a biztonsági mentési központhoz, és válassza a **visszaállítás** lehetőséget az **Áttekintés** lap tetején.

    ![Biztonsági mentési központ – áttekintés a virtuális gép visszaállításához](./media/backup-center-actions/backup-center-overview-restore.png)

2. Válassza ki a visszaállítani kívánt adatforrás típusát (ebben az esetben az Azure-beli virtuális gép).

    ![Adatforrás kiválasztása a virtuális gép visszaállításához](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Válasszon ki egy biztonsági mentési példányt, és válassza a **Folytatás**lehetőséget. Ezzel a beállítással a visszaállítási beállítások olyanok, amelyek megegyeznek egy Recovery Services-tárolóból elérhetővel. [További információ az Azure-beli virtuális gépek Recovery Services-](backup-azure-arm-restore-vms.md#before-you-start)tárolóval történő visszaállításáról.

### <a name="if-youre-restoring-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL kiszolgáló visszaállításakor

1. Navigáljon a biztonsági mentési központhoz, és válassza a **visszaállítás** lehetőséget az **Áttekintés** lap tetején.
2. Válassza ki a visszaállítani kívánt adatforrás típusát (ebben az esetben Azure Database for PostgreSQL-kiszolgáló).

    ![Adatforrás kiválasztása Azure Database for PostgreSQL kiszolgáló visszaállításakor](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Válasszon ki egy biztonsági mentési példányt, és válassza a **Folytatás**lehetőséget. Ezzel a beállítással a visszaállítási beállítások olyanok, amelyek megegyeznek egy Recovery Services-tárolóból elérhetővel. [További információ a Azure Database for PostgreSQL-kiszolgálók biztonsági mentési tárolóval való visszaállításáról](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Új tároló létrehozása

Új tároló létrehozásához lépjen a Backup Center webhelyre, és válassza a **+ Vault** lehetőséget az **Áttekintés** lap tetején.

![Tár létrehozása](./media/backup-center-actions/backup-center-create-vault.png)

* [További információ a Recovery Services-tároló létrehozásáról](backup-create-rs-vault.md)
* [További tudnivalók a Backup-tárolók létrehozásáról](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Új biztonsági mentési szabályzat létrehozása

Attól függően, hogy milyen adatforrást szeretne biztonsági másolatot készíteni, kövesse az alább ismertetett utasításokat.

### <a name="if-youre-backing-up-an-azure-virtual-machine"></a>Ha Azure-beli virtuális gép biztonsági mentését végzi

1. Navigáljon a biztonsági mentési központhoz, és válassza a **+ házirend** elemet az **Áttekintés** lap tetején.

    ![Backup Center – áttekintés a biztonsági mentési szabályzatról](./media/backup-center-actions/backup-center-overview-policy.png)

2. Válassza ki a biztonsági mentéshez használni kívánt adatforrás típusát (ebben az esetben az Azure-beli virtuális gép).

    ![Válassza ki a virtuális gép biztonsági mentésére vonatkozó házirend adatforrását](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Válassza ki a Recovery Services-tárolót, és válassza a **Folytatás**lehetőséget. Ez olyan házirend-létrehozási élményt eredményez, amely megegyezik egy Recovery Services-tárolóból elérhetővel. [További információ arról, hogyan hozhat létre új biztonsági mentési szabályzatot az Azure-beli virtuális géphez a Recovery Services-tárolóval](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-an-azure-database-for-postgresql-server"></a>Ha Azure Database for PostgreSQL-kiszolgáló biztonsági mentését végzi

1. Navigáljon a biztonsági mentési központhoz, és válassza a **+ házirend** elemet az **Áttekintés** lap tetején.
2. Válassza ki a biztonsági mentéshez használni kívánt adatforrás típusát (ebben az esetben Azure Database for PostgreSQL-kiszolgáló).

    ![Válassza ki az adatforrást Azure Database for PostgreSQL kiszolgáló biztonsági mentésére vonatkozó házirendhez](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Válassza a **Folytatás**lehetőséget. Ez a szabályzat létrehozásának olyan élményét eredményezi, amely megegyezik a Backup-tárolóból elérhetővel. [További információ arról, hogyan hozhat létre új biztonsági mentési szabályzatot egy backup](backup-azure-database-postgresql.md#create-backup-policy)-tárolóval.

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Igény szerinti biztonsági mentés végrehajtása biztonsági mentési példányhoz

A Backup Center lehetővé teszi a biztonsági mentési példányok keresését a Backup-hagyatékon, és igény szerint végrehajtja a biztonsági mentési műveleteket.

Igény szerinti biztonsági mentés elindításához navigáljon a Backup Center elemhez, és válassza a **biztonsági mentési példányok** menüelemet. Ezzel a beállítással megtekintheti az összes olyan biztonsági mentési példány adatait, amelyekhez hozzáfér. Megkeresheti azt a biztonsági mentési példányt, amelyről biztonsági másolatot szeretne készíteni. Kattintson a jobb gombbal a rács egyik elemére, és megnyílik az elérhető műveletek listája. Válassza a **biztonsági mentés** lehetőséget egy igény szerinti biztonsági mentés végrehajtásához.

![Igény szerinti biztonsági mentés](./media/backup-center-actions/backup-center-on-demand-backup.png)

[További információ az Azure Virtual Machines igény szerinti biztonsági mentéséről](backup-azure-manage-vms.md#run-an-on-demand-backup).

[További információ a Azure Database for PostgreSQL-kiszolgáló igény szerinti biztonsági másolatainak elvégzéséről](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Biztonsági mentés leállítása biztonsági mentési példány esetén

Előfordulhat, hogy a biztonsági másolatok biztonsági mentését le szeretné állítani, például ha a mögöttes erőforrásról biztonsági másolat készül, már nem létezik.

Igény szerinti biztonsági mentés elindításához navigáljon a Backup Center elemhez, és válassza a **biztonsági mentési példányok** menüelemet. Ezzel a beállítással megtekintheti az összes olyan biztonsági mentési példány részleteit, amelyekhez hozzáfér. Megkeresheti azt a biztonsági mentési példányt, amelyről biztonsági másolatot szeretne készíteni. Kattintson a jobb gombbal a rács egyik elemére, és megnyílik az elérhető műveletek listája. Válassza a biztonsági **Mentés leállítása** lehetőséget a biztonsági mentési példány biztonsági mentésének leállításához.

![A védelem kikapcsolása](./media/backup-center-actions/backup-center-stop-protection.png)

[További információ az Azure-Virtual Machines biztonsági mentésének leállításáról](backup-azure-manage-vms.md#stop-protecting-a-vm).

[További információ a Azure Database for PostgreSQL-kiszolgáló biztonsági mentésének leállításáról](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>További lépések

* [Biztonsági másolatok figyelése és üzemeltetése](backup-center-monitor-operate.md)
* [A biztonsági mentési hagyaték szabályozása](backup-center-govern-environment.md)
* [Bepillantást nyerhet a biztonsági másolatokra](backup-center-obtain-insights.md)
