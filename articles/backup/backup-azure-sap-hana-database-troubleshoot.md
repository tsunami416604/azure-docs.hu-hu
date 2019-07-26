---
title: Hibák elhárítása SAP HANA adatbázisok Azure Backup használatával történő biztonsági mentése során | Microsoft Docs
description: Leírja, hogy miként lehet elhárítani a SAP HANA-adatbázisok biztonsági mentésekor Azure Backup használata során előforduló gyakori hibákat.
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: pullabhk
ms.openlocfilehash: 221b669c141681749709d6a5a406c78499f21032
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465478"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>SAP HANA-adatbázisok Azure-beli biztonsági mentésének hibáinak megoldása

Ez a cikk a SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentésével kapcsolatos hibaelhárítási információkat tartalmaz.

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

## <a name="common-user-errors"></a>Gyakori felhasználói hibák

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Hibaüzenet | A lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem sikerült csatlakozni a HANA rendszerhez. Ellenőrizze, hogy a rendszer működik-e.| A Azure Backup szolgáltatás nem tud csatlakozni a HANA-hoz, mert a HANA-adatbázis nem működik. Vagy a HANA fut, de nem engedélyezi a Azure Backup szolgáltatás kapcsolódását. | Győződjön meg arról, hogy a HANA-adatbázis vagy-szolgáltatás nem áll le. Ha a HANA-adatbázis vagy-szolgáltatás fut, ellenőrizze, hogy az [összes engedély be](#setting-up-permissions)van-e állítva. Ha a kulcs hiányzik, futtassa újra az előregisztrációi parancsfájlt egy új kulcs létrehozásához. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet | A lehetséges okok | Javasolt művelet |
|---|---|---|
| A rendszer érvénytelen Backint-konfigurációt észlelt. Állítsa le a védelmet, és konfigurálja újra az adatbázist.| A backInt paraméterek helytelenül vannak megadva a Azure Backuphoz. | Győződjön [meg arról, hogy a paraméterek be vannak állítva](#setting-up-backint-parameters). Ha backInt paraméterek találhatók a GAZDAGÉPen, távolítsa el őket. Ha a paraméterek nem találhatók meg a GAZDAGÉP szintjén, de az adatbázis szintjén manuálisan lettek módosítva, állítsa azokat a megfelelő értékekre a korábban leírtak szerint. Vagy futtassa a **védelem leállítása és a biztonsági mentési adatok megőrzése** a Azure Portal, majd válassza a **biztonsági mentés folytatása**lehetőséget.|
