---
title: Előre fizet az Azure dedikált házigazdák számára, hogy pénzt takarítson meg
description: Ismerje meg, hogyan vásárolhat Azure-ra fenntartott gazdagépek fenntartott példányokat a számítási költségek megtakarítása érdekében.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207744"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Takarítson meg költségeket az Azure-ra fenntartott gazdagépek fenntartott példányával

Ha véglegesíti az Azure dedikált gazdagépek fenntartott példányát, pénzt takaríthat meg. A foglalási kedvezmény automatikusan vonatkozik a foglalási hatókörnek és attribútumnak megfelelő dedikált állomások számára. A kedvezmények hez nem kell foglalást rendelnie egy dedikált házigazdához. A fenntartott példányvásárlás csak a használat számítási részét fedi le, és szoftverlicencelési költségeket is tartalmaz. Tekintse [meg az Azure dedikált gazdagépek áttekintése a virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Vásárlás előtt határozza meg a megfelelő dedikált gazdatermékváltozatot


Mielőtt foglalást vásárolna, meg kell határoznia, hogy melyik dedikált házigazdára van szüksége. A termékváltozat van definiálva egy dedikált gazdagép, amely a virtuális gép sorozat és a típus. 

Kezdje azzal, hogy a [windowsos virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) támogatott méreteit a virtuális gép sorozat azonosításához.

Ezután ellenőrizze, hogy támogatott-e az Azure dedikált gazdagépeken. [Az Azure Dedikált házigazdák díjszabási](https://aka.ms/ADHPricing) lapja tartalmazza a dedikált gazdagépek teljes listáját, a processzoradatokat és a különböző díjszabási lehetőségeket (beleértve a fenntartott példányokat is).

Előfordulhat, hogy több termékkészlet támogatja a Virtuálisgép-sorozatot (különböző típusokkal). Azonosítsa a legjobb termékváltozatot az állomás kapacitásának (vCPU-k száma) összehasonlításával. Vegye figyelembe, hogy ön foglalás tanusítható több dedikált gazdagép skus támogatja az azonos virtuálisgép-sorozat (például DSv3_Type1 és DSv3_Type2), de nem a különböző virtuálisgép-sorozatok (például A DSv3 és ESv3).



## <a name="purchase-restriction-considerations"></a>Beszerzési korlátozással kapcsolatos szempontok

A fenntartott példányok a legtöbb dedikált gazdagépmérethez elérhetők, néhány kivételtől eltekintve.

A foglalási kedvezmények nem vonatkoznak a következőkre:

- **Felhők** – A foglalások nem vásárolnak németországi vagy kínai régiókban.

- **Nincs elegendő kvóta** – Az egyetlen előfizetésre ható foglalásnak rendelkeznie kell vCPU-kvótával az új fenntartott példány előfizetésében. Ha például a cél-előfizetés kvótakorlátja 10 vCPU a DSv3-sorozat, majd nem vásárolhat foglalás dedikált gazdagépek támogatja ezt a sorozatot. A foglalások kvótaellenőrzése tartalmazza a virtuális gépeket és az előfizetésben már üzembe helyezett dedikált gazdagépeket. A probléma megoldásához [kvótanövelési kérelmet](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) hozhat létre.

- **Kapacitáskorlátozások** – Ritka körülmények között az Azure korlátozza az új foglalások vásárlását a dedikált gazdagép-sorozatok részhalmazához, a régió alacsony kapacitása miatt.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

Az [Azure-portálon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)megvásárolhatja egy Dedikált gazdagép fenntartott példányát.

A foglalást [előre vagy havi fizetéssel](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations)kell kifizetni. Ezek a követelmények a fenntartott dedikált gazdagép példány megvásárlására vonatkoznak:

- Legalább egy Nagy- és Szolgáltatás-előfizetéshez tulajdonosi szerepkörben kell lennie, vagy előfizetésnek, amelynek díjfizetése van.

- EA-előfizetések esetén engedélyezni kell a **Fenntartott példányok** hozzáadása beállítást az [EA portálon.](https://ea.azure.com/) Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.

- A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az  [Azure Portalra](https://portal.azure.com/).

2. Válassza a **Minden szolgáltatás** \> **foglalása lehetőséget.**

3. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson **a Dedikált állomások gombra.**

4. Töltse ki a kötelező mezőket. A kijelölt állomásoknak megfelelő példányok futtatása jogosult a foglalási kedvezmény rekedésére. A dedikált gazdagép példányainak tényleges száma, amelyek az engedményt kapják, a kiválasztott hatókörtől és mennyiségtől függ.

Ha nagyvállalati szerződéssel rendelkezik, a **További hozzáadás lehetőséggel** gyorsan hozzáadhat további példányokat. A lehetőség más előfizetéstípusok esetében nem érhető el.

| **Mező**           | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Előfizetés        | A foglaláshoz használt előfizetés. Az előfizetésfizetési mód a foglalás költségeit terheli. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfélszerződésnek, vagy egyéni előfizetésnek kell lennie, amelynek díja felosztó-kiosztó díjszabás (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). A díjakat levonják a monetáris kötelezettségvállalási egyenlegből, ha rendelkezésre állnak, vagy túllépésként számítják fel. A felosztó-kiosztó díjszabással rendelkező előfizetés ek esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük. |
| Hatókör               | A foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Régió              | A foglalás által lefedett Azure-régió.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedikált gazdagép méret | A dedikált gazdagép példányainak mérete.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Időtartam                | Egy vagy három éve.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Mennyiség            | A foglaláson belül vásárolt példányok száma. A mennyiség a számlázási engedményt lekérdező dedikált állomáspéldányok száma.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Egyetlen erőforráscsoport hatóköre** – A foglalási engedményt csak a kijelölt erőforráscsoport megfelelő erőforrásaira alkalmazza.

- **Egyetlen előfizetés hatóköre** – A foglalási kedvezmény alkalmazása a kijelölt előfizetés megfelelő erőforrásaira.

- **Megosztott hatókör** – A foglalási kedvezmény alkalmazása a számlázási környezetben lévő jogosult előfizetések megfelelő erőforrásaira. Az EA-ügyfelek számára a számlázási környezet a regisztráció. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalási kihasználtság

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. Megtekintheti, hogy melyik virtuálisgép-példány kapta meg az egyes foglalási kedvezményt.

Ha többet szeretne tudni arról, hogy a foglalási kedvezmények hogyan jelennek meg a használati adatokban, [olvassa el Az Azure-foglalás használatának ismertetése a nagyvállalati regisztrációhoz,](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) ha Ön nagyvállalati szerződéssel rendelkezik. Ha egyéni előfizetéssel rendelkezik, [olvassa el az Azure-foglalás használatának ismertetése az akárfelosztó-előfizetéshez.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása vásárlás után

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

- Foglalás hatókörének frissítése

- A példány méretének rugalmassága (adott esetben)

- Tulajdonjog

A foglalást kisebb részekre is feloszthatja, és egyesítheti a foglalásokat. A módosítások egyike sem okoz új kereskedelmi tranzakciót, és nem módosítja a foglalás befejezési dátumát.

A vásárlás után nem hajthatja végre közvetlenül a következő típusú módosításokat:

- Meglévő foglalási régió

- SKU

- Mennyiség

- Időtartam

A módosításokat *exchange* azonban módosíthatja.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információ: [Self-service exchanges and refund for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalások kezeléséről az [Azure-foglalások kezelése témakörben](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)olvashat.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok az Azure-foglalások?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Az Azure dedikált gazdagépeinek használata](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Dedikált házigazdák árképzése](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [A Reservations kezelése az Azure-ban](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [A foglalási kedvezmény alkalmazásának ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [A Reservations díjában nem szereplő Windows-szoftverköltségek](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)


