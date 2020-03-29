---
title: Az Azure Cosmos DB többmesteres előnyei
description: Ismerje meg a több főkiszolgálós Azure Cosmos DB előnyeit, a késés és az SLA-követelmények összehasonlítása egy és több írási helyen.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872009"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Ismerje meg a többfős előnyöket az Azure Cosmos DB-ben

A Cosmos DB-fiók üzemeltetőinek megfelelő globális terjesztési konfigurációt kell választaniuk az alkalmazások késésének, rendelkezésre állásának és RTO-követelményeinek biztosításához. A több írási hellyel konfigurált Azure Cosmos-fiókok jelentős előnyöket kínálnak az egyetlen írási helyű fiókokhoz képest, beleértve a 99,999%-os írási rendelkezésre állási SLA-t, <10 ms írási késleltetésű SLA-t a 99. percentilisnél és az RTO = 0-t egy regionális katasztrófa esetén.

## <a name="comparison-of-features"></a>A funkciók összehasonlítása

|Alkalmazási követelmény|Több írási hely|Egyszeri írási hely|Megjegyzés|
|---|---|---|---|
|<10 ms-os késleltetési SLA írása a P99-en|**Igen**|Nem|Az egyszeri írási helynel rendelkező fiókok minden egyes íráshoz további régióközi hálózati késést vonnak maga után.|
|A <10 ms-os késleltetési SLA olvasása a P99-en|**Igen**|Igen| |
|99,999%-os SLA írása|**Igen**|Nem|Az egyszeri írási helylel rendelkező fiókok 99,99%-os SLA-t garantálnak|
|RTO = 0|**Igen**|Nem|Regionális katasztrófák esetén nulla idő áll az írásokra. Az egyírási helynel rendelkező fiókok RTO-ja 15 min.|

## <a name="next-steps"></a>Következő lépések

Ha továbbra is le szeretné tiltani az EnableMultipleWriteLocations helyet az Azure Cosmos-fiókban, [nyisson meg egy támogatási jegyet.](https://azure.microsoft.com/support/create-ticket/)
