---
title: SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépeken
description: Ebből a cikkből megtudhatja, hogyan állíthatja vissza az Azure Virtual Machines-on futó SAP HANA adatbázisokat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287917"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépeken

Ez a cikk azt ismerteti, hogyan lehet visszaállítani az Azure-beli virtuális gépen (VM) futó SAP HANA-adatbázisokat, hogy a Azure Backup szolgáltatás biztonsági mentést készített egy Azure Backup Recovery Services-tárolóra. A visszaállítással az adatok másolatait lehet létrehozni fejlesztési és tesztelési forgatókönyvekhez, vagy visszatérhet egy korábbi állapotba.

További információ a SAP HANA adatbázisok biztonsági mentéséről: [SAP HANA adatbázisok biztonsági mentése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Visszaállítás egy időpontra vagy egy helyreállítási pontra

A Azure Backup az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat a következőképpen állíthatja helyre:

* Visszaállítás adott dátumra vagy időpontra (a másodikra) a naplók biztonsági másolatainak használatával. A Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági másolatokat, valamint a visszaállításhoz szükséges napló biztonsági mentésének láncát a kiválasztott idő alapján.

* Egy adott helyreállítási pontra való visszaállításhoz állítsa vissza egy adott teljes vagy különbözeti biztonsági mentést.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis visszaállítása előtt vegye figyelembe a következőket:

* Az adatbázist csak olyan SAP HANA-példányra állíthatja vissza, amely ugyanabban a régióban található

* A cél példányt regisztrálni kell ugyanazzal a tárolóval, mint a forrás

* Azure Backup nem azonosítható két különböző SAP HANA példány ugyanazon a virtuális gépen. Ezért az adatok egyik példányról a másikra való visszaállítása nem lehetséges.

* Annak érdekében, hogy a cél SAP HANA-példány készen álljon a visszaállításra, ellenőrizze a **biztonsági mentés készültségi** állapotát:

  * Nyissa meg a tárolót, amelyben a cél SAP HANA példány regisztrálva van

  * A tároló irányítópultjának **első lépések**területén válassza a **biztonsági mentés** lehetőséget.

![Biztonsági mentés a tároló irányítópultján](media/sap-hana-db-restore/getting-started-backup.png)

* A **biztonsági**mentés alatt a **mit szeretne biztonsági másolatot készíteni?** területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**

![SAP HANA kiválasztása az Azure-beli virtuális gépen](media/sap-hana-db-restore/sap-hana-backup.png)

* A **virtuális gépek felderítése** területen kattintson a **részletek megtekintése** elemre.

![Részletek](media/sap-hana-db-restore/view-details.png)

* A cél virtuális gép **biztonsági mentési készültségének** áttekintése

![Védett kiszolgálók](media/sap-hana-db-restore/protected-servers.png)

* Ha többet szeretne megtudni a SAP HANA által támogatott visszaállítási típusokról, tekintse meg a SAP HANA Megjegyzés [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Adatbázis helyreállítása

* Nyissa meg a tárolót, amelyben a visszaállítani kívánt SAP HANA adatbázis regisztrálva van

* A tároló irányítópultján, a **védett elemek**területen válassza a **biztonsági másolati elemek elemet** .

![Biztonsági másolati elemek](media/sap-hana-db-restore/backup-items.png)

* A **biztonsági másolati elemek** **területen válassza** **Az Azure-beli virtuális gép SAP HANA** kiválasztása lehetőséget.

![Biztonságimásolat-kezelés típusa](media/sap-hana-db-restore/backup-management-type.png)

* Válassza ki a visszaállítani kívánt adatbázist

 ![Visszaállítani kívánt adatbázis](media/sap-hana-db-restore/database-to-restore.png)

* Tekintse át az adatbázis menüt. Az adatbázis biztonsági mentésével kapcsolatos információkat tartalmaz, beleértve a következőket:

  * A legrégebbi és a legújabb visszaállítási pontok

  * Az adatbázis utolsó 24 és 72 órájának naplózási biztonsági mentési állapota

![Adatbázis menü](media/sap-hana-db-restore/database-menu.png)

* A **visszaállítási adatbázis** kiválasztása

* A **konfiguráció visszaállítása**területen adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adatvisszaállítást:

  * **Másik hely**: állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrás-adatbázist.

  * **Adatbázis felülírása**: állítsa vissza az adatforrást ugyanarra a SAP HANA példányra, mint az eredeti forrást. Ez a beállítás felülírja az eredeti adatbázist.

![Konfiguráció visszaállítása](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

* A visszaállítási **konfiguráció** menüben, a **visszaállítás helye**területen válassza a **másik hely**lehetőséget.

![Visszaállítás másik helyre](media/sap-hana-db-restore/restore-alternate-location.png)

* Válassza ki azt a SAP HANA állomásnevet és példánynév, amelyre vissza kívánja állítani az adatbázist.
* Ellenőrizze, hogy a cél SAP HANA-példány készen áll-e a visszaállításra a **biztonsági mentési készültségének** biztosításával. További részletekért tekintse meg az [Előfeltételek című szakaszt](#prerequisites) .
* A **visszaállított adatbázis neve** mezőbe írja be a céladatbázis nevét.

> [!NOTE]
> Önálló adatbázis tároló (SDC) visszaállításának az alábbi [ellenőrzéseknek](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)kell megfelelnie.

* Ha van ilyen, válassza **a felülírás lehetőséget, ha a kiválasztott HANA-példányon már van ilyen nevű adatbázis**.
* Kattintson az **OK** gombra.

![Konfiguráció visszaállítása – végső képernyő](media/sap-hana-db-restore/restore-configuration-last.png)

* A **visszaállítási pont kiválasztása lapon**válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** [egy adott helyreállítási pontra való visszaállításhoz](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

* A visszaállítási **konfiguráció** menüben a **visszaállítás helye**alatt válassza az **adatbázis felülírása** > **OK**elemet.

![Adatbázis felülírása](media/sap-hana-db-restore/overwrite-db.png)

* A **visszaállítási pont kiválasztása lapon**válassza ki a **naplók (** adott időpontban) lehetőséget [egy adott időpontra való visszaállításhoz](#restore-to-a-specific-point-in-time). Vagy válassza a **teljes & különbözetet** [egy adott helyreállítási pontra való visszaállításhoz](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás adott időpontra

Ha a **naplókat (időpontot)** a visszaállítási típusként választotta, tegye a következőket:

* Válasszon ki egy helyreállítási pontot a log gráfból, majd kattintson az **OK** gombra a visszaállítási pont kiválasztásához.

![Visszaállítási pont](media/sap-hana-db-restore/restore-point.png)

* A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.

![Visszaállítás kiválasztása](media/sap-hana-db-restore/restore-restore.png)

* Kövesse nyomon a visszaállítási folyamatot az **értesítések** területén, vagy nyomon követheti az adatbázis menü **feladatok visszaállítása** elemét választva.

![A visszaállítás sikeresen elindítva](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Visszaállítás adott helyreállítási pontra

Ha a **teljes & különbözetet** választotta a visszaállítási típusként, tegye a következőket:

* Válasszon ki egy helyreállítási pontot a listából, és kattintson az **OK** gombra a visszaállítási pont kiválasztásához.

![Adott helyreállítási pont visszaállítása](media/sap-hana-db-restore/specific-recovery-point.png)

* A visszaállítási **menüben válassza a visszaállítás** **lehetőséget a visszaállítási** feladatok elindításához.

![Visszaállítási feladatok indítása](media/sap-hana-db-restore/restore-specific.png)

* Kövesse nyomon a visszaállítási folyamatot az **értesítések** területén, vagy nyomon követheti az adatbázis menü **feladatok visszaállítása** elemét választva.

![Visszaállítási folyamat](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Több adatbázis-tárolóban (MDC) a rendszer visszaállítja a rendszeradatbázist a célként megadott példányra, az egyiknek újra kell futtatnia az előzetes regisztrációs parancsfájlt. A következő bérlői adatbázis-visszaállítások sikeresek lesznek. További információt a [Hibaelhárítás – MDC visszaállítás](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan](sap-hana-db-manage.md) kezelheti a biztonsági mentést SAP HANA-adatbázisokat a Azure Backup használatával
