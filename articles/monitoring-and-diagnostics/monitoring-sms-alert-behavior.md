---
title: SMS riasztási viselkedés művelet csoportokban
description: SMS-üzenet formátuma és ad választ az SMS-üzenetek mondhatja, resubscribe, illetve segítséget kérni.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: f2f463f6c428ce6c72e2640472376fa17a2bfe5a
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263007"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS riasztási művelet csoportok működése
## <a name="overview"></a>Áttekintés ##
A művelet csoportok lehetővé teszik a műveletek listájának konfigurálásához. Ezek a csoportok riasztások; meghatározásakor használatosak. Győződjön meg arról, hogy egy adott művelet csoport értesítést kap a riasztás aktiválása. A támogatott műveletek egyike SMS; SMS értesítések kétirányú kommunikáció támogatja. A felhasználó számára az SMS jelenhetnek meg:

- **Riasztások lemondani:** egy felhasználó lehet lemondani összes művelet csoportot, vagy egyetlen művelettel csoport minden SMS-riasztás.
- **A riasztások resubscribe:** A felhasználó az összes művelet csoportot, vagy egyetlen művelettel csoport összes SMS riasztásnál előfordulhat, hogy resubscribe.  
- **Segítség kérése:** A felhasználó további információt az SMS kérheti. Ez a cikk a rendszer visszairányítja.

Ez a cikk ismerteti az SMS-riasztások viselkedését, és a válasz műveletek a felhasználói vehet igénybe alapján a felhasználó területi:

## <a name="receiving-an-sms-alert"></a>SMS riasztást fogadása
Az SMS fogadó egy művelettel csoport tagja SMS kap, ha figyelmeztetés jelenik meg. Az SMS a következő információkat tartalmazza:
* A művelet csoport rövid_név ezt a figyelmeztetést küldött
- A riasztás nevét

| VÁLASZ | Leírás |
| ----- | ----------- |
| DISABLE <Action Group Short name> | Letiltja a további SMS a művelet csoportból |
| ENGEDÉLYEZÉSE <Action Group Short name> | Újra lehetővé teszi, hogy a művelet csoportból SMS |
| ÁLLJ | Letiltja a további SMS művelet minden olyan csoportból |
| INDÍTSA EL | Újból engedélyezi a művelet minden olyan csoportból SMS |
| SÚGÓ | A válasz nem jut hozzá a felhasználó a cikkre mutató hivatkozást. |

>[!NOTE]
>Ha egy felhasználó leiratkozott a SMS riasztást küld, de egy új művelet csoportba kerül RENDSZER SMS figyelmeztetést a művelet új csoport, hanem marad a művelet minden olyan csoportból korábbi művelet.

## <a name="next-steps"></a>További lépések
Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md) és megtudhatja, hogyan küldjön riasztást Önnek  
További információ [SMS sebesség korlátozása](monitoring-alerts-rate-limiting.md)  
További információ [művelet csoportok](monitoring-action-groups.md)
