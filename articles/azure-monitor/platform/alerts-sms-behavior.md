---
title: SMS-riasztás viselkedése a műveleti csoportokban
description: SMS-üzenet formátuma és válaszadás az SMS-üzenetek lemondására, előfizetésére vagy a segítség kérésére.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: ff3d055840d69623234a799c62b1d8a6efd96ec5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327089"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS-riasztás viselkedése a műveleti csoportokban

## <a name="overview"></a>Áttekintés 
A műveleti csoportok lehetővé teszik a műveletek listájának konfigurálását. Ezek a csoportok a riasztások definiálásakor használatosak; annak biztosítása, hogy egy adott műveleti csoport értesítést kapjon a riasztás indításakor. Az egyik támogatott művelet az SMS; Az SMS-értesítések támogatják a kétirányú kommunikációt. Egy felhasználó válaszolhat az SMS-re a következőre:

- **Leiratkozás a riasztásokból:** Egy felhasználó lemondhatja az összes olyan SMS-riasztást, amely az összes műveleti csoportra vonatkozóan vagy egyetlen műveleti csoportba kerül.
- **Újrajelentkezés a riasztásokra:** A felhasználók visszafizethetnek minden SMS-riasztásra az összes műveleti csoportra vagy egyetlen műveleti csoportra vonatkozóan.  
- **Segítség kérése:** A felhasználók további információkat kérhetnek az SMS-ről. Ezek átirányítva erre a cikkre.

Ez a cikk az SMS-riasztások viselkedését és a felhasználó által a felhasználó területi beállítása alapján elvégezhető reagálási műveleteket ismerteti.

## <a name="receiving-an-sms-alert"></a>SMS-riasztás fogadása
Egy műveleti csoport részeként konfigurált SMS-fogadó SMS-t kap, ha riasztást vált ki. Az SMS a következő információkat tartalmazza:
* A gazdagépbejegyzés, amelyre a riasztást elküldték
* A riasztás címe

| VÁLASZ | Leírás |
| ----- | ----------- |
| Megbénít `<Action Group Short name>` | Letiltja a további SMS-ket a műveleti csoportból |
| ENGEDÉLYEZÉSE `<Action Group Short name>` | Az SMS újbóli engedélyezése a műveleti csoportból |
| állj | Letiltja a további SMS-ket minden műveleti csoportból |
| START | Az SMS újbóli engedélyezése az összes műveleti csoportból |
| SÚGÓ | A rendszer egy erre a cikkre mutató hivatkozást tartalmazó választ kap a felhasználónak. |

>[!NOTE]
>Ha egy felhasználó leiratkozott az SMS-riasztásokból, de ezt követően új műveleti csoportba kerül. SMS-riasztásokat kapnak az új műveleti csoporthoz, de az összes korábbi műveleti csoportból leiratkozott maradnak.

## <a name="next-steps"></a>Következő lépések
[Tekintse át a tevékenységek naplójának riasztásait](alerts-overview.md) , és Ismerje meg, hogyan kaphat riasztást  
További információ az [SMS-díjak korlátozásáról](alerts-rate-limiting.md)  
További információ a [műveleti csoportokról](./action-groups.md)

