---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219834"
---
Zónaredundáns tárolás (ZRS) replikálja az adatokat három tárolási fürtöket egy régió között. Minden egyes tárolási fürt fizikailag elkülönül a többi, és a saját (AZ) rendelkezésre állási zónában található. Egyes rendelkezésre állási zónában&mdash;és a benne lévő ZRS fürt&mdash;önálló, és külön segédprogramok és hálózati funkciókat tartalmaz.

Ha az adatok a storage-fiókban a ZRS-replikációval, akkor továbbra is hozzá és felügyelhesse az adatokat, ha egy rendelkezésre állási zóna nem érhető el. A ZRS biztosít kiváló teljesítménye és kis késleltetése. Zrs-t kínál azonos [teljesítménycélokat](../articles/storage/common/storage-scalability-targets.md) , [helyileg redundáns tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

A zrs konzisztencia, tartósság és magas rendelkezésre állást igénylő forgatókönyvek esetén. Akkor is, ha a leállás vagy természeti katasztrófából megjelenik a rendelkezésre állási zóna nem érhető el, zrs-t biztosít a tárolt objektumok legalább 99,9999999999 %-os számára (12 9-es) éves.

Rendelkezésre állási zónákkal kapcsolatos további információkért lásd: [rendelkezésre állási zónák áttekintésének](https://docs.microsoft.com/azure/availability-zones/az-overview).