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
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133597"
---
Zónaredundáns tárolás (ZRS) replikálja az adatokat három tárolási fürtöket egy régió között. Minden egyes tárolási fürt fizikailag elkülönül a többi, és a saját (AZ) rendelkezésre állási zónában található. Egyes rendelkezésre állási zónában&mdash;és a benne lévő ZRS fürt&mdash;önálló, és külön segédprogramok és hálózati funkciókat tartalmaz. A ZRS tárfiókok írási kérés sikeresen visszaadja, csak azt követően az adatok írása az összes replika a három fürtök között.

Ha az adatok a storage-fiókban a ZRS-replikációval, akkor továbbra is hozzá és felügyelhesse az adatokat, ha egy rendelkezésre állási zóna nem érhető el. A ZRS biztosít kiváló teljesítménye és kis késleltetése. Zrs-t kínál azonos [teljesítménycélokat](../articles/storage/common/storage-scalability-targets.md) , [helyileg redundáns tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

A zrs konzisztencia, tartósság és magas rendelkezésre állást igénylő forgatókönyvek esetén. Akkor is, ha a leállás vagy természeti katasztrófából megjelenik a rendelkezésre állási zóna nem érhető el, zrs-t biztosít a tárolt objektumok legalább 99,9999999999 %-os számára (12 9-es) éves.

Rendelkezésre állási zónákkal kapcsolatos további információkért lásd: [rendelkezésre állási zónák áttekintésének](https://docs.microsoft.com/azure/availability-zones/az-overview).