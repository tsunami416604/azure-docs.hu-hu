---
title: Az adatbázisok biztonsági mentésével kapcsolatos hibák elhárítása SAP HANA
description: Leírja, hogy miként lehet elhárítani a SAP HANA-adatbázisok biztonsági mentésekor Azure Backup használata során előforduló gyakori hibákat.
ms.topic: conceptual
ms.date: 08/03/2019
ms.openlocfilehash: cbffa7415f315fd396e57afa355d2415c4612eb5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172751"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>SAP HANA-adatbázisok Azure-beli biztonsági mentésének hibáinak megoldása

Ez a cikk a SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentésével kapcsolatos hibaelhárítási információkat tartalmaz. A következő szakasz a SAP HANA biztonsági mentés gyakori hibáinak diagnosztizálásához szükséges fontos fogalmakat ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Az [Előfeltételek](backup-azure-sap-hana-database.md#prerequisites)részeként győződjön meg arról, hogy az Előregisztráció parancsfájlt futtatták azon a virtuális gépen, ahol a HANA telepítve van.

### <a name="setting-up-permissions"></a>Engedélyek beállítása

Az Előregisztráció szkriptje:

1. AZUREWLBACKUPHANAUSER hoz létre a HANA-rendszeren, és hozzáadja ezeket a szükséges szerepköröket és engedélyeket:
    - ADATBÁZIS-rendszergazda: új adatbázisok létrehozása a visszaállítás során.
    - Katalógus OLVASása: a biztonsági mentési katalógus beolvasása.
    - SAP_INTERNAL_HANA_SUPPORT: néhány privát tábla eléréséhez.
2. Feltesz egy kulcsot a HANA beépülő modul Hdbuserstore az összes művelet (adatbázis-lekérdezések, visszaállítási műveletek, a biztonsági mentés konfigurálása és futtatása) kezelésére.

   A kulcs létrehozásának megerősítéséhez futtassa a HDBSQL parancsot a HANA gépen a SIDADM hitelesítő adataival:

    ``` hdbsql
    hdbuserstore list
    ```

    A parancs kimenetének meg kell jelennie a {SID} {DBNAME} kulcsnak, amely a felhasználó AZUREWLBACKUPHANAUSER jelenik meg.

> [!NOTE]
> Győződjön meg arról, hogy a SSFS-fájlok egyedi készlete van a **/usr/sap/{SID}/Home/.HDB/** területen. Ezen az elérési úton csak egy mappa lehet.

### <a name="setting-up-backint-parameters"></a>BackInt paramétereinek beállítása

Miután kiválasztott egy adatbázist a biztonsági mentéshez, a Azure Backup szolgáltatás az backInt-paramétereket az adatbázis szintjén konfigurálja:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Győződjön meg arról, hogy ezek a paraméterek *nem* találhatók a gazdagép szintjén. A gazdagép szintű paraméterek felülbírálják ezeket a paramétereket, és váratlan viselkedést okozhatnak.

## <a name="restore-checks"></a>Visszaállítási ellenőrzések

### <a name="single-container-database-sdc-restore"></a>Egy tároló-adatbázis (SDC) visszaállítása

Ügyeljen a bevitelre, miközben egyetlen tároló-adatbázist (SDC) állít vissza a HANA-hoz egy másik SDC-gépre. Az adatbázis nevét kisbetűs értékkel kell megadni, és zárójelek között a "SDC" utótaggal kell kiegészíteni. A HANA-példány a fővárosokban fog megjelenni.

Tegyük fel, hogy a "H21" SDC HANA-példányról biztonsági másolat készül. A biztonsági mentési elemek lapon megjelenik a biztonsági mentési elem neve **"H21 (SDC)"** néven. Ha megpróbálja visszaállítani az adatbázist egy másik cél SDC, tegyük fel, hogy a H11 a következő bemeneti adatokat kell megadni.

![SDC-visszaállítási bemenetek](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Vegye figyelembe a következő szempontokat:

- Alapértelmezés szerint a visszaállított adatbázis neve a biztonsági mentési elem nevével lesz feltöltve, azaz H21 (SDC)
- A cél kiválasztása, mivel a H11 nem módosítja automatikusan a visszaállított adatbázis nevét. **Ezt a H11 (SDC) kell szerkeszteni**. A SDC esetében a visszaállított adatbázis neve a célként megadott példány-azonosító kisbetűkkel és a "SDC" zárójelek között.
- Mivel a SDC csak egyetlen adatbázissal rendelkezhet, a jelölőnégyzetre kattintva engedélyezheti a meglévő adatbázis-adatmennyiség felülbírálását a helyreállítási pontra vonatkozó adattal.
- A Linux megkülönbözteti a kis-és nagybetűket, ezért ügyeljen arra, hogy megőrizze az esetet.

### <a name="multiple-container-database-mdc-restore"></a>Több tároló-adatbázis (MDC) visszaállítása

A HANA-hoz készült több Container Database-ben a standard konfiguráció SYSTEMDB + 1 vagy több bérlői adatbázis. A teljes SAP HANA példány visszaállítása a SYSTEMDB és a bérlői adatbázisok visszaállítását jelenti. Először az egyik visszaállítja a SYSTEMDB, majd a bérlői adatbázist folytatja. A rendszeradatbázis lényegében azt jelenti, hogy felülbírálja a kiválasztott cél rendszerinformációit. Ez felülbírálja a BackInt kapcsolatos információkat is a cél példányban. Ezért miután a rendszeradatbázist visszaállította egy cél példányra, az egyiknek újra kell futtatnia az előzetes regisztrációs parancsfájlt. A következő bérlői adatbázis-visszaállítások sikeresek lesznek.

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem sikerült csatlakozni a HANA rendszerhez. Ellenőrizze, hogy a rendszer működik-e.| A Azure Backup szolgáltatás nem tud csatlakozni a HANA-hoz, mert a HANA-adatbázis nem működik. Vagy a HANA fut, de nem engedélyezi a Azure Backup szolgáltatás kapcsolódását. | Győződjön meg arról, hogy a HANA-adatbázis vagy-szolgáltatás nem áll le. Ha a HANA-adatbázis vagy-szolgáltatás fut, ellenőrizze, hogy az [összes engedély be](#setting-up-permissions)van-e állítva. Ha a kulcs hiányzik, futtassa újra az előregisztrációi parancsfájlt egy új kulcs létrehozásához. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A rendszer érvénytelen Backint-konfigurációt észlelt. Állítsa le a védelmet, és konfigurálja újra az adatbázist.| A backInt paraméterek helytelenül vannak megadva a Azure Backuphoz. | Győződjön [meg arról, hogy a paraméterek be vannak állítva](#setting-up-backint-parameters). Ha backInt paraméterek találhatók a GAZDAGÉPen, távolítsa el őket. Ha a paraméterek nem találhatók meg a GAZDAGÉP szintjén, de az adatbázis szintjén manuálisan lettek módosítva, állítsa azokat a megfelelő értékekre a korábban leírtak szerint. Vagy futtassa a **védelem leállítása és a biztonsági mentési adatok megőrzése** a Azure Portal, majd válassza a **biztonsági mentés folytatása**lehetőséget.|
