---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027447"
---
Zónaredundáns tárolás (ZRS) replikálja az adatokat három tárolási fürtöket egy régió között. Minden egyes tárolási fürt fizikailag elkülönül a többi pedig a saját rendelkezésre állási zónában (AZ) jelenti. Egyes rendelkezésre állási zóna és a ZRS fürtön belül, a rendszer külön segédprogramok és hálózati képességek autonóm.

Az adatok tárolása a ZRS-fiókok biztosítja, hogy lesz érhet el és -adatok kezelése, abban az esetben, ha a zóna nem érhető el. A ZRS biztosít kiváló teljesítménye és kis késleltetése. Zrs-t kínál azonos [teljesítménycélokat](../articles/storage/common/storage-scalability-targets.md) , [helyileg redundáns tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

A zrs akkor is, ha a leállás vagy természeti katasztrófából rendereli zónaszintű adatközpont elérhetetlenné erős konzisztencia, erős tartósság és magas rendelkezésre állást igénylő forgatókönyvek esetén. A ZRS kínál a tárolt objektumok legalább 99,9999999999 %-os számára (12 9-es) éves.

Rendelkezésre állási zónákkal kapcsolatos további információkért lásd: [rendelkezésre állási zónák áttekintésének](https://docs.microsoft.com/azure/availability-zones/az-overview).