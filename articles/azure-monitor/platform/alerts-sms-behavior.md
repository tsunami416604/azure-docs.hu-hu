---
title: Műveletcsoportok az SMS-riasztás viselkedése
description: Az SMS-üzenet formátuma és a válaszadás le szeretne iratkozni, az SMS-üzenetek a feliratkozásra, vagy segítséget kérni.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 85480a098dc5ef70938a5ec4cd7a31105496df6f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461400"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS-riasztás Műveletcsoportok viselkedés
## <a name="overview"></a>Áttekintés ##
Műveletcsoportok lehetővé teszi azon műveletek listáját konfigurálása. A csoportok használatban vannak, riasztások; meghatározásakor annak ellenőrzése, hogy egy adott műveletcsoport a riasztás akkor aktiválódik, amikor értesítést kap. A támogatott műveletek egyike SMS; SMS-értesítések és a kétirányú kommunikáció támogatásához. A felhasználó válaszolhatnak egy SMS-t:

- **Leiratkozik a figyelmeztetésekről:** A felhasználó az összes Műveletcsoportok, vagy egyetlen művelettel csoport minden SMS-riasztás leiratkozhat.
- **Feliratkozásra riasztások:** A felhasználó minden SMS-riasztás minden Műveletcsoportok, vagy egyetlen művelettel csoport, előfordulhat, hogy feliratkozásra.  
- **Segítség kérése:** További információ az SMS kérhet egy felhasználó. Ez a cikk a rendszer átirányítja.

Ez a cikk ismerteti az SMS-riasztások viselkedésének és a válasz műveleteket a felhasználó alapján a felhasználó területi:

## <a name="receiving-an-sms-alert"></a>Az SMS-riasztás fogadásakor
Az SMS-fogadó műveletcsoport tagja SMS kap, amikor a riasztás aktiválódik. Az SMS a következő információkat tartalmazza:
* A műveletcsoport küldte el ezt a riasztást a gazdagépnév
- A riasztás címe

| VÁLASZ | Leírás |
| ----- | ----------- |
| DISABLE <Action Group Short name> | Letiltja a további SMS-a műveletcsoport |
| ENGEDÉLYEZÉSE <Action Group Short name> | Újra lehetővé teszi, hogy a műveleti csoporthoz SMS |
| ÁLLJ | Letiltja a további SMS-minden Műveletcsoportok |
| INDÍTSA EL | Újból engedélyezi az SMS-minden Műveletcsoportok |
| SEGÍTSÉG | Ez a cikk mutató hivatkozást tartalmazó válasz a felhasználó érkezik. |

>[!NOTE]
>Ha a felhasználó leiratkozott rendelkezik SMS riasztást küld, de majd adnak hozzá egy új műveletcsoportot; FOG számára, hogy új műveletcsoport SMS-értesítést kapni, hanem továbbra is az összes korábbi Műveletcsoportok leiratkozott.

## <a name="next-steps"></a>További lépések
Get- [tevékenységnapló-riasztások áttekintése](alerts-overview.md) , és megtudhatja, hogyan riasztások  
Tudjon meg többet [SMS arány korlátozása](alerts-rate-limiting.md)  
Tudjon meg többet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md)

