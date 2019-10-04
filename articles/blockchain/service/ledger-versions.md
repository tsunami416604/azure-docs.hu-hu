---
title: Az Azure Blockchain szolgáltatás által támogatott Főkönyv verziója, javítása és frissítése
description: Az Azure Blockchain szolgáltatás támogatott főkönyvek verzióinak áttekintése, beleértve a rendszerek javításával, a rendszer által felügyelt és a felhasználó által felügyelt frissítésekkel kapcsolatos házirendeket is.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 6cded49f14ae90f84b18c0a37059677c6c1fed99
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562786"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Támogatott Azure Blockchain Service Ledger-verziók

Az Azure Blockchain szolgáltatás az Azure Blockchain Service [](https://www.goquorum.com/developers) -ben konzorciumként azonosított Ethereum-alapú kvórumot használ, amely az ismert résztvevők egy csoportján belüli privát tranzakciók feldolgozására lett tervezve.

Az Azure Blockchain szolgáltatás jelenleg támogatja a [kvórum Version 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) és [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera)verzióját.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése

A kvórumon belüli verziószámozás a fő, a másodlagos és a javítási kiadásokon keresztül történik. Ha például a kvórum verziója 2.0.1, a kiadás típusa a következőképpen lesz kategorizálva:

|Fő | Alverzió  | Javítás  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Az Azure Blockchain szolgáltatás a kvórumról elérhetővé tett 30 napon belül automatikusan frissíti a kvórum javítását a meglévő futó tagoknak.

## <a name="availability-of-new-ledger-versions"></a>Új Főkönyv-verziók rendelkezésre állása

Az Azure Blockchain szolgáltatás a kvórum főkönyvének legújabb fő és alverzióit a kvórum gyártójától 60 napon belül elérhetővé teszi. Egy új tag és konzorcium kiépítés esetén legfeljebb négy kisebb kiadást biztosítunk a konzorciumok számára. A verzióról a verzióra való verziófrissítés jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

[Korlátok az Azure Blockchain szolgáltatásban](limits.md)
