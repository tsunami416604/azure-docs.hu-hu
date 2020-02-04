---
title: Foglalási kedvezmények az Azure App Service-hez
description: Megtudhatja, hogyan érvényesülnek a foglalási kedvezmények az Azure App Service izolált tranzakcióin.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 1294ee126ecef1f635ffc4d6bec6da02c6171897
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995792"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Foglalási kedvezmények alkalmazása az Azure App Service izolált tranzakcióira

Miután az App Service izolált tranzakcióinak számára fenntartott kapacitást vásárolt, a foglalási kedvezmény automatikusan érvényesül az adott régió tranzakciódíján. A foglalási kedvezmény az izolált tranzakció díjszabási mérőszáma által mért használatra vonatkozik. A feldolgozókért, a további előtérrendszerekért és a tranzakcióhoz kapcsolódó minden más erőforrásért továbbra is a normál díjszabás szerint kell fizetni.

## <a name="reservation-discount-application"></a>A foglalási kedvezmény alkalmazása

Az App Service izolált tranzakciódíj-kedvezményt a rendszer óránként alkalmazza a futó izolált tranzakciókra. Ez azt jelenti, hogy ha nem üzemel végig valamely tranzakció az óra időtartama alatt, akkor az arra az órára vonatkozó lefoglalt kapacitás kárba vész. A kapacitás nem vihető át a következő órára.

A vásárlás után a megvásárolt foglalást a rendszer megfelelteti egy izolált tranzakciónak, amely egy adott régióban fut. Ha leállítja ezt a tranzakciót, a foglalási kedvezmények automatikusan a régióban futó más tranzakciókra lesznek érvényesítve. Ha nincsenek tranzakciók, a foglalás a régióban következőként létrehozott tranzakción lesz érvényesítve.

Ha a tranzakciók nem futnak végig az óra időtartama alatt, a foglalás automatikusan más megfelelő tranzakciókon lesz érvényesítve ugyanabban a régióban és órában.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Tranzakciótípus választása – Windows vagy Linux

Az üres izolált tranzakciók alapértelmezés szerint a Windows-tranzakciómérőszámot bocsátják ki. Ez például akkor van így, ha nincsenek üzembe helyezve feldolgozók. A tranzakció továbbra is ezt a mérőszámot bocsátja ki, ha Windows-feldolgozók lesznek üzembe helyezve. Ha üzembe helyez egy Linux-feldolgozót, akkor a mérő a Linux-tranzakciómérőszámra vált át. Ha Linux- és Windows-feldolgozók is üzembe vannak helyezve, a tranzakció Windows-mérőszámot bocsát ki.

Ennek eredményeképpen a tranzakció mérőszáma a Windows és a Linux között váltakozhat a tranzakció élettartama során. A foglalások operációsrendszer-specifikusak. Olyan foglalást vásároljon, amely támogatja a tranzakción üzembe helyezni kívánt feldolgozókat. A csak Windows-alapú és a vegyes tranzakciók a Windows-foglalást használják. A csak Linux-feldolgozókkal rendelkező tranzakciók a Linux-foglalást használják.

Linux-alapú tranzakciófoglalást csak akkor érdemes vásárolnia, ha a tranzakción _kizárólag_ Linux-feldolgozókkal szeretne rendelkezni.

## <a name="discount-examples"></a>Példák a kedvezményre

Az alábbi példák bemutatják, hogyan érvényesül a fenntartott példányokra vonatkozó izolált tranzakciódíj-kedvezmény az üzemelő példányoktól függően.

- **1. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy App Service izolált tranzakcióval nem rendelkező régióban. Új tranzakciót helyez üzembe a régióban, és a foglalásnak megfelelő díjszabás szerint fizet a tranzakcióért.
- **2. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy üzembe helyezett App Service izolált tranzakcióval már rendelkező régióban. Az üzembe helyezett tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet.
- **3. példa**: A lefoglalt izolálttranzakció-kapacitás egy példányát vásárolja meg egy üzembe helyezett App Service izolált tranzakcióval már rendelkező régióban. Az üzembe helyezett tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet. Később törli a tranzakciót, és újat helyez üzembe. Az új tranzakcióért innentől a foglalásnak megfelelő díjszabás szerint fizet. Az üzembe helyezett tranzakciók nélküli időtartamok esetében a kedvezmények nem vihetők át.
- **4. példa**: A lefoglalt Linux-alapú izolálttranzakció-kapacitás egy példányát vásárolja meg egy régióban, majd új tranzakciót helyez üzembe a régióban. Amikor a tranzakciót kezdetben feldolgozók nélkül helyezi üzembe, az a Windows-tranzakciómérőszámot bocsátja ki. Ekkor nem részesül kedvezményben. Amint az első Linux-feldolgozót üzembe helyezi a tranzakción, az a Linux-tranzakciómérőszámot bocsátja ki, és a foglalási kedvezmény érvénybe lép. Ha később Windows-feldolgozót helyez üzembe a tranzakción, a tranzakció-mérőszám Windowsra vált vissza. Ettől fogva nem részesül kedvezményben a Linux-alapú izolálttranzakció-foglalásért.

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Ha többet szeretne tudni a lefoglalt App Service izolálttranzakció-kapacitás takarékossági céllal történő előzetes megvásárlásáról, tekintse meg az [az Azure App Service izolált tranzakciódíjért lefoglalt kapacitással történő előzetes fizetést](prepay-app-service-isolated-stamp.md) ismertető szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)
  - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
