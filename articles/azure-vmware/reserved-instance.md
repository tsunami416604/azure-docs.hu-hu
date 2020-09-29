---
title: Költségek megtakarítása az Azure VMware-megoldás fenntartott példányával
description: Megtudhatja, hogyan vásárolhat fenntartott példányt az Azure VMware-megoldáshoz.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492426"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Költségek megtakarítása Azure VMware-megoldással

Ha az Azure VMware-megoldás fenntartott példányát véglegesíti, pénzt takaríthat meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a fenntartási hatókörrel és attribútumokkal megegyező Azure VMware-megoldási gazdagépek számára. A kedvezmények beszerzéséhez nem szükséges foglalást hozzárendelni egy dedikált gazdagéphez. A fenntartott példányok vásárlása csak a használat számítási részét fedi le, és a licencelési költségeket is magában foglalja. Tekintse át az [Azure VMware-megoldás áttekintését](introduction.md).

## <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott példányok bizonyos kivételekkel érhetők el.

-   **Felhők**   – A foglalások csak az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) lapon felsorolt régiókban érhetők el.

-   Nem **elegendő kvóta**   – Az egyetlen/megosztott előfizetésre vonatkozó foglalásnak az új fenntartott példányra vonatkozó előfizetésben elérhetőnek kell lennie a gazdagépek számára. A probléma megoldásához [kvóta-növelési kérelmet hozhat létre](enable-azure-vmware-solution.md) .

-   **Ajánlati jogosultság**– szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements)   a Microsofttal.

-   **Kapacitás korlátozásai**   – Ritka körülmények között az Azure korlátozza az Azure VMware Solution Host SKU-ra vonatkozó új foglalások megvásárlását, mivel a régió alacsony kapacitású.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

Megvásárolhatja az Azure VMware Solution Host-példány fenntartott példányát a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

A foglalást [elöl vagy havi fizetéssel](../cost-management-billing/reservations/prepare-buy-reservation.md)kell fizetni.

Ezek a követelmények a fenntartott dedikált gazdagép-példány megvásárlására vonatkoznak:

-   Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.

-   Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása**   beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.

Példány vásárlása:

1. Jelentkezzen be az  [Azure Portalra](https://portal.azure.com/).

2. Válassza **a minden szolgáltatás**   >  **foglalás**lehetőséget.

3.  **Add**   Új foglalás megvásárlásához válassza a Hozzáadás lehetőséget, majd válassza az **Azure VMware-megoldás**elemet.

4. Töltse ki a kötelező mezőket. Az Azure VMware megoldás azon gazdagépek futtatása, amelyek megfelelnek a kiválasztott attribútumoknak, a foglalási kedvezmény beszerzéséhez válassza a minősítés lehetőséget. Az Azure VMware megoldás azon gazdagépek tényleges száma, amelyek a kedvezményt kapják, a kiválasztott hatókörtől és mennyiségtől függenek.

   Ha nagyvállalati szerződéssel rendelkezik, a további példányok gyors hozzáadásához használhatja a **továbbiak hozzáadása lehetőséget**is   . A beállítás más előfizetési típusok esetében nem érhető el.

   | Mező        |  Leírás |
   | ------------ | ------------ |
   | Előfizetés | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfél-szerződés vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak a pénzügyi keretek egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben. |
   | Hatókör        | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja:<br><ul><li><b>Egy erőforráscsoport hatóköre – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li><b>Egyszeri előfizetés hatóköre – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li><b>Megosztott hatókör – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>       |
   | Region       | A foglalás által érintett Azure-régió.   |
   | Gazdagép mérete    | AV36    |
   | Időszak         | Egy vagy három év.  |
   | Mennyiség     | A foglalás keretében megvásárolt példányok száma. A mennyiség azon Azure VMware-megoldási gazdagépek száma, amelyek beszerezhetik a számlázási kedvezményt.    |

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalás kihasználtsága

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. Láthatja, hogy melyik Azure VMware Solution-példány fogadta el az egyes foglalások foglalási kedvezményét.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatokban, és Ön [nagyvállalati ügyfél, az Azure foglalás használatának megismerése a vállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)című témakörben talál további információt. Ha egyéni előfizetéssel rendelkezik, tekintse meg [Az Azure foglalás használatának megismerése](../cost-management-billing/reservations/understand-reserved-instance-usage.md)az utólagos elszámolású előfizetéshez című témakört.

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása a vásárlás után

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

-   Foglalás hatókörének frissítése

-   Példány méretének rugalmassága (ha van ilyen)

-   Tulajdonjog

A foglalásokat kisebb adattömbökre vagy egyesítési foglalásokra is kioszthatja. A módosítások egyike sem eredményez új kereskedelmi tranzakciót, vagy nem módosítja a foglalás befejezési dátumát.

>[!NOTE]
>A foglalás megvásárlása után a következő típusú módosításokat nem végezheti el közvetlenül:
>
> - Egy meglévő foglalás régiója
> - Termékváltozat
> - Mennyiség
> - Időtartam
>
>Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére*   .

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információ: [önkiszolgáló cserék és visszatérítések Azure Reservations számára](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).
