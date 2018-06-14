---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928708"
---
Helyileg redundáns tárolás (LRS) szolgál a legalább 99.999999999 % (11 9 tartozó) keresztül replikálja az adatokat a tárolási méretezési egység belül egy adott évben objektumok tartóssága. A tárolási méretezési egység esetén egy adatközpontban, a régióban, amelyben létrehozta a tárfiók tárolja. Az LRS tárfiók írási kérelem sikeresen visszaadja, csak azt követően az adatok írt összes replikát. Ezekre a replikákra találhatók minden egyes tartalék tartományok elválasztásához, és frissítse egy tárolási skálázási egységet tartományban.

A tárolási méretezési egység tárolócsomópontok rackszekrények gyűjteménye. A tartalék tartomány (FD) olyan csomóponton, amelyeket egy fizikai egységet kialakulását jelöl, és az ugyanazon fizikai állványra tartozó számítógépen tekinthető. Frissítési tartományok (UD) olyan csomóponton, amelyeket a szolgáltatás frissítése (Bevezetés) folyamata során egyszerre frissítik. A replikák vannak elosztva UDs és FDs belül egy tárolási skálázási egységet. Ez az architektúra biztosítja, hogy az adatok elérhető legyen-e ha hardverhiba egyetlen állvány hatással van, vagy ha a csomópont frissítése a bevezetés alatt.

LRS a legalacsonyabb költséget replikációs beállítás, és az egyéb beállítások képest legalább tartósságot biztosít. (Például tűz vagy elárasztás) datacenter szintű katasztrófa történik, ha az összes replika lehet elveszett vagy helyreállíthatatlan. Ennek a kockázatnak a mérséklése érdekében a Microsoft azt javasolja, zónaredundáns tárolás (ZRS) vagy a georedundáns tárolás (GRS) használatával.

* Ha az alkalmazás eltárolja könnyen rekonstruálható, ha adatvesztés történik, LRS is választhat.
* Adatreplikálás adatok cégirányítási követelmények miatt országon belül csak bizonyos alkalmazások korlátozódnak. Bizonyos esetekben a párosított régiók között, amelyek adatokat a rendszer replikálja a Georedundáns fiókok lehet egy másik országban. A párhuzamos régiók további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).
