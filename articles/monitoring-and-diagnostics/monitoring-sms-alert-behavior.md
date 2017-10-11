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
ms.openlocfilehash: 3e4eca174209eeb9cbce1d45111d1e5cc30af8b0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
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
A felhasználó leiratkozhat SMS az értesítések egy-egy művelettel csoport által a shortcode 20873 a kulcsszavakkal válaszol: "LETILTÁSA &lt;művelet csoport rövid_név&gt;".

Példa Egy művelet csoport a rövid_név "Azure", a riasztások lemondani kívánó felhasználó SMS, amely szerint "Azure tiltsa le a" shortcode 20873 volna küldése

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Az SMS riasztásokból az összes művelet csoport unsubscribing
A felhasználó válaszol a shortcode 20873 a következő kulcsszavak egyikét a leiratkozhat az összes művelet csoport összes SMS riasztás:
* ÁLLJ

Példa A felhasználó az összes művelet csoport összes SMS riasztás lemondani kívánó, amely szerint a "STOP" shortcode 20873 SMS volna küldése

>[!NOTE]
>Ha egy felhasználó leiratkozott a SMS riasztást küld, de egy új művelet csoportba kerül RENDSZER SMS figyelmeztetést a művelet új csoport, hanem marad a művelet minden olyan csoportból korábbi művelet.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>A művelet egy csoport SMS riasztásokat resubscribing
A felhasználó is resubscribe SMS az értesítések egy-egy művelettel csoport által a shortcode 20873 a kulcsszavakkal válaszol: "engedélyezése &lt;művelet csoport rövid_név&gt;".

Példa A riasztásokat a rövid_név "Azure", a művelet csoportok resubscribe kívánó felhasználó volna SMS küldése a shortcode 20873, amely szerint "Azure engedélyezése"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Az összes művelet csoport SMS riasztás resubscribing
A felhasználó által válaszol a shortcode 20873 a következő kulcsszavak egyikét a riasztások az összes művelet csoport összes SMS is resubscribe:

* INDÍTSA EL

Példa A felhasználó az összes művelet csoport összes SMS riasztás lemondani kívánó, amely szerint a "START" shortcode 20873 SMS volna küldése

### <a name="requesting-help-via-sms"></a>SMS segítségkérés
A felhasználó további információt az SMS válaszol a shortcode 20873 a következő kulcsszavak egyikét a kaptak teheti fel:
* SEGÍTSÉG

Választ a felhasználó egy hivatkozás, ez a cikk kapnak.

## <a name="next-steps"></a>Következő lépések
Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md) és megtudhatja, hogyan küldjön riasztást Önnek  
További információ [SMS sebesség korlátozása](monitoring-alerts-rate-limiting.md)  
További információ [művelet csoportok](monitoring-action-groups.md)
