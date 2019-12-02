---
title: Az adatbázisok biztonsági mentésével kapcsolatos hibák elhárítása SAP HANA
description: Leírja, hogy miként lehet elhárítani a SAP HANA-adatbázisok biztonsági mentésekor Azure Backup használata során előforduló gyakori hibákat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e8bb1d3328f95b647a788c53afe3ac1455eefa13
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665338"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>SAP HANA-adatbázisok Azure-beli biztonsági mentésének hibáinak megoldása

Ez a cikk a SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentésével kapcsolatos hibaelhárítási információkat tartalmaz. A jelenleg támogatott SAP HANA biztonsági mentési forgatókönyvekkel kapcsolatos további információkért tekintse meg a [forgatókönyvek támogatása](sap-hana-backup-support-matrix.md#scenario-support)című témakört.

## <a name="prerequisites-and-permissions"></a>Előfeltételek és engedélyek

A biztonsági mentések konfigurálása előtt tekintse át az [Előfeltételek](tutorial-backup-sap-hana-db.md#prerequisites) és az [engedélyek beállítása](tutorial-backup-sap-hana-db.md#setting-up-permissions) szakaszt.

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hibaüzenet      | <span style="font-weight:normal">Nem sikerült csatlakozni a HANA rendszerhez</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehet, hogy a SAP HANA példány nem található.<br/>Az Azure Backup számára a HANA-adatbázissal való interakcióhoz szükséges engedélyek nincsenek beállítva. |
| **Javasolt művelet** | Ellenőrizze, hogy a SAP HANA-adatbázis működik-e. Ha az adatbázis működik és fut, ellenőrizze, hogy az összes szükséges engedély be van-e állítva. Ha az engedélyek bármelyike hiányzik, futtassa az [Előregisztráció parancsfájlt](https://aka.ms/scriptforpermsonhana) a hiányzó engedélyek hozzáadásához. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA példány érvénytelen vagy nem található.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Egyetlen Azure-beli virtuális gépen több SAP HANA példányról nem készíthető biztonsági másolat. |
| **Javasolt művelet** | Futtassa az [előregisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) arra a SAP HANA példányra, amelyről biztonsági másolatot szeretne készíteni. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Hibaüzenet      | <span style="font-weight:normal">A megadott SAP HANA művelet nem támogatott</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A SAP HANA készült Azure Backup nem támogatja a növekményes biztonsági mentést és a SAP HANA natív ügyfeleken végrehajtott műveleteket (Studio/cockpit/DBA pilótafülke) |
| **Javasolt művelet** | További [információkért tekintse](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)meg a következő témakört:. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Hibaüzenet      | <span style="font-weight:normal">Ez a SAP HANA-adatbázis nem támogatja a kért biztonsági mentési típust</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Az Azure Backup nem támogatja a növekményes biztonsági mentést és biztonsági mentést Pillanatképek használatával |
| **Javasolt művelet** | További [információkért tekintse](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)meg a következő témakört:. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Hibaüzenet      | <span style="font-weight:normal">A biztonsági mentési napló lánca megszakadt</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Lehetséges, hogy a napló biztonsági mentésének célhelye a backint-ről a fájlrendszerre lett frissítve, vagy a backint végrehajtható fájl módosítva lett. |
| **Javasolt művelet** | A probléma megoldásához indítson el egy teljes biztonsági mentést                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Hibaüzenet      | <span style="font-weight:normal">A visszaállításhoz használt forrás-és célhelyek nem kompatibilisek</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A visszaállításhoz használt cél rendszer nem kompatibilis a forrással |
| **Javasolt művelet** | A ma támogatott visszaállítási típusok megismeréséhez tekintse meg az [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP-megjegyzést. |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Hibaüzenet      | <span style="font-weight:normal">SDC a MDC frissítését észlelte</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | A SAP HANA példány frissítve lett a SDC-ről a MDC-re. A frissítés után a biztonsági mentések sikertelenek lesznek. |
| **Javasolt művelet** | A probléma megoldásához kövesse a [verziófrissítés SAP HANA 1,0 – 2,0 szakaszban](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) felsorolt lépéseket |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet      | <span style="font-weight:normal">A rendszer érvénytelen backint-konfigurációt észlelt</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Lehetséges okok**    | Helytelenül vannak megadva a háttérbeli paraméterek az Azure Backup szolgáltatáshoz |
| **Javasolt művelet** | Ellenőrizze, hogy a következő (backint) paraméterek be vannak-e állítva:<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: FALSE]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Ha backint paraméterek találhatók a GAZDAGÉPen, távolítsa el őket. Ha a paraméterek nem találhatók meg a GAZDAGÉP szintjén, de az adatbázis szintjén manuálisan lettek módosítva, állítsa azokat a megfelelő értékekre a korábban leírtak szerint. Vagy futtassa a [védelem leállítása és a biztonsági mentési adatok megőrzése](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) a Azure Portal, majd válassza a **biztonsági mentés folytatása**lehetőséget. |

## <a name="restore-checks"></a>Visszaállítási ellenőrzések

### <a name="single-container-database-sdc-restore"></a>Egy tároló-adatbázis (SDC) visszaállítása

Ügyeljen a bevitelre, miközben egyetlen tároló-adatbázist (SDC) állít vissza a HANA-hoz egy másik SDC-gépre. Az adatbázis nevét kisbetűs értékkel kell megadni, és zárójelek között a "SDC" utótaggal kell kiegészíteni. A HANA-példány a fővárosokban fog megjelenni.

Tegyük fel, hogy a "H21" SDC HANA-példányról biztonsági másolat készül. A biztonsági mentési elemek lapon megjelenik a biztonsági mentési elem neve **"H21 (SDC)"** néven. Ha megpróbálja visszaállítani az adatbázist egy másik cél SDC, tegyük fel, hogy a H11 a következő bemeneti adatokat kell megadni.

![SDC-visszaállítási bemenetek](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Vegye figyelembe a következő szempontokat:

- Alapértelmezés szerint a visszaállított adatbázis neve a biztonsági mentési elem nevével lesz feltöltve, azaz H21 (SDC)
- A cél kiválasztása, mivel a H11 nem módosítja automatikusan a visszaállított adatbázis nevét. **Ezt a H11 (SDC) kell szerkeszteni**. A SDC kapcsolatban a visszaállított adatbázis neve a célként megadott példány-azonosító kisbetűkkel és a "SDC" zárójelek között.
- Mivel a SDC csak egyetlen adatbázissal rendelkezhet, a jelölőnégyzetre kattintva engedélyezheti a meglévő adatbázis-adatmennyiség felülbírálását a helyreállítási pontra vonatkozó adattal.
- A Linux megkülönbözteti a kis-és nagybetűket. Ezért ügyeljen arra, hogy megőrizze a kis-és nagybetűket.

### <a name="multiple-container-database-mdc-restore"></a>Több tároló-adatbázis (MDC) visszaállítása

A HANA-hoz készült több Container Database-ben a standard konfiguráció SYSTEMDB + 1 vagy több bérlői adatbázis. A teljes SAP HANA példány visszaállítása a SYSTEMDB és a bérlői adatbázisok visszaállítását jelenti. Először az egyik visszaállítja a SYSTEMDB, majd a bérlői adatbázist folytatja. A rendszeradatbázis lényegében azt jelenti, hogy felülbírálja a kiválasztott cél rendszerinformációit. Ez a visszaállítás felülbírálja a BackInt kapcsolatos információkat is a cél példányban. Ezért miután a rendszeradatbázist visszaállította egy cél példányra, az egyiknek újra kell futtatnia az előzetes regisztrációs parancsfájlt. A következő bérlői adatbázis-visszaállítások sikeresek lesznek.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Frissítés SAP HANA 1,0 – 2,0

Ha SAP HANA 1,0-es adatbázist véd, és a 2,0-re kíván frissíteni, hajtsa végre az alábbi lépéseket:

- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) a régi SDC-adatbázis megőrzése érdekében.
- Futtassa újra az [előzetes regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) a (SID és MDC) helyes részleteivel.
- A bővítmény újbóli regisztrálása (Backup-> nézet részletei – > Válassza ki a megfelelő Azure-beli virtuális gépet – > újra regisztrálja).
- Kattintson az azonos virtuális géphez tartozó adatbázisok újbóli felderítése elemre. Ez a művelet a 2. lépésben szereplő új adatbázisok helyes részleteit (SYSTEMDB és bérlői adatbázis, nem SDC) jeleníti meg.
- Az új adatbázisok védelméhez.

## <a name="upgrading-without-an-sid-change"></a>Frissítés SID-változás nélkül

A SID-módosítást nem okozó operációs rendszerre vagy SAP HANAra való frissítés az alábbi módon kezelhető:

- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) az adatbázis megőrzése érdekében
- Az [előzetes regisztrációs parancsfájl](https://aka.ms/scriptforpermsonhana) újrafuttatása
- Az adatbázis [védelmének](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) újbóli folytatása

## <a name="next-steps"></a>Következő lépések

- Tekintse át a SAP HANA adatbázisok Azure-beli virtuális gépekről történő biztonsági mentésével kapcsolatos [gyakori kérdéseket](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) .)
