---
title: "SMS riasztási viselkedésnek a művelet csoportok |} Microsoft Docs"
description: "SMS-üzenet formátuma és ad választ az SMS-üzenetek mondhatja, resubscribe, illetve segítséget kérni."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 012f001356463a8a7d9b95f186111959627f2c28
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS riasztási művelet csoportok működése
## <a name="overview"></a>Áttekintés ##
A művelet csoportok lehetővé teszik a fogadók listájának konfigurálásához. Ezek a csoportok majd kihasználható, napló tevékenységriasztásokat; meghatározásakor Győződjön meg arról, hogy egy adott művelet csoport értesítést kaphat a tevékenység napló figyelmeztetés jelenik meg. A támogatott riasztási mechanizmus egyik SMS; a riasztások kétirányú kommunikáció támogatja. A felhasználók válaszolhassanak riasztást:

- **Riasztások lemondani:** A felhasználó az összes művelet csoportot, vagy egyes számú művelet csoport összes SMS riasztásokból lemondhatja.  
- **A riasztások resubscribe:** A felhasználó az összes művelet csoportot, vagy egyes számú művelet csoport összes SMS riasztásnál is resubscribe.  
- **Segítség kérése:** A felhasználó további információt az SMS teheti fel. A rendszer átirányítja a cikkhez

Ez a cikk ismerteti az SMS-riasztások viselkedését, és a válasz műveletek a felhasználói vehet igénybe alapján a felhasználó területi:

## <a name="usacanada-sms-behavior"></a>Amerikai Egyesült Államok vagy Kanada SMS viselkedése
### <a name="receiving-an-sms-alert"></a>SMS riasztást fogadása
Az SMS fogadó, akik egy művelet csoport részeként van konfigurálva, az SMS kap, ha a riasztás akkor következik be. Az SMS fogja hajtani a következő információkat:
* A művelet csoport rövid_név ezt a figyelmeztetést küldött
- A riasztás nevét

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>Az SMS riasztásokból egy művelet csoport unsubscribing
A felhasználó leiratkozhat SMS az értesítések egy-egy művelettel csoport által a kulcsszavak a rövid kódot 29873 válaszol: "LETILTÁSA &lt;művelet csoport rövid_név&gt;".

Példa Egy művelet csoport a rövid_név "Azure", a riasztások lemondani kívánó felhasználó SMS elküldése a rövid kód 29873, amely szerint "LETILTÁSA Azure"

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Az SMS riasztásokból az összes művelet csoport unsubscribing
A felhasználó a rövid kód a következő kulcsszavak egyikét 29873 válaszol leiratkozhat az összes művelet csoport összes SMS riasztás:
* ÁLLJ

Példa A felhasználó az összes művelet csoport összes SMS riasztás lemondani kívánó volna SMS küldése a rövid kódot 29873, amely szerint a "STOP"

>[!NOTE]
>Ha egy felhasználó leiratkozott a SMS riasztást küld, de egy új művelet csoportba kerül RENDSZER SMS figyelmeztetést a művelet új csoport, hanem marad a művelet minden olyan csoportból korábbi művelet.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>A művelet egy csoport SMS riasztásokat resubscribing
A felhasználó is resubscribe SMS az értesítések egy-egy művelettel csoport által a kulcsszavak a rövid kódot 29873 válaszol: "engedélyezése &lt;művelet csoport rövid_név&gt;".

Példa A riasztásokat a rövid_név "Azure", a művelet csoportok resubscribe kívánó felhasználó SMS elküldése a rövid kód 29873, amely szerint "Azure engedélyezése"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Az összes művelet csoport SMS riasztás resubscribing
A felhasználó által a rövid kód a következő kulcsszavak egyikét 29873 válaszol a riasztások az összes művelet csoport összes SMS is resubscribe:

* INDÍTSA EL

Példa A felhasználó az összes művelet csoport összes SMS riasztás lemondani kívánó volna SMS küldése a rövid kódot 29873, amely szerint a "START"

### <a name="requesting-help-via-sms"></a>SMS segítségkérés
A felhasználó további információt az SMS válaszol a rövid kód a következő kulcsszavak egyikét 29873 kaptak teheti fel:
* SÚGÓ

Választ a felhasználó egy hivatkozás, ez a cikk kapnak.

## <a name="next-steps"></a>Következő lépések
Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md) és megtudhatja, hogyan küldjön riasztást Önnek  
További információ [SMS sebesség korlátozása](monitoring-alerts-rate-limiting.md)  
További információ [művelet csoportok](monitoring-action-groups.md)
