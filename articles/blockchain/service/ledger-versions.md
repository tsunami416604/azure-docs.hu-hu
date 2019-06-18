---
title: Az Azure Blockchain-szolgáltatás támogatott Főkönyv verziók, javítását és frissítése
description: A támogatott főkönyvek verziók az Azure Blockchain Service, beleértve a házirendek lefektetése rendszerek javításokat és a rendszer által felügyelt és a felhasználó által felügyelt frissítések áttekintése.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399103"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Az Azure Blockchain-szolgáltatás Főkönyv támogatott verziók

Az Azure Blockchain-szolgáltatás használ a Ethereum-alapú [kvórum](https://www.goquorum.com/developers) Főkönyv ismert résztvevők azonosította az Azure Blockchain-szolgáltatás konzorcium csoporton belüli privát tranzakciók feldolgozását tervezve.

Jelenleg támogatja az Azure Blockchain-szolgáltatás [kvórum verzió 2.2.1-es](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) és [Tessera tranzakciókezelő](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése

Kvórum verziószámozást a fő-és alverzió keresztül történik, és a patch-kiadások. Például 2.0.1 kvórum verzió esetén kiadási típus akkor lehet kategóriái a következők:

|Fő | Alverzió  | Javítás  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Az Azure Blockchain-szolgáltatás automatikusan frissíti kvórum javítás verzióiban meglévő futó tagok vált elérhetővé a kvórum számított 30 napon belül.

## <a name="availability-of-new-ledger-versions"></a>Új Főkönyv verziók rendelkezésre állása

Az Azure Blockchain-szolgáltatás biztosítja a legújabb fő- és alverzió verziói a kvórum Főkönyv legyenek elérhetők a kvórum gyártótól után 60 napon belül. Négy kisebb kiadások legfeljebb egy új tag és consortium üzembe helyezésekor választhat konzorcium-okat. Frissíti az egy jelentős vagy kisebb kiadás jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

[Az Azure Blockchain-szolgáltatás korlátozásai](limits.md)
