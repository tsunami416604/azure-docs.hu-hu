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
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029799"
---
A tárfiókok replikációs lehetőségei:

* [Helyileg redundáns tárolás (LRS)](../articles/storage/common/storage-redundancy-lrs.md): Egyszerű, alacsony költséghatékonyságú replikációs stratégia. Az adatreplikálást az elsődleges régióban háromszor szinkron módon replikálja a rendszer.
* [Zóna – redundáns tárolás (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): A magas rendelkezésre állást igénylő forgatókönyvek replikálása. Az adatreplikációt a rendszer szinkron módon replikálja az elsődleges régió három Azure-beli rendelkezésre állási zónáján belül.
* [Geo-redundáns tárolás (GRS)](../articles/storage/common/storage-redundancy-grs.md): Régiók közötti replikáció a regionális kimaradások elleni védelemhez. Az adatreplikálást az elsődleges régióban háromszor szinkronizálja a rendszer, majd aszinkron módon replikálja a másodlagos régióba. A másodlagos régióban lévő adatolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS).
* [Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)](../articles/storage/common/storage-redundancy-gzrs.md): Replikáció a magas rendelkezésre állást és a maximális tartósságot igénylő forgatókönyvek esetében. Az adatreplikációt a rendszer szinkron módon replikálja az elsődleges régió három Azure-beli rendelkezésre állási zónájában, majd aszinkron módon replikálja a másodlagos régióba. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS).
