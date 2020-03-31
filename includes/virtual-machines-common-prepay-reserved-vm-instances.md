---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371672"
---
Ha véglegesíti az Azure számára fenntartott virtuálisgép-példányt, pénzt takaríthat meg. A foglalási kedvezmény automatikusan vonatkozik a foglalási hatókörnek és attribútumnak megfelelő futó virtuális gépek számára. A kedvezmények hez nem kell foglalást hozzárendelnie egy virtuális géphez. A fenntartott példány vásárlása csak a virtuális gép használatának számítási részét fedi le. Windows virtuális gépek esetén a használati mérő két külön méterre van osztva. Van egy számítási mérő, amely ugyanaz, mint a Linux mérő, és a Windows IP-mérő. A vásárláskor látható díjak csak a számítási költségekre szolgálnak. A díjak nem tartalmazzák a Windows szoftverköltségeit. A szoftverköltségekről további információt az [Azure által fenntartott virtuálisgép-példányok nem tartalmazzon című szoftverköltségek című témakörben talál.](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

## <a name="determine-the-right-vm-size-before-you-buy"></a>A megfelelő méret meghatározása a vásárlás előtt

Foglalás vásárlása előtt meg kell határoznia a virtuális gép méretét, amire szüksége van. A következő szakaszok segítenek meghatározni a megfelelő virtuális gép méretét.

### <a name="use-reservation-recommendations"></a>Foglalási javaslatok használata

A foglalási javaslatok segítségével meghatározhatja a megvásárolni kívánt foglalásokat.

- Vásárlási javaslatok és az ajánlott mennyiség jelenik meg, ha vásárol egy virtuális gép fenntartott példányaz Azure Portalon.
- Az Azure Advisor vásárlási javaslatokat nyújt az egyes előfizetésekhez.  
- Az API-k segítségével vásárlási javaslatokat kaphat a megosztott hatókörhöz és az egyelőfizetéses hatókörhöz. További információ: [Reserved instance purchase recommendation API-k for enterprise customers.](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- Nagyvállalati szerződés (EA) és a Microsoft ügyfélszerződés (MCA) ügyfelek számára az [Azure Consumption Insights Power BI tartalomcsomaggal](/power-bi/service-connect-to-azure-consumption-insights)vásárlási javaslatok érhetők el a megosztott és az egyelőfizetéses hatókörökhöz.

### <a name="services-that-get-vm-reservation-discounts"></a>A virtuálisgép-foglalási kedvezményekre jogosult szolgáltatások

A virtuálisgép-foglalások a több szolgáltatás általi virtuálisgép-használatra is alkalmazhatók, nem csupán a virtuális gépek üzemelő példányaira. A foglalási kedvezményekre jogosult erőforrások a példány méretének rugalmassági beállításától függően változnak.

#### <a name="instance-size-flexibility-setting"></a>Példány méretrugalmassági beállítása

A példány méretrugalmassági beállítása határozza meg, hogy mely szolgáltatásokra vonatkozik a fenntartott példányokhoz kapcsolódó kedvezmény.

Ha a *ConsumedService* értéke `Microsoft.Compute`, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva. Ebből kifolyólag ellenőrizze a használati adatait a *ConsumedService* értékének megtekintéséhez. Néhány példa:

- Virtuális gépek
- Virtuálisgép-méretezési csoportok
- Container Service
- Azure Batch-üzemelőpéldányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a *ConsumedService* az alábbiak egyike, a foglalási kedvezmények automatikusan érvényesülnek minden megfelelő virtuálisgép-használat esetében, attól függetlenül, hogy a beállítás be vagy ki van-e kapcsolva:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a *ConsumedService* értékét a használati adatokban annak megállapításához, hogy a használatra alkalmazhatók-e a foglalási kedvezmények.

A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése

A használati adatok elemzése segít meghatározni, hogy mely foglalásokat érdemes megvásárolnia. A használati adatok a használati fájlban és az API-kban érhetők el. Használja őket együtt annak meghatározására, hogy melyik foglalást vásárolja meg. Ellenőrizze a virtuális gép példányok, amelyek magas napi rendszerességgel a vásárláshoz szükséges foglalások mennyiségének meghatározásához. Kerülje `Meter` az alkategóriát és `Product` a használati adatok mezőit. Nem tesznek különbséget a prémium szintű tárhelyet használó virtuálisgép-méretek között. Ha ezeket a mezőket használja a virtuális gép méretének meghatározására a foglalási vásárláshoz, előfordulhat, hogy nem megfelelő méretet vásárol. Akkor nem kapja meg a foglalási kedvezményt, amit elvár. Ehelyett tekintse meg `AdditionalInfo` a mezőt a használati fájlban vagy a használati API-ban a megfelelő virtuális gép méretének meghatározásához.

A használati fájl a számlázási időszak és a napi használat szerint jeleníti meg a díjakat. A használati fájl letöltéséről az [Azure-használat és díjak megtekintése és letöltése](../articles/cost-management-billing/understand/download-azure-daily-usage.md)című témakörben talál. Ezt követően a használati fájl adatai alapján [meghatározhatja, hogy milyen foglalást vásároljon.](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Beszerzési korlátozással kapcsolatos szempontok

Fenntartott virtuálisgép-példányok érhetők el a legtöbb virtuálisgép-méretek, néhány kivételtől eltekintve. A foglalási kedvezmények nem vonatkoznak a következő virtuális gépekre:

- **VM sorozat** - A-sorozat, Av2-sorozat, vagy G-sorozat.

- **Előzetes verzió vagy promóciós virtuális gépek** – minden olyan virtuális gépsorozat vagy méret, amely előzetes verzióban van, vagy promóciós mérőt használ.

- **Felhők** – A foglalások nem vásárolnak németországi vagy kínai régiókban.

- **Nincs elegendő kvóta** – Az egyetlen előfizetésre ható foglalásnak rendelkeznie kell vCPU-kvótával az új fenntartott hely előfizetésében. Ha például a cél-előfizetés kvótakorlátja 10 vCPU d-sorozat, majd nem vásárolhat foglalást 11 Standard_D1 példányok. A foglalások kvótaellenőrzése tartalmazza az előfizetésben már üzembe helyezett virtuális gépeket. Ha például az előfizetés 10 vCPU-kvótával rendelkezik a D sorozathoz, és két standard_D1 példány telepítve van, akkor 10 standard_D1 példányfoglalást vásárolhat ebben az előfizetésben. A probléma megoldásához [ajánlatnövelési kérelmet](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) hozhat létre.

- **Kapacitáskorlátozások** – Ritka körülmények között az Azure korlátozza az új foglalások vásárlását a virtuális gépméretek részhalmazához, a régió alacsony kapacitása miatt.

## <a name="buy-a-reserved-vm-instance"></a>Fenntartott virtuálisgép-példány vásárlása

Az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)vásárolhat egy fenntartott virtuálisgép-példányt. A foglalásért fizethet [előre vagy havi részletekben](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Ezek a követelmények egy fenntartott virtuálisgép-példány megvásárlására vonatkoznak:

- Legalább egy Nagy- és Szolgáltatás-előfizetéshez tulajdonosi szerepkörben kell lennie, vagy előfizetésnek, amelynek díjfizetése van.
- EA-előfizetések esetén engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA portálon.](https://ea.azure.com/) Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.
- A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza a **Minden szolgáltatás** > **foglalása lehetőséget.**
1. Új foglalás vásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson **a Virtuális gép gombra.**
1. Töltse ki a kötelező mezőket. A foglalási kedvezményre a kiválasztott attribútumoknak megfelelő, futó virtuálisgép-példányok jogosultak. A kedvezményt megkapó virtuálisgép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

Ha nagyvállalati szerződéssel rendelkezik, a **További hozzáadás lehetőséggel** gyorsan hozzáadhat további példányokat. A lehetőség más előfizetéstípusok esetében nem érhető el.


| Mező      | Leírás|
|------------|--------------|
|Előfizetés|A foglaláshoz használt előfizetés. Az előfizetésfizetési mód a foglalás költségeit terheli. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfélszerződésnek, vagy egyéni előfizetésnek kell lennie, amelynek díja felosztó-kiosztó díjszabás (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). A díjakat levonják a monetáris kötelezettségvállalási egyenlegből, ha rendelkezésre állnak, vagy túllépésként számítják fel. A felosztó-kiosztó díjszabással rendelkező előfizetés ek esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.|    
|Hatókör       |A foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja: <ul><li>**Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.</li><li>**Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. Az EA-ügyfelek számára a számlázási környezet a regisztráció. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>|
|Régió    |A foglalás által lefedett Azure-régió.|    
|Virtuális gép mérete     |A virtuális gép példányainak mérete.|
|Optimalizálás     |A virtuális gép példányméretének rugalmassága alapértelmezés szerint be van jelölve. Kattintson a **Speciális beállítások** gombra a példányméret rugalmassági értékének módosításához, ha a foglalási kedvezményt az azonos [virtuálisgép-méretcsoportba](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)sorba foglaló más virtuális gépekre szeretné alkalmazni. A kapacitás prioritása kiemelt fontosságúként kezeli az adatközpont kapacitását az üzemelő példányok számára. További bizalmat biztosít a virtuálisgép-példányok elindításának képességében, amikor szüksége van rájuk. A kapacitásprioritás csak akkor érhető el, ha a foglalási hatókör egyetlen előfizetés. |
|Időtartam        |Egy vagy három éve.|
|Mennyiség    |A foglaláson belül vásárolt példányok száma. A mennyiség a virtuálisgép-példányok száma, amelyek letudják a számlázási engedményt. Ha például 10 Standard_D2 virtuális gépet futtat az USA keleti részén, akkor a mennyiséget 10-ként kell megadnia, hogy maximalizálja az összes futó virtuális gép előnyét. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalási kihasználtság

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. Megtekintheti, hogy melyik virtuálisgép-példány kapta meg az egyes foglalási kedvezményt.

Ha többet szeretne tudni arról, hogy a foglalási kedvezmények hogyan jelennek meg a használati adatokban, [olvassa el Az Azure-foglalás használatának ismertetése a nagyvállalati regisztrációhoz,](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) ha Ön nagyvállalati szerződéssel rendelkezik. Ha egyéni előfizetéssel rendelkezik, [olvassa el az Azure-foglalás használatának ismertetése az akárfelosztó-előfizetéshez.](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)

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

A módosításokat *exchange* azonban módosíthatja.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mik azok az Azure-foglalások?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [A Reservations kezelése az Azure-ban](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazásának ismertetése](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
