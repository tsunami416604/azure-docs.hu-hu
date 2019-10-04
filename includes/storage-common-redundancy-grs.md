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
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029802"
---
A Geo-redundáns tárolás (GRS) úgy van kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben, az adatoknak egy olyan másodlagos régióba való replikálásával, amely több száz mérföld távolságra van az elsődleges régiótól. Ha a GRS engedélyezve van, akkor az adatai tartósak maradnak, még akkor is, ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható.

Ha a GRS lehetőséget választja, két kapcsolódó lehetőség közül választhat:

* A GRS replikálja az adatait egy másik adatközpontba egy másodlagos régióban, de az adatok csak olvashatók, ha a Microsoft feladatátvételt kezdeményez az elsődlegestől a másodlagos régióba.
* A Read-Access geo-redundáns tárolás (RA-GRS) a GRS-on alapul. Az RA-GRS egy másodlagos régióba replikálja az adatait egy másik adatközpontba, és lehetővé teszi a másodlagos régióból való olvasás lehetőségét is. Az RA-GRS segítségével elolvashatja a másodlagos régiót, függetlenül attól, hogy a Microsoft kezdeményez-e feladatátvételt az elsődlegesről a másodlagos régióra.

A GRS-t vagy RA-GRS-t használó Storage-fiókok esetében az összes adattal a helyileg redundáns tárolással (LRS) lesznek replikálva. A rendszer először egy frissítést véglegesít az elsődleges helyen, és replikálja a LRS használatával. Ezt követően a rendszer aszinkron módon replikálja a frissítést a másodlagos régióba a GRS használatával. Ha az adatírás a másodlagos helyre történik, a rendszer a LRS használatával is replikálja az adott helyen belül.

Az elsődleges és a másodlagos régió is kezeli a replikákat a különböző tartalék tartományok között, és a tárolási méretezési egységen belül frissíti a tartományokat. A tárolási méretezési egység az adatközpontban található alapszintű replikációs egység. A replikáció ezen a szinten a LRS által biztosított. További információt a helyileg redundáns [tárolás (LRS) című témakörben talál: Alacsony költséghatékonyságú adatredundancia az Azure Storage](../articles/storage/common/storage-redundancy-lrs.md)szolgáltatáshoz.

Tartsa szem előtt ezeket a szempontokat, amikor dönti el, hogy melyik replikációs beállítást szeretné használni:

* A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) biztosítja a magas rendelkezésre állást és a maximális tartósságot azáltal, hogy szinkronizálja az adatátvitelt három Azure rendelkezésre állási zónában, majd aszinkron módon replikálja az adatreplikálást a másodlagos régióba. Engedélyezheti a másodlagos régió olvasási hozzáférését is. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben. További információ a GZRS-ről: [geo-Zone-redundáns tárolás a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)](../articles/storage/common/storage-redundancy-gzrs.md).
* A Zone-redundáns tárolás (ZRS) a három Azure-beli rendelkezésre állási zónában való szinkron replikálással biztosítja a nagyfokú rendelkezésre állást, és a GRS vagy RA-GRS esetében jobb választás lehet. További információ a ZRS: [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Az aszinkron replikáció az adatoknak az elsődleges régióba való beírásának időpontjától számított késleltetést foglal magában, a másodlagos régióba való replikáláskor. Regionális katasztrófa esetén előfordulhat, hogy a másodlagos régióba még nem replikált módosítások elvesznek, ha az adatok nem állíthatók helyre az elsődleges régióból.
* A GRS esetében a replika nem érhető el olvasási vagy írási hozzáféréshez, kivéve, ha a Microsoft feladatátvételt kezdeményez a másodlagos régióba. Feladatátvétel esetén a feladatátvétel befejezése után olvasási és írási hozzáféréssel fog rendelkezni az adataihoz. További információ: vész- [helyreállítási útmutató](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Ha az alkalmazásnak a másodlagos régióból kell beolvasnia, engedélyezze az RA-GRS vagy az RA-GZRS (előzetes verzió) lehetőséget.
