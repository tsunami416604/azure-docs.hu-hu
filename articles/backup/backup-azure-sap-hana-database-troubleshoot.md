---
title: SAP HANA-adatbázisok Azure Backup használatával biztonsági mentésekor a hibák elhárítása |} A Microsoft Docs
description: Ez az útmutató ismerteti a biztonsági mentési SAP HANA-adatbázisok Azure Backup használatával tett kísérlet során előforduló gyakori hibák elhárítása.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514183"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Az Azure-beli SAP HANA-kiszolgáló biztonsági mentése hibaelhárítása

Ez a cikk az Azure virtuális gépeken futó SAP HANA-adatbázisok védelmének hibaelhárítási információkat. Mielőtt továbblép a hibaelhárítás, tekintsük át néhány fő szempontot sorolunk kapcsolatos engedélyeit és beállításait.

## <a name="understanding-pre-requisites"></a>Előfeltételek ismertetése

Részeként [Előfeltételek](backup-azure-sap-hana-database.md#prerequisites), az előzetes regisztráció kell a parancsfájlt a virtuális gép a megfelelő engedélyek beállítása a HANA helyéül.

### <a name="setting-up-permissions"></a>Engedélyek beállítása

A regisztráció előtti parancsfájl célja:

1. HANA rendszer AZUREWLBACKUPHANAUSER hoz létre, és hozzáadja a szükséges szerepköröket és engedélyeket az alábbiak:
    - ADATBÁZIS-rendszergazda - visszaállítás során az új adatbázisok létrehozásához
    - KATALÓGUS OLVASÁSI – olvasni a biztonságimásolat-katalógus
    - SAP_INTERNAL_HANA_SUPPORT – néhány privát táblák eléréséhez
2. Kulcs hozzáadása Hdbuserstore HANA beépülő modul ehhez minden művelet (adatbázis-biztonsági mentés konfigurálása, a biztonsági mentéshez, a visszaállítás során lekérdezés)
   
   - Ellenőrizze a kulcs létrehozása, a parancsot HDBSQL a HANA-gépen SIDADM hitelesítő adatokkal:

    ``` hdbsql
    hdbuserstore list
    ```
    
    A parancs kimenete kell jelenít meg a kulcsot a felhasználó {SID} {%{DBNAME/} "AZUREWLBACKUPHANAUSER".

> [!NOTE]
> Ellenőrizze, hogy az elérési út alatt SSFS fájlok egyedi készletének "/ usr/sap/{SID}/home/.hdb/". Csak egy mappát az elérési út alatt kell lennie.

### <a name="setting-up-backint-parameters"></a>BackInt paraméterek beállítása

Csak egy adatbázis biztonsági mentésének ki kell választania, az Azure Backup szolgáltatás konfigurál az adatbázis szintjén backInt paramétereket.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Ellenőrizze, hogy ezek a paraméterek nincsenek jelen a GAZDAGÉP szintjén. Gazdagép szinten paraméterek felülírja ezeket a paramétereket, és eltérő viselkedést okozhat a vártnál.

## <a name="understanding-common-user-errors"></a>A gyakori felhasználói hibák ismertetése

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem sikerült csatlakozni a rendszer működik és HANA system.check.| Az Azure Backup szolgáltatás nem sikerül csatlakozni a Hana-hoz, mert a HANA-adatbázis nem működik. Vagy a HANA fut, de nem engedélyezi az Azure Backup szolgáltatással való csatlakozáshoz | Ellenőrizze, hogy a HANA-DB/szolgáltatás le-e. Ha a HANA-adatbázis vagy-szolgáltatás működik és elérhető, ellenőrizze, ha minden engedély beállítása leírtaknak [Itt](#setting-up-permissions). A kulcs hiányzik, futtassa újra az új kulcs létrehozása előtti regisztrációs szkriptet. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Észlelt érvénytelen Backint konfigurációja. Állítsa le a védelmet, és konfigurálja újra az adatbázis.| A backInt paraméterek helytelenül vannak megadva az Azure Backup szolgáltatáshoz. | Ellenőrizze a paraméterek nem említett [Itt](#setting-up-backint-parameters). Ha a GAZDAGÉP alapú backInt paraméterek szerepelnek, majd távolítsa el őket. Ha paraméterek nem jelenik meg a GAZDAGÉP, de manuálisan módosították egy adatbázis szintjén, majd állítsa vissza őket a megfelelő értékekre fent említett módon. Vagy a "védelem leállítása és adatok megőrzése" az Azure portal és a "biztonsági mentés folytatásához" még egyszer.|
