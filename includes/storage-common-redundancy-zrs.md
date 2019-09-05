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
ms.openlocfilehash: b7fddce8f682bc341b361a47f8e083cc281e90aa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309562"
---
A Zone-redundáns tárolás (ZRS) egy régióban három tárolási fürtön párhuzamosan replikálja az adatait. Minden egyes tárolási fürt fizikailag el van különítve a többitől, és a saját rendelkezésre állási zónájában (AZ) található. Az egyes rendelkezésre&mdash;állási zónák és a ZRS&mdash;-fürt autonóm, és külön segédprogramokat és hálózatkezelési funkciókat tartalmaz. A ZRS Storage-fiók írási kérelme csak akkor ad vissza sikeres értéket, ha az adatok a három fürt összes replikáján át lettek írva.

Ha az adatait ZRS-replikáció használatával tárolja Storage-fiókban, továbbra is elérheti és kezelheti az adatait, ha a rendelkezésre állási zónák elérhetetlenné válnak. A ZRS kiváló teljesítményt és kis késleltetést biztosít. A ZRS ugyanazokat a [méretezhetőségi célokat](../articles/storage/common/storage-scalability-targets.md) kínálja, mint a [helyileg REDUNDÁNS tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Érdemes lehet olyan forgatókönyveket ZRS, amelyek következetességet, tartósságot és magas rendelkezésre állást igényelnek. Ha a rendelkezésre állási zóna nem érhető el, akkor is, ha az adatkimaradás vagy a természeti katasztrófa elérhetetlenné válik, a ZRS az adott évben legalább 99,9999999999%-os (12 9) tárolási objektumok számára biztosít tartósságot.

A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) az elsődleges régióban három Azure-beli rendelkezésre állási zónában replikálja az adatait, majd aszinkron módon replikálja az adatait a másodlagos régióba. A GZRS biztosítja a magas rendelkezésre állást, valamint a maximális tartósságot. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS). További információ a GZRS: [geo-Zone-redundáns tárolás a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)](../articles/storage/common/storage-redundancy-gzrs.md).

További információ a rendelkezésre állási zónákról: [Availability Zones Overview (áttekintés](https://docs.microsoft.com/azure/availability-zones/az-overview)).
