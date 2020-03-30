---
title: SMS-riasztások viselkedése a műveletcsoportokban
description: SMS-üzenet formátumban, és válaszol az SMS-üzenetekleiratkozáshoz, újrairatkozáshoz vagy segítségkéréshez.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665306"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS-riasztások viselkedése műveletcsoportokban

## <a name="overview"></a>Áttekintés 
A műveletcsoportok lehetővé teszik a műveletek listájának konfigurálását. Ezek a csoportok a riasztások meghatározásakor használatosak; annak biztosítása, hogy egy adott műveletcsoportot értesítsenek a riasztás aktiválásáról. Az egyik támogatott művelet az SMS; Az SMS-értesítések támogatják a kétirányú kommunikációt. A felhasználó sms-ben válaszolhat:

- **Leiratkozás a riasztásokról:** A felhasználó leiratkozhat az összes műveletcsoportra vonatkozó összes SMS-értesítésről, vagy egyetlen műveletcsoportról.
- **Feliratkozás a riasztásokra:** A felhasználó újra feliratkozhat az összes műveletcsoport összes SMS-riasztására, vagy egyetlen műveletcsoportra.  
- **Kérjen segítséget:** A felhasználó további információt kérhet az SMS-ről. A cikkre átlesznek irányítva.

Ez a cikk az SMS-riasztások viselkedését és a felhasználó által a felhasználó területi beállításának alapján végrehajtható válaszműveleteket ismerteti:

## <a name="receiving-an-sms-alert"></a>SMS-értesítés fogadása
A műveletcsoport részeként konfigurált SMS-fogadó sms-t kap, amikor egy riasztás aktiválódik. Az SMS a következő információkat tartalmazza:
* Annak a műveletcsoportnak a rövid neve, amelynek a riasztást küldték
* A figyelmeztető jelzés címe

| VÁLASZ | Leírás |
| ----- | ----------- |
| Megbénít`<Action Group Short name>` | Letiltja a további SMS-t a műveletcsoportból |
| Engedélyezi`<Action Group Short name>` | Az SMS újbóli engedélyezése a műveletcsoporttól |
| állj | Letiltja a további SMS-t az összes műveletcsoportból |
| Elkezd | Sms újbóli engedélyezése az ÖSSZES műveletcsoportból |
| SÚGÓ | A rendszer választ küld a felhasználónak a cikkre mutató hivatkozással. |

>[!NOTE]
>Ha egy felhasználó leiratkozott az SMS-értesítésekről, de ezután hozzáadódik egy új műveletcsoporthoz; sms-értesítéseket kapnak az adott új akciócsoportról, de az összes korábbi akciócsoportból leiratkoztak.

## <a name="next-steps"></a>Következő lépések
Áttekintés [a tevékenységnapló-riasztásokról,](alerts-overview.md) és megtudhatja, hogyan kaphat értesítést  
További információ az [SMS-sebesség korlátozásáról](alerts-rate-limiting.md)  
További információ a [műveletcsoportokról](../../azure-monitor/platform/action-groups.md)

