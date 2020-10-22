---
title: Költségek megtakarítása az Azure VMware-megoldás fenntartott példányával
description: Megtudhatja, hogyan vásárolhat fenntartott példányt az Azure VMware-megoldáshoz.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370678"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Költségek megtakarítása Azure VMware-megoldással

Ha az [Azure VMware-megoldás](introduction.md)fenntartott példányára véglegesít, pénzt takarít meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a lefoglalási hatókörnek és attribútumoknak megfelelő Azure VMware megoldás-gazdagépekre. A kedvezmények beszerzéséhez nem szükséges foglalást hozzárendelni egy dedikált gazdagéphez. A fenntartott példányok vásárlása csak a használat számítási részét fedi le, és magában foglalja a licencelési költségeket is. 


## <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott példányok bizonyos kivételekkel érhetők el.

-   **Felhők** – a foglalások csak az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) lapon felsorolt régiókban érhetők el.

-   Nem **elegendő kvóta** – az egyetlen/megosztott előfizetéshez tartozó foglalási kvótának az új fenntartott példány előfizetésében elérhetőnek kell lennie a gazdagépek számára. A probléma megoldásához [kvóta-növelési kérelmet hozhat létre](enable-azure-vmware-solution.md) .

-   **Ajánlati jogosultság**– szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](../cost-management-billing/manage/ea-portal-agreements.md) a Microsofttal.

-   **Kapacitás korlátozásai** – ritka körülmények között az Azure korlátozza az Azure VMware-megoldás gazdagépének új foglalásának megvásárlását az adott régió alacsony kapacitása miatt.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

Megvásárolhatja az Azure VMware Solution Host-példány fenntartott példányát a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md).

Ezek a követelmények a fenntartott dedikált gazdagép-példány megvásárlására vonatkoznak:

-   Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.

-   Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása** beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.

3. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd válassza az **Azure VMware-megoldás**elemet.

4. Adja meg a kötelező mezőket. Ha az Azure VMware megoldás azon gazdagépeit futtatja, amelyek megfelelnek az attribútumoknak, válassza a minősítés lehetőséget a foglalási kedvezmény beszerzéséhez. Az Azure VMware-megoldás azon gazdagépek tényleges száma, amelyek a kiválasztott hatókörtől és mennyiségtől függően kedvezményt kapnak.

   Ha nagyvállalati szerződéssel rendelkezik, a további példányok gyors hozzáadásához használhatja a **továbbiak hozzáadása lehetőséget** is. A beállítás más előfizetési típusok esetében nem érhető el.

   | Mező        |  Leírás |
   | ------------ | ------------ |
   | Előfizetés | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfél-szerződés vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak a pénzügyi keretek egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben. |
   | Hatókör        | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja:<br><ul><li><b>Egy erőforráscsoport hatóköre – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li><b>Egyszeri előfizetés hatóköre – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li><b>Megosztott hatókör – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>       |
   | Régió       | A foglalás által érintett Azure-régió.   |
   | Gazdagép mérete    | AV36    |
   | Időszak         | Egy vagy három év.  |
   | Mennyiség     | A foglalás keretében megvásárolt példányok száma. A mennyiség azon Azure VMware-megoldási gazdagépek száma, amelyek beszerezhetik a számlázási kedvezményt.    |

## <a name="usage-data-and-reservation-usage"></a>Használati adatok és foglalások használata

A foglalási kedvezményt lefoglaló használat nulla értékű. Láthatja, hogy melyik Azure VMware Solution-példány fogadta el az egyes foglalások foglalási kedvezményét.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatokban:

- Nagyvállalati szerződéssel rendelkező ügyfelek esetében lásd: az [Azure foglalás használatának ismertetése a vállalati regisztrációhoz](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Egyéni előfizetések esetén lásd: az [Azure foglalás használatának ismertetése](../cost-management-billing/reservations/understand-reserved-instance-usage.md) az utólagos elszámolású előfizetéshez

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
>Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére* .

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).