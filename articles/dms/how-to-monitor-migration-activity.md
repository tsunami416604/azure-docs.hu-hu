---
title: Áttelepítési tevékenység figyelése – Azure-adatbázis-áttelepítési szolgáltatás
description: Ismerje meg, hogyan használhatja az Azure Database Migration Service az áttelepítési tevékenység figyeléséhez.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648512"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Áttelepítési tevékenység figyelése az Azure-adatbázis-áttelepítési szolgáltatás használatával
Ebből a cikkből megtudhatja, hogyan figyelheti az áttelepítés előrehaladását adatbázis- és táblaszinten egyaránt.

## <a name="monitor-at-the-database-level"></a>Figyelő adatbázis szinten
A tevékenység adatbázisszintű figyeléséhez tekintse meg az adatbázisszintű panelt:

![Adatbázisszintű panel](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Az adatbázis hivatkozásának kiválasztásával megjelenik a táblák listája és áttelepítési folyamata.

Az alábbi táblázat az adatbázisszintű panel mezőit sorolja fel, és ismerteti az egyes állapotértékeket.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Mezőnév</strong></th>
      <th><strong>Mező alállapota</strong></th>
      <th><strong>Leírás</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Tevékenység állapota</strong></td>
      <td>Fut</td>
      <td>Az áttelepítési tevékenység fut.</td>
    </tr>
    <tr>
      <td>Sikeres</td>
      <td>Az áttelepítési tevékenység problémamentesen sikerült.</td>
    </tr>
    <tr>
      <td>Hibás</td>
      <td>Az áttelepítés nem sikerült. A teljes hibaüzenethez az áttelepítés részletei között válassza a "Hibarészleteinek megtekintése" hivatkozást.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Állapot</strong></td>
      <td>Inicializálás</td>
      <td>A DMS az áttelepítési folyamatot állítja be.</td>
    </tr>
    <tr>
      <td>Fut</td>
      <td>A DMS-folyamat fut és áttelepítést hajt végre.</td>
    </tr>
    <tr>
      <td>Befejezve</td>
      <td>Az áttelepítés befejeződött.</td>
    </tr>
    <tr>
      <td>Sikertelen</td>
      <td>Az áttelepítés nem sikerült. Az áttelepítési részletek megtekintéséhez kattintson az áttelepítési részletekre.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>A migrálás részletei</strong></td>
      <td>Az áttelepítési folyamat megtervezése</td>
      <td>A DMS az áttelepítési folyamatot állítja be.</td>
    </tr>
    <tr>
      <td>Teljes adatterhelés folyamatban</td>
      <td>A DMS kezdeti terhelést hajt végre.</td>
    </tr>
    <tr>
      <td>Készen áll az átvágásra</td>
      <td>A kezdeti betöltés befejezése után a DMS az adatbázist az átvágásra készként jelöli meg. A felhasználónak ellenőriznie kell, hogy az adatok felzárkóztak-e a folyamatos szinkronizáláshoz.</td>
    </tr>
    <tr>
      <td>Minden alkalmazott módosítás</td>
      <td>A kezdeti terhelés és a folyamatos szinkronizálás befejeződött. Ez az állapot az adatbázis sikeres kivágása után is bekövetkezik.</td>
    </tr>
    <tr>
      <td>Lásd a hiba részleteit</td>
      <td>Kattintson a linkre a hiba részleteinek megjelenítéséhez.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Időtartam</strong></td>
      <td>N/A</td>
      <td>Az inicializált áttelepítési tevékenységtől az áttelepítés befejezéséig vagy az áttelepítés hibássá történő teljes ideje.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor táblázat szinten – Gyors összegzés
A tevékenység táblaszinten való figyeléséhez tekintse meg a táblaszintű panelt. A panel felső része a teljes terhelésben áttelepített sorok részletes számát és a növekményes frissítéseket mutatja. 

A panel alsó része felsorolja a táblázatokat, és megjeleníti az áttelepítés előrehaladásának gyors összegzését.

![Táblázatszintű penge - gyors összegzés](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

Az alábbi táblázat a táblaszintű részletekben látható mezőket ismerteti.

| Mező neve        | Leírás       |
| ------------- | ------------- |
| **Teljes terhelés befejeződött**      | A teljes adatterheléssel teljesített táblák száma. |
| **Teljes terhelés várólistára kerül**      | A teljes terheléshez várólistára helyezett táblák száma.      |
| **Teljes terhelés** | A táblák száma nem sikerült.      |
| **Növekményes frissítések**      | A változási adatrögzítési (CDC) frissítések száma a célra alkalmazott sorokban. |
| **Növekményes lapkák**      | Cdc beszúrások száma a célra alkalmazott sorokban.      |
| **Növekményes törlések** | Cdc törlések száma a célra alkalmazott sorokban.      |
| **Függőben lévő módosítások**      | Cdc száma sorokban, amelyek még mindig arra várnak, hogy alkalmazzák a cél. |
| **Alkalmazott módosítások**      | Cdc-frissítések, beszúrások és törlések száma a célra alkalmazott sorokban.      |
| **Hibás állapotban lévő táblák** | Az áttelepítés során "hiba" állapotban lévő táblák száma. Néhány példa, hogy a táblák hibaállapotba léphetnek, ha vannak ismétlődések azonosított a cél vagy az adatok nem kompatibilis betöltése a céltáblában.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor táblázat szinten – Részletes összegzés
Két lap mutatja az áttelepítés előrehaladását a Teljes terhelés és a Növekményes adatszinkronizálás ban.
    
![Teljes betöltés lap](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Növekményes adatszinkronizálási lap](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

Az alábbi táblázat a táblaszintű áttelepítés folyamatában látható mezőket ismerteti.

| Mező neve        | Leírás       |
| ------------- | ------------- |
| **Állapot - Szinkronizálás**      | Folyamatos szinkronizálás fut. |
| **Beszúrása**      | Cdc beszúrások száma a célra alkalmazott sorokban.      |
| **Frissítés** | Cdc frissítések száma a célra alkalmazott sorokban.      |
| **Szabályzat**      | Cdc törlések száma a célra alkalmazott sorokban. |
| **Alkalmazott összesen**      | Cdc-frissítések, beszúrások és törlések száma a célra alkalmazott sorokban. |
| **Adathibák** | Adathibák száma történt ebben a táblában. Néhány példa a következő példákra: *A(z) %d méretű sor nem hozható létre, amely nagyobb, mint a(z) %d megengedett maximális sormérete: 8114: Hiba a(z) %ls adattípus %ls-re történő konvertálásasorán.*  Az ügyfélnek le kell kérdeznie dms_apply_exceptions táblából az Azure-célban a hiba részleteinek megtekintéséhez.    |

> [!NOTE]
> A Beszúrás, frissítés és törlés, valamint az Alkalmazott teljes összeg CDC-értékei csökkenhetnek az adatbázis átállása vagy áttelepítése esetén.

## <a name="next-steps"></a>További lépések
- Tekintse át az áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatójában.](https://datamigration.microsoft.com/)