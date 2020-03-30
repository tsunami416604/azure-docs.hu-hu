---
title: Az Azure Blockchain Service főkönyvi verziói, javítás, & frissítés
description: Az Azure Blockchain Service támogatott főkönyvi verzióinak áttekintése, beleértve a rendszerek javítására, valamint a rendszer által felügyelt és a felhasználó által felügyelt frissítésekre vonatkozó irányelveket.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325190"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Az Azure Blockchain Service támogatott főkönyvi verziói

Az Azure Blockchain Service az Ethereum-alapú [Quorum](https://www.goquorum.com/developers) főkönyvet használja, amelyet az ismert résztvevők egy csoportján belüli magántranzakciók feldolgozására terveztek, és amelyet az Azure Blockchain Szolgáltatás konzorciumaként azonosítottak.

Az Azure Blockchain Service jelenleg támogatja [a Quorum 2.2.3-as verzióját](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) és [a Tessera tranzakciókezelőt.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése

A Quorum verziószámozása egy nagyobb, kisebb és patch kiadáson keresztül történik. Ha például a Quorum verziója 2.0.1, a kiadás típusa a következőképpen lesz kategorizálva:

|Jelentős | Kismérvű  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Az Azure Blockchain Szolgáltatás a Quorum javítási kiadásait a Quorum-ból való elérhetővé tenni 30 napon belül automatikusan frissíti a meglévő futó tagokra.

## <a name="availability-of-new-ledger-versions"></a>Az új főkönyvi verziók elérhetősége

Az Azure Blockchain Service a Kvórum főkönyv legújabb fő- és alverzióit biztosítja a Kvórum gyártójától való elérhetővé tételtől számított 60 napon belül. Egy új tag és konzorcium kiépítésekor legfeljebb négy kisebb kiadás közül választhatnak a konzorciumok. A nagyobb vagy kisebb kiadásra való frissítés jelenleg nem támogatott. Ha például a 2.x verziót futtatja, a 3.x verzióra való frissítés jelenleg nem támogatott. Hasonlóképpen, ha a 2.2-es verziót futtatja, a 2.3-as verzióra való frissítés jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

[Korlátok az Azure Blockchain szolgáltatásban](limits.md)
