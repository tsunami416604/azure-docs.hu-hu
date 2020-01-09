---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463937"
---
A Zone-redundáns tárolás (ZRS) egy régióban három tárolási fürtön párhuzamosan replikálja az adatait. Minden egyes tárolási fürt fizikailag el van különítve a többitől, és a saját rendelkezésre állási zónájában (AZ) található. A rendelkezésre állási zónák&mdash;és a&mdash;benne lévő ZRS-fürt önálló, és külön segédprogramokat és hálózatkezelési funkciókat tartalmaz. A ZRS Storage-fiók írási kérelme csak akkor ad vissza sikeres értéket, ha az adatok a három fürt összes replikáján át lettek írva.

Ha az adatait ZRS-replikáció használatával tárolja Storage-fiókban, továbbra is elérheti és kezelheti az adatait, ha a rendelkezésre állási zónák elérhetetlenné válnak. A ZRS kiváló teljesítményt és kis késleltetést biztosít. A ZRS ugyanazokat a méretezhetőségi célokat kínálja, mint a [helyileg redundáns tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md). A standard szintű Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd a [standard szintű Storage-fiókok méretezhetőségi céljait](../articles/storage/common/scalability-targets-standard-account.md)ismertető témakört.

Érdemes lehet olyan forgatókönyveket ZRS, amelyek következetességet, tartósságot és magas rendelkezésre állást igényelnek. Ha a rendelkezésre állási zóna nem érhető el, akkor is, ha az adatkimaradás vagy a természeti katasztrófa elérhetetlenné válik, a ZRS az adott évben legalább 99,9999999999%-os (12 9) tárolási objektumok számára biztosít tartósságot.

A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) az elsődleges régióban három Azure-beli rendelkezésre állási zónában replikálja az adatait, majd aszinkron módon replikálja az adatait a másodlagos régióba. A GZRS biztosítja a magas rendelkezésre állást, valamint a maximális tartósságot. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS). További információ a GZRS: [geo-Zone-redundáns tárolás a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)](../articles/storage/common/storage-redundancy-gzrs.md).

További információ a rendelkezésre állási zónákról: [Availability Zones Overview (áttekintés](https://docs.microsoft.com/azure/availability-zones/az-overview)).
