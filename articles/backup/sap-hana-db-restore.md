---
title: SAP HANA-adatbázisok visszaállítása az Azure virtuális gépeken
description: Ebben a cikkben megtudhatja, hogyan állíthatja vissza az Azure virtuális gépeken futó SAP HANA-adatbázisokat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287917"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA-adatbázisok visszaállítása az Azure virtuális gépeken

Ez a cikk ismerteti, hogyan állíthatja vissza az Azure virtuális gépen (VM) futó SAP HANA-adatbázisokat, amelyekről az Azure Backup szolgáltatás biztonsági másolatot készített egy Azure Backup Recovery Services-tárolóba. A visszaállítások segítségével másolatot készíthet az adatokról a fejlesztési/ tesztelési forgatókönyvekhez, vagy visszatérhet egy korábbi állapothoz.

További információ az SAP HANA-adatbázisok biztonsági mentése, olvassa el [az SAP HANA-adatbázisok biztonsági mentése az Azure virtuális gépeken.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Visszaállítás egy adott időpontra vagy egy helyreállítási pontra

Az Azure Backup az azure-beli virtuális gépeken futó SAP HANA-adatbázisokat az alábbiak szerint állíthatja vissza:

* Visszaállítás egy adott dátumra vagy időpontra (a másodikra) naplóbiztonsági mentések használatával. Az Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági mentések és a napló biztonsági mentések láncolatát, amelyek a kiválasztott idő alapján visszaállíthatók.

* Visszaállítás egy adott teljes vagy különbözeti biztonsági mentésre egy adott helyreállítási pontra való visszaállításhoz.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis visszaállítása előtt vegye figyelembe az alábbiakat:

* Az adatbázis t csak egy SAP HANA-példány, amely ugyanabban a régióban

* A célpéldányt ugyanazzal a tárolóval kell regisztrálni, mint a forrást.

* Az Azure Backup nem tud azonosítani két különböző SAP HANA-példányokat ugyanazon a virtuális gépen. Ezért az adatok visszaállítása az egyik példányból a másikba ugyanazon a virtuális gépen nem lehetséges

* Annak érdekében, hogy a cél SAP HANA-példány készen áll a visszaállításra, ellenőrizze a **biztonsági mentés készenléti** állapotát:

  * Nyissa meg azt a tárolót, amelyben a cél SAP HANA-példány regisztrálva van

  * A tároló irányítópultján az **Első lépések**csoportban válassza a Biztonsági **mentés lehetőséget.**

![Biztonsági mentés a tároló irányítópultján](media/sap-hana-db-restore/getting-started-backup.png)

* A **Biztonsági másolat csoportban**a **Mit szeretne biztonsági másolatot készíteni?** válassza **az SAP HANA az Azure VM-ben**

![Válassza az SAP HANA-t az Azure virtuális gépében](media/sap-hana-db-restore/sap-hana-backup.png)

* A **Virtuális gépek DB-k felfedezése csoportban** kattintson a **Részletek megtekintése elemre**

![Részletek megtekintése](media/sap-hana-db-restore/view-details.png)

* A célvirtuális gép **biztonsági mentési készenlétének** áttekintése

![Védett kiszolgálók](media/sap-hana-db-restore/protected-servers.png)

* Ha többet szeretne megtudni az SAP HANA által támogatott visszaállítási típusokról, olvassa el az SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) megjegyzést

## <a name="restore-a-database"></a>Adatbázis helyreállítása

* Nyissa meg azt a tárolót, amelyben a visszaállítandó SAP HANA-adatbázis regisztrálva van

* A tároló irányítópultján, a **Védett elemek**csoportban válassza a **Biztonsági másolat elemei t.**

![Biztonsági másolat elemei](media/sap-hana-db-restore/backup-items.png)

* A **Biztonsági másolat elemei csoportban**a Biztonsági másolat kezelése típusa **csoportban** válassza az **SAP HANA elemet az Azure virtuális gépben**

![Biztonsági mentés felügyeleti típusa](media/sap-hana-db-restore/backup-management-type.png)

* A visszaadandó adatbázis kijelölése

 ![Visszaállítandó adatbázis](media/sap-hana-db-restore/database-to-restore.png)

* Tekintse át az adatbázis menüjét. Információkat nyújt az adatbázis biztonsági mentéséről, többek között a következőkről:

  * A legrégebbi és legújabb visszaállítási pontok

  * Az adatbázis legutóbbi 24 és 72 órájának naplóbiztonsági mentési állapota

![Adatbázis menü](media/sap-hana-db-restore/database-menu.png)

* Válassza **a Visszaállítás i.**

* A **Konfiguráció visszaállítása csoportban**adja meg, hogy hol (vagy hogyan) hol állítsa vissza az adatokat:

  * **Alternatív hely:** Állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrásadatbázist.

  * **Adatbázis felülírása:** Állítsa vissza az adatokat ugyanabba az SAP HANA-példányra, mint az eredeti forrás. Ez a beállítás felülírja az eredeti adatbázist.

![Konfiguráció visszaállítása](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

* A **Konfiguráció visszaállítása** menü Hol állítsa vissza a **Helyét,** válassza az **Alternatív hely lehetőséget.**

![Visszaállítás másik helyre](media/sap-hana-db-restore/restore-alternate-location.png)

* Válassza ki azt az SAP HANA állomásnevet és példánynevet, amelyre vissza szeretné állítani az adatbázist.
* Ellenőrizze, hogy a cél SAP HANA-példány készen áll-e a visszaállításra a **biztonsági mentési készenlét biztosításával.** További részleteket az [előfeltételek című részben](#prerequisites) talál.
* A **Visszaállított adatbázisnév** mezőbe írja be a céladatbázis nevét.

> [!NOTE]
> Az egyadatbázis-tároló (SDC) visszaállításának követnie kell ezeket az [ellenőrzéseket.](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)

* Ha van ilyen, válassza a Felülírás lehetőséget, **ha az azonos nevű adatbázis már létezik a kijelölt HANA-példányon.**
* Válassza **az OK gombot.**

![Konfiguráció visszaállítása - végső képernyő](media/sap-hana-db-restore/restore-configuration-last.png)

* A **Visszaállítási pont kiválasztása**csoportban válassza a Naplók **(időpontban)** lehetőséget [egy adott időpontra való visszaállításhoz.](#restore-to-a-specific-point-in-time) Vagy válassza **a Teljes & különbözetet** [egy adott helyreállítási pont visszaállításához.](#restore-to-a-specific-recovery-point)

### <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

* A **Konfiguráció visszaállítása** menü **Hol állítsa vissza,** válassza a Adatbázis írása AZ OK **felülírása** > **OK**lehetőséget.

![Adatbázis felülírása](media/sap-hana-db-restore/overwrite-db.png)

* A **Visszaállítási pont kiválasztása**csoportban válassza a Naplók **(időpontban)** lehetőséget [egy adott időpontra való visszaállításhoz.](#restore-to-a-specific-point-in-time) Vagy válassza **a Teljes & különbözetet** [egy adott helyreállítási pont visszaállításához.](#restore-to-a-specific-recovery-point)

### <a name="restore-to-a-specific-point-in-time"></a>Visszaállítás egy adott időpontra

Ha **a Naplók (időpontban)** lehetőséget választotta visszaállítási típusként, tegye a következőket:

* Jelöljön ki egy helyreállítási pontot a naplódiagramból, és válassza az **OK** gombot a visszaállítási pont kiválasztásához.

![Visszaállítási pont](media/sap-hana-db-restore/restore-point.png)

* A **Visszaállítás** menü **visszaállítása parancsára** kattintson a visszaállítási feladat elindításához.

![Visszaállítás kiválasztása](media/sap-hana-db-restore/restore-restore.png)

* Az **Értesítések** területen nyomon követheti a visszaállítási folyamatot, vagy nyomon követheti azt az adatbázis menü **Feladatok visszaállítása** parancsával.

![A visszaállítás sikeresen megtörtént](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Visszaállítás egy adott helyreállítási pontra

Ha visszaállítási típusként **a Teljes & különbözetet** választotta, tegye a következőket:

* Jelöljön ki egy helyreállítási pontot a listából, és válassza az **OK** gombot a visszaállítási pont kiválasztásához.

![Adott helyreállítási pont visszaállítása](media/sap-hana-db-restore/specific-recovery-point.png)

* A **Visszaállítás** menü **visszaállítása parancsára** kattintson a visszaállítási feladat elindításához.

![Visszaállítási feladat indítása](media/sap-hana-db-restore/restore-specific.png)

* Az **Értesítések** területen nyomon követheti a visszaállítási folyamatot, vagy nyomon követheti azt az adatbázis menü **Feladatok visszaállítása** parancsával.

![A folyamat visszaállítása](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Több adatbázis-tárolóban (MDC) a rendszeradatbázis-visszaállítás után a rendszeradatbázis-visszaállítás után újra futtatni kell az előzetes regisztrációs parancsfájlt. Csak akkor a későbbi bérlői adatbázis-visszaállítások sikeresek lesznek. További információt a [Hibaelhárítás – MDC visszaállítás című témakörben olvashat.](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan](sap-hana-db-manage.md) kezelheti az Azure Backup használatával biztonsági mentést készítő SAP HANA-adatbázisokat
