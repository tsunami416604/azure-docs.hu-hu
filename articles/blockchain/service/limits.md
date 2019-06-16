---
title: Az Azure Blockchain-korlátok
description: A szolgáltatás és a működési korlátai Azure Blockchain-Service-ben – áttekintés
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028170"
---
# <a name="limits-in-azure-blockchain-service"></a>Az Azure Blockchain-szolgáltatás korlátozásai

Az Azure Blockchain-szolgáltatás rendelkezik szolgáltatási és működési korlátai például a csomópontok tagja lehet, a consortium korlátozások és a storage összegek számát.

## <a name="pricing-tier"></a>Tarifacsomag

Tranzakcióinak és érvényesítő csomópontok maximális korlátig attól függnek, hogy üzembe helyezi az Azure Blockchain-szolgáltatás alapszintű vagy standard szintű tarifacsomag.

| Tarifacsomag | Tranzakció-csomópontok maximális száma | Érvényesítési csomópontok maximális száma |
|:---|:---:|:---:|
| Alapszintű | 10 | 1 |
| Standard | 10 | 2 |

Között az alapszintű és standard szintű tarifacsomag módosítása után tag létrehozása nem támogatott.

## <a name="storage-capacity"></a>Tárkapacitás

A maximális Főkönyv adatokat és a naplók használható csomópontonként tárterülete 1 terabájt lehet.

Főkönyvi és a naplófájlok tárolási méretének csökkentése nem támogatott.

## <a name="consortium-limits"></a>Consortium korlátok

* **Consortium és tag nevének egyedinek kell lennie** a másik consortium és tag nevéből, az Azure Blockchain-szolgáltatás.

* **Tag és consortium neve nem módosítható.**

* **Minden tag a konzorciumot ugyanabba a tarifacsomagba tartozó kell lennie**

* **Az összes tag konzorcium részt ugyanabban a régióban kell lennie.**

    Az első olyan tag konzorcium létrehozott szabja meg, hogy a régióban. Meghívott tagokat a consortium és az első olyan tag ugyanabban a régióban kell lennie. Minden tag számára ugyanabban a régióban korlátozásával biztosítja, hogy a hálózati konszenzus se legyen negatív hatással.

* **Konzorcium rendelkeznie kell legalább egy rendszergazda**

    Konzorcium csak egy rendszergazda van, ha azok nem magukat a consortium távolítsa el, vagy a tag törlése, amíg egy másik rendszergazda hozzáadásakor vagy a consortium promotálja.

* **Eltávolítja a consortium tagok nem adható hozzá újra**

    Ahelyett hogy kell lehet újból meghívni a consortium csatlakozzon, és hozzon létre egy új tag. A meglévő tag erőforrás segítségével megőrizheti a korábbi tranzakciók nem törlődnek.

* **Konzorcium szereplő összes tag a Főkönyv ugyanazon verzióját kell használnia**

    További információ a javítások, frissítések és Főkönyv verziója elérhető az Azure Blockchain-szolgáltatás: [javítás, frissítések és verziók](ledger-versions.md).

## <a name="next-steps"></a>További lépések

* [Javítások, frissítések és verziók](ledger-versions.md)
