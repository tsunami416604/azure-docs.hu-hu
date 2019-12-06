---
title: Több főkiszolgálós előnyök Azure Cosmos DB
description: Ismerje meg a Azure Cosmos DB több főkiszolgálójának előnyeit, valamint a késés és az SLA-követelmények összehasonlítását egyetlen és több írási helyen.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872009"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>A több főkiszolgálós előnyök megismerése Azure Cosmos DB

Cosmos DB a Fiókfelelősök a megfelelő globális terjesztési konfiguráció kiválasztásával biztosítják az alkalmazásai késési, rendelkezésre állási és RTO követelményeit. A több írási hellyel konfigurált Azure Cosmos-fiókok jelentős előnyt biztosítanak az olyan fiókokhoz, mint például a 99,999%-os írási rendelkezésre állási SLA, < 10 MS írási késés SLA a esetek 99% percentilis és a RTO = 0 esetében regionális katasztrófa esetén.

## <a name="comparison-of-features"></a>Funkciók összehasonlítása

|Alkalmazásra vonatkozó követelmény|Több írási hely|Egyszeri írási hely|Megjegyzés|
|---|---|---|---|
|< 10ms írási késleltetése (SLA) a következő helyen: P99|**Igen**|Nem|Az egyszeri írási hellyel rendelkező fiókok esetében az egyes írásokhoz további régiók közötti hálózati késések merülhetnek fel.|
|< 10ms olvasási késése (SLA) a következő helyen: P99|**Igen**|Igen| |
|99,999%-os SLA-írás|**Igen**|Nem|Egyszeri írási hellyel rendelkező fiókok, 99,99%-os SLA-val|
|RTO = 0|**Igen**|Nem|A regionális katasztrófák esetén az írási idő nulla. Az egyszeri írási hellyel rendelkező fiókok 15 perces RTO rendelkeznek.|

## <a name="next-steps"></a>Következő lépések

Ha továbbra is szeretné letiltani az EnableMultipleWriteLocations az Azure Cosmos-fiókjában, [Nyisson meg egy támogatási jegyet](https://azure.microsoft.com/support/create-ticket/).
