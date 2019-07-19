---
title: A foglalási kedvezmények Azure App Service izolált bélyegzők esetében
description: Ismerje meg, hogyan vonatkoznak a foglalási kedvezmények Azure App Service izolált bélyegzőre.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298255"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>A foglalási kedvezmények Azure App Service izolált bélyegzők esetében

Miután megvásárolta izolált App Service Stamp díj számára fenntartott kapacitást, a foglalási kedvezményt a rendszer automatikusan alkalmazza az adott régióban lévő Stamp díjra. A foglalási kedvezmény az elkülönített Stamp díjszabási mérőszám által kibocsátott használatra vonatkozik. A feldolgozók, a további kezelőfelületek és a bélyegzőhöz kapcsolódó egyéb erőforrások továbbra is a normál díjszabás szerint kerülnek számlázásra.

## <a name="reservation-discount-application"></a>Foglalási kedvezmény alkalmazása

Az izolált App Service Stamp díjas kedvezményt az elkülönített bélyegek óránkénti futtatására alkalmazza a rendszer. Ha nem rendelkezik egy órán belül üzembe helyezett bélyegzővel, akkor a lefoglalt kapacitás az adott órában kárba vész. Nem végzi el a feladatátvételt.

A vásárlást követően a megvásárolt foglalás egy adott régióban futó elkülönített bélyegzővel egyezik meg. Ha leállítja a bélyegzőt, a foglalási kedvezményeket a rendszer automatikusan alkalmazza a régióban futó más bélyegzők esetében. Ha nem létezik bélyeg, a foglalás a régióban létrehozott következő bélyegzőre lesz alkalmazva.

Ha a bélyegzők nem egy teljes órára futnak, a foglalás automatikusan az ugyanabban a régióban lévő többi egyező bélyegre vonatkozik az adott órában.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Stamp típusának kiválasztása – Windows vagy Linux

Az üres elkülönített bélyegző alapértelmezés szerint a Windows Stamp-mérőt bocsátja ki. Például, ha nem telepítenek munkavégzőket. A Windows-feldolgozók telepítésekor továbbra is kibocsátja a mérőszámot. Ha Linux-feldolgozót telepít, a mérő a Linux Stamp-mérőre változik. A bélyegző a Windows-mérőt bocsátja ki, ha a Linux és a Windows rendszerű dolgozók is telepítve vannak.

Ennek eredményeképpen a Stamp mérőszáma változhat a Windows és a Linux között a Stamp élettartama során. Eközben a foglalások operációs rendszer-specifikusak. Olyan foglalást kell vásárolnia, amely támogatja a stampre telepítendő munkavégzőket. A csak Windowsos bélyegzők és a kevert bélyegek a Windows-foglalást használják. A csak Linux-feldolgozókkal rendelkező bélyegek a Linux-foglalást használják.

A Linux-foglalást csak akkor kell megvásárolnia, ha _csak_ a Linux-feldolgozókat tervezi a stampben.

## <a name="discount-examples"></a>Kedvezményes példák

Az alábbi példák azt mutatják be, hogy az elkülönített Stamp díjas példány kedvezménye a központi telepítéstől függően legyen érvényes.

- **1. példa**: Az elkülönített foglalási kapacitás egy példányát vásárolhatja meg izolált App Service Stamp nélküli régióban. Új bélyegzőt helyez üzembe a régióba, és megfizeti az adott bélyegző számára fenntartott díjakat.
- **2. példa**: Egy elszigetelt fenntartott bélyegző kapacitásának egy példányát vásárolhatja meg egy olyan régióban, ahol már van telepített izolált App Service Stamp. Elkezdi fogadni a központilag telepített bélyegző lefoglalt díjait.
- **3. példa**: Az elkülönített fenntartott bélyegző kapacitása egy példányban vásárolható meg egy olyan régióban, amelyen már telepítve van egy izolált App Service Stamp. Megkezdi a lefoglalt díj fogadását a központilag telepített bélyegzőn. Később törli a bélyegzőt, és üzembe helyez egy újat. A rendszer az új bélyegző számára fenntartott díjat kapja. A kedvezmények nem vihetők át a üzembe helyezett bélyegzők nélküli időtartamokra.
- **4. példa**: Egy adott régióban egy elszigetelt linuxos fenntartott Stamp kapacitás egy példányát vásárolja meg, majd új bélyegzőt helyez üzembe a régióba. Ha a bélyegzőt kezdetben feldolgozók nélkül helyezik üzembe, a Windows Stamp-mérőt bocsát ki. Nem érkezik kedvezmény. Az első Linux-feldolgozó üzembe helyezése után a rendszer kibocsátja a Linux Stamp-mérőt, és a foglalási kedvezményt is alkalmazza. Ha a Windows-feldolgozót később a stampre telepítik, a Stamp mérő visszavált a Windows rendszerre. Már nem kap kedvezményt az elkülönített linuxos fenntartott bélyegző foglalásához.

## <a name="next-steps"></a>További lépések

- A foglalások kezeléséről a [Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni az előzetes vásárlási izolált App Service Stamp fenntartott kapacitásáról a pénz megtakarítása érdekében, tekintse meg a [fenntartott kapacitással rendelkező Azure app Service elkülönített Stamp díj](billing-prepay-app-service-isolated-stamp.md)megfizetését.
- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
  - [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Fenntartások kezelése az Azure-ban](billing-manage-reserved-vm-instance.md)
  - [Az előfizetések foglalási használatának ismertetése az utólagos elszámolású díjszabással](billing-understand-reserved-instance-usage.md)
  - [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
