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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027885"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Az Azure Blockchain-szolgáltatás Főkönyv támogatott verziók

Az Azure Blockchain-szolgáltatás használ a Ethereum-alapú [kvórum](https://github.com/jpmorganchase/quorum/wiki) Főkönyv ismert résztvevők azonosította az Azure Blockchain-szolgáltatás konzorcium csoporton belüli privát tranzakciók feldolgozását tervezve.

Jelenleg támogatja az Azure Blockchain-szolgáltatás [kvórum verzió 2.2.1-es](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) és [Tessera tranzakciókezelő](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése

A kvórum Versioning jelentős kiadása, a fő pont kiadás és a egy kisebb pont kiadás történik. Például ha jelentős kiadása a kvórum verzió 2.0.1, 2, 0 fő pont kiadás, pedig 1 kisebb pont kiadás. A szolgáltatás automatikus javítások kisebb pont kiadásaiban a Főkönyv. Frissítése fő- és fő pont kiadásai jelenleg nem támogatott.

## <a name="availability-of-new-ledger-versions"></a>Új Főkönyv verziók rendelkezésre állása

Az Azure Blockchain-szolgáltatás biztosít a legújabb verzióra a Főkönyv legyenek elérhetők a Főkönyv gyártótól után 60 napon belül. Négy fő pont kiadások legfeljebb egy új tag és consortium üzembe helyezésekor választhat konzorcium-okat.

## <a name="next-steps"></a>További lépések

[Az Azure Blockchain-szolgáltatás korlátozásai](limits.md)
