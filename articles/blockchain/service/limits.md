---
title: Az Azure Blockchain szolgáltatás korlátai
description: A szolgáltatás és a működési korlátok áttekintése az Azure Blockchain szolgáltatásban
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455656"
---
# <a name="limits-in-azure-blockchain-service"></a>Korlátok az Azure Blockchain szolgáltatásban

Az Azure Blockchain szolgáltatás rendelkezik olyan szolgáltatás-és működési korlátokkal, mint például egy tag csomópontjainak száma, a konzorcium korlátozásai és a tárterület mennyisége.

## <a name="pricing-tier"></a>Tarifacsomag

A tranzakciók és az érvényesítő csomópontok maximális korlátai attól függnek, hogy az Azure Blockchain szolgáltatást alapszintű vagy standard szintű díjszabással kívánja-e kiépíteni.

| Tarifacsomag | Tranzakciós csomópontok maximális száma | Érvényesítő csomópontok maximális száma |
|:---|:---:|:---:|
| Alapszintű | 10 | 1 |
| Standard | 10 | 2 |

Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

## <a name="storage-capacity"></a>Tárkapacitás

A Főkönyv és a naplók számára a csomópontok által használható maximális tárterület 1,8 terabájt.

A Főkönyv és a napló tárolási méretének csökkentése nem támogatott.

## <a name="consortium-limits"></a>Konzorcium korlátai

* A **konzorcium és a tagok nevének egyedinek kell lennie** az Azure Blockchain szolgáltatásban található többi konzorciumtól és tag nevétől.

* **A tag és a konzorcium neve nem módosítható**

* **A konzorcium összes tagjának ugyanabban az árképzési szinten kell lennie**

* **A konzorciumban részt vevő összes tagnak ugyanabban a régióban kell lennie**

    A konzorciumban létrehozott első tag diktálja a régiót. A konzorciumnak meghívott tagoknak ugyanabban a régióban kell lenniük, mint az első taggal. Ha egyetlen régióra korlátozza az összes tagot, azzal biztosítható, hogy a hálózati konszenzus ne legyen negatív hatással.

* **A konzorciumnak rendelkeznie kell legalább egy rendszergazdával**

    Ha a konzorciumban csak egy rendszergazda található, akkor nem távolíthatja el magukat a konzorciumból, vagy törölhetik a tagjaikat, amíg egy másik rendszergazdát nem ad hozzá vagy nem előléptet a konzorciumban.

* **A konzorciumból eltávolított tagok nem vehetők fel újra**

    Ehelyett újra kell hívni őket a konzorciumhoz való csatlakozáshoz és egy új tag létrehozásához. A meglévő tag-erőforrás nem törlődik a korábbi tranzakciók megőrzése érdekében.

* **A konzorcium összes tagjának ugyanazt a Főkönyv-verziót kell használnia.**

    Az Azure Blockchain szolgáltatásban elérhető javításokkal, frissítésekkel és Főkönyv verziókkal kapcsolatos további információkért lásd: [javítások, frissítések és verziók](ledger-versions.md).

## <a name="next-steps"></a>Következő lépések

További információ a rendszerek javításával és frissítésével kapcsolatos házirendekről – [javítások, frissítések és verziók](ledger-versions.md).
