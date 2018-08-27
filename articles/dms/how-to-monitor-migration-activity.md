---
title: Az áttelepítési tevékenység figyelése az Azure Database Migration Service segítségével |} A Microsoft Docs
description: Ismerje meg az áttelepítési tevékenység figyelése az Azure Database Migration Service használatával.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: e2ed45d9b87945247a3a4a4cfc58b4beb2353b10
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889565"
---
# <a name="monitoring-migration-activity"></a>Az áttelepítési tevékenység figyelése
Ebből a cikkből elsajátíthatja egy adatbázisszintű és a egy tábla szintjén egyaránt áttelepítés előrehaladásának figyeléséhez.

## <a name="monitoring-activity-at-the-database-level"></a>Az adatbázis szintjén tevékenységek figyelése
Az adatbázis szintjén figyelése, tekintse meg az adatbázisszintű panelen:

![Adatbázisszintű panel](media\how-to-monitor-migration-activity\dms-database-level-blade.png)

> [!NOTE]
> Az adatbázis-hivatkozás kijelölésével bemutatják, a táblákat és az áttelepítési folyamat listáját.

Az alábbi táblázat az adatbázisszintű panelen lévő mezők listája, és ismerteti a különböző állapot értékei az egyes társított.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Mező neve</strong></th>
      <th><strong>A mező részállapot</strong></th>
      <th><strong>Leírás</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus">A tevékenység állapota</td>
      <td>Fut</td>
      <td>Az áttelepítési tevékenység fut.</td>
    </tr>
    <tr>
      <td>Sikeres</td>
      <td>Az áttelepítési tevékenység sikeres problémák nélkül.</td>
    </tr>
    <tr>
      <td>Meghibásodott</td>
      <td>Az áttelepítés sikertelen volt. A "Lásd a hiba részletei" hivatkozásra a teljes hibaüzenetet a migrálás részletei alapján.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status">status</td>
      <td>Inicializálás</td>
      <td>A DMS a migrálási folyamat beállítását.</td>
    </tr>
    <tr>
      <td>Fut</td>
      <td>A DMS folyamat fut, és áttelepítés.</td>
    </tr>
    <tr>
      <td>Befejezve</td>
      <td>Migrálás befejezve.</td>
    </tr>
    <tr>
      <td>Meghiúsult</td>
      <td>Az áttelepítés sikertelen volt. Kattintson a migrálás részleteit megtekintheti, migrálási hibák.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details">Migrálás részletei</td>
      <td>A migrálási folyamat inicializálása</td>
      <td>A DMS a migrálási folyamat beállítását.</td>
    </tr>
    <tr>
      <td>Adatok teljes betöltése folyamatban</td>
      <td>A DMS kezdeti betöltés végez.</td>
    </tr>
    <tr>
      <td>Készen áll a megoldásról</td>
      <td>A rendszer kezdeti betöltés után DMS lezárásával befejezettként jelöli meg adatbázis-készen áll a megoldásról. Felhasználói ellenőrizni kell, ha adatok már szerepelnek a folyamatos szinkronizálás.</td>
    </tr>
    <tr>
      <td>Az összes módosítás alkalmazva</td>
      <td>Kezdeti betöltési és a folyamatos szinkronizálás is befejeződött. Ez az állapot akkor is jelentkezik, után az adatbázis sikeres átállás.</td>
    </tr>
    <tr>
      <td>Lásd a hiba részleteit</td>
      <td>Kattintson a hivatkozásra a hiba részleteinek megjelenítése.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration">Időtartam</td>
      <td>–</td>
      <td>Teljes idő a migrálási tevékenység inicializálása közben az áttelepítés befejeződött, vagy áttelepítés hibát jelzett.</td>
    </tr>
     </tbody>
</table>

## <a name="monitoring-migration-activity-at-table-level--quick-summary"></a>Tábla szintjén – rövid összefoglalás az áttelepítési tevékenység figyelése
A tábla szintjén figyelése, tekintse meg a táblaszintű panel. A panel felső részén látható a teljes terhelés és a növekményes frissítéseket át a részletes sorok száma. 

A panel alsó részén felsorolja a táblákat, és az áttelepítési folyamat gyors összegzését jeleníti meg.

![Táblaszintű panel – rövid összefoglalás](media\how-to-monitor-migration-activity\dms-table-level-blade-summary.png)

A következő táblázat ismerteti a mezőket a táblaszintű részletei között látható.

| Mező neve        | Leírás       |
| ------------- | ------------- |
| A teljes terhelés befejeződött      | Táblák száma az adatok teljes betöltése befejeződött. |
| Teljes terhelés várólistán van      | A teljes terhelés céljából várólistára helyezett táblák száma.      |
| Teljes terhelés betöltése folyamatban van | Nem sikerült a táblák száma.      |
| Növekményes bővítések      | Adatváltozások száma rögzítése (CDC) frissítések a alkalmazni a cél sorok. |
| Növekményes beszúrások      | CDC száma a alkalmazni a cél sorok beszúrása.      |
| Növekményes törlések | CDC száma a alkalmazni a cél sorok törlése.      |
| Függőben lévő módosítások      | CDC váró továbbra is érvényben sorok számát a cél. |
| Alkalmazott módosítások      | CDC összesen frissíti, a beszúrások, és a alkalmazni a cél sorok törlése.      |
| Hibás állapotú táblák | "Hiba" állapotban van, a migrálás során táblák számát. Néhány példa, amelyek táblákat is hibás állapotú akkor, ha a cél az azonosított ismétlődések vannak, vagy az adatok nem kompatibilis a céloldali tábla betöltése.      |

## <a name="monitoring-migration-activity-at-table-level--detailed-summary"></a>Tábla szintjén – részletes összegzése az áttelepítési tevékenység figyelése
Nincsenek azt mutatják be, a teljes terhelés és az adatok növekményes szinkronizálása az áttelepítési folyamat két lap található.
    
![A teljes terhelés lap](media\how-to-monitor-migration-activity\dms-full-load-tab.png)

![Adatok növekményes szinkronizálás lap](media\how-to-monitor-migration-activity\dms-incremental-data-sync-tab.png)

A következő táblázat ismerteti a táblázat szintű áttelepítési folyamatban lévő mezők.

| Mező neve        | Leírás       |
| ------------- | ------------- |
| Állapot – szinkronizálása      | A folyamatos szinkronizálás fut. |
| Beszúrás      | CDC száma a alkalmazni a cél sorok beszúrása.      |
| Frissítés | CDC-frissítések a alkalmazni a cél sorok száma.      |
| Törlés      | CDC száma a alkalmazni a cél sorok törlése. |
| Alkalmazva összesen      | CDC összesen frissíti, a beszúrások, és a alkalmazni a cél sorok törlése. |
| Adathibák | Az adatok hibák száma ebben a táblában történt. Néhány példa a hibák *511: %d Ez meghaladja a megengedett maximális sorméretet % d, 8114 méretű sor nem hozható létre: Hiba történt a(z) %ls adattípus %ls.*  Ügyfél kérdezze le a hiba részleteinek megtekintéséhez az Azure cél attms_apply_exceptions táblából.    |

> [!NOTE]
> CDC-értékek az Insert, Update és Delete és a teljes alkalmazott lecsökkenhet adatbázis átállás vagy áttelepítés újraindul.

## <a name="next-steps"></a>További lépések
- Tekintse át a migrálási útmutató segítséget nyújt a Microsoft [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).