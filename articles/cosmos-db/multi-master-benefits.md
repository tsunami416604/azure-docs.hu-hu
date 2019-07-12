---
title: Az Azure Cosmos DB több főkiszolgálós előnyei
description: Az Azure Cosmos DB több főkiszolgálós előnyeinek megismeréséhez.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789292"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Több főkiszolgálós előnyeit az Azure Cosmos DB ismertetése

A cosmos DB Fiókfelelősök kell választania a megfelelő globális terjesztés beállításait, a késést, rendelkezésre állás és RTO-alkalmazásaik követelményei. Az Azure Cosmos-fiókok több írási helyen konfigurált jelentős előnyöket keresztül egyetlen írási hely többek között a fiókok, 99,999 %-os írási rendelkezésre állási SLA, < 10 ms írási késés SLA-t a 99. percentilis és RTO = 0 a regionális katasztrófa.

## <a name="comparison-of-features"></a>Szolgáltatások összehasonlítása

|Alkalmazás követelményeinek|Több írási hely|Egyetlen írási hely|Megjegyzés|
|---|---|---|---|
|Írási késés garantált szolgáltatási SZINTJÉTŐL < 10ms (P99)|**Igen**|Nem|Egyetlen írási hellyel rendelkező fiókok esetében minden egyes írási számítunk fel további régiók közötti hálózati késés.|
|Olvasási késés garantált szolgáltatási SZINTJÉTŐL < 10ms (P99)|**Igen**|Igen| |
|Írási 99,999 %-os szolgáltatásiszint-szerződés|**Igen**|Nem|Egyetlen írási hellyel rendelkező fiókok garantálja a 99,99 %-os szolgáltatásiszint-szerződés|
|RTO = 0|**Igen**|Nem|A nulla regionális katasztrófa írási ideje le. Egyetlen írási hellyel rendelkező fiókok jogosultak az RTO 15 perc.|

## <a name="next-steps"></a>További lépések

Ha továbbra is szeretné EnableMultipleWriteLocations letiltása az Azure Cosmos-fiókban, [hozzon létre egy támogatási jegyet](https://azure.microsoft.com/support/create-ticket/).
