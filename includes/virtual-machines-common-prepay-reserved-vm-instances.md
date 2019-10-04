---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08/29/2019
ms.openlocfilehash: 99263b7c7efee54381c9a7f624429b343dba49d0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806096"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Költségek megtakarítása Azure Reserved VM Instances

Ha egy Azure-beli fenntartott VM-példányra véglegesít, pénzt takaríthat meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő futó virtuális gépek számára. A kedvezmények beszerzéséhez nincs szükség foglalások hozzárendelésére egy virtuális géphez. A fenntartott példányok vásárlása csak a virtuális gépek használatának számítási részét fedi le. Windows rendszerű virtuális gépek esetén a használati mérőszám két külön méterre oszlik. Van egy számítási mérőszám, amely ugyanaz, mint a Linux-mérő, és egy Windows IP-mérő. A vásárláskor megjelenő díjak csak a számítási költségekre vonatkoznak. A díjak nem tartalmazzák a Windows-szoftverek költségeit. További információ a szoftverekkel kapcsolatos költségekről: [a Azure Reserved VM instances nem tartalmazott szoftveres költségek](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>A virtuális gép megfelelő méretének meghatározása a vásárlás előtt

A foglalás megvásárlása előtt meg kell határoznia a szükséges virtuális gép méretét. A következő szakaszban a virtuális gép megfelelő méretének meghatározásához talál segítséget.

### <a name="use-reservation-recommendations"></a>Foglalási javaslatok használata

A foglalási javaslatok segítségével megadhatja a megvásárolni kívánt foglalásokat.

- A vásárlási javaslatok és a javasolt mennyiség akkor jelenik meg, ha egy virtuális gép számára fenntartott példányt vásárol a Azure Portal.
- Azure Advisor az egyes előfizetésekre vonatkozó vásárlási javaslatokat tartalmaz.  
- Az API-k használatával vásárlási javaslatokat kaphat a megosztott hatókörhöz és az egyszeri előfizetések hatóköréhez is. További információ: [fenntartott példányok vásárlására vonatkozó ajánlás API-k nagyvállalati ügyfelek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)számára.
- Nagyvállalati Szerződés (EA) ügyfelek esetében a megosztott és az egyszeri előfizetési hatókörökkel kapcsolatos vásárlási javaslatok a [Azure Consumption Insights Power bi a csomagban](/power-bi/service-connect-to-azure-consumption-insights)érhetők el.

### <a name="services-that-get-vm-reservation-discounts"></a>A virtuális gépek foglalási kedvezményeit lekérdező szolgáltatások

A VM-foglalások a több szolgáltatásból kibocsátott virtuálisgép-használatra vonatkozhatnak, nem csupán a virtuális gépek üzembe helyezésére. A foglalási kedvezményeket lefoglaló erőforrások a példány méretének rugalmassági beállításától függően változnak.

#### <a name="instance-size-flexibility-setting"></a>Példány méretének rugalmassági beállítása

A példány méretének rugalmassága beállítás határozza meg, hogy mely szolgáltatások kapják meg a fenntartott példányok kedvezményeit.

Azt jelzi, hogy be van-e kapcsolva a beállítás, és a foglalási kedvezmények automatikusan érvényesek- `Microsoft.Compute`e a *ConsumedService* a megfelelő virtuális gépek használatára. Ezért a *ConsumedService* értéknél keresse meg a használati adatokat. Néhány példa:

- Virtual machines (Virtuális gépek)
- Virtuálisgép-méretezési csoportok
- Tárolószolgáltatás
- Azure Batch üzemelő példányok (felhasználói előfizetési módban)
- Azure Kubernetes Service (AKS)
- Service Fabric

Ha a beállítás be van kapcsolva, a foglalási kedvezmények automatikusan érvényesek a virtuális gépek használatára, ha a *ConsumedService* a következő elemek bármelyike:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Ellenőrizze a használati adatok *ConsumedService* értékét annak megállapításához, hogy a használat jogosult-e a foglalási kedvezményekre.

A példányok méretének rugalmasságával kapcsolatos további információkért lásd: [virtuális gépek méretének rugalmassága fenntartott VM-példányokkal](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése
A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia.

A használati adatok a használati fájlban és az API-kon érhetők el. Ezeket együtt használva meghatározhatja, hogy melyik foglalást szeretné megvásárolni. Ellenőrizze, hogy vannak-e napi szinten magas kihasználtságú virtuálisgép-példányok a megvásárolni kívánt foglalások mennyiségének meghatározásához.

Kerülje a `Meter` használati adatok alkategóriáját és `Product` mezőit. Nem tesznek különbséget a Premium Storage-t használó virtuális gépek méretei között. Ha ezeket a mezőket használja a foglalás megvásárlásához szükséges virtuális gép méretének meghatározásához, akkor a mérete nem megfelelő. Ezután nem fogja megkapni a várt foglalási kedvezményt. Ehelyett tekintse `AdditionalInfo` meg a használati fájl vagy a használati API mezőjét a virtuális gép megfelelő méretének meghatározásához.

### <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott VM-példányok a legtöbb virtuálisgép-mérethez érhetők el, néhány kivétellel. A foglalási kedvezmények nem vonatkoznak a következő virtuális gépekre:

- **VM-sorozat** – sorozat, Av2 sorozat vagy G sorozat.

- **Előzetes vagy promóciós virtuális gépek** – az előzetes verzióban vagy a promóciós fogyasztásmérőt használó VM-sorozatok vagy méretek.

- **Felhők** – a foglalások nem vásárolhatók meg németországi vagy kínai régiókban.

- Nem **elegendő kvóta** – egy adott előfizetésre vonatkozó foglalásnak az új ri-előfizetésben elérhető vCPU-kvótával kell rendelkeznie. Ha például a célként megadott előfizetés 10 vCPU a D sorozathoz, akkor nem vásárolhat le 11 Standard_D1-példányra vonatkozó foglalást. A foglalások kvótájának keresése magában foglalja az előfizetésben már üzembe helyezett virtuális gépeket. Ha például az előfizetés 10 vCPU rendelkezik a D sorozathoz, és két standard_D1 példánya van telepítve, akkor az előfizetésben 10 standard_D1-példány foglalását is megvásárolhatja. A probléma megoldásához [létrehozhat árajánlat-növelési kérést](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Kapacitás korlátozásai** – ritka körülmények között az Azure korlátozza a virtuálisgép-méretek részhalmazára vonatkozó új foglalások megvásárlását a régió alacsony kapacitása miatt.

## <a name="buy-a-reserved-vm-instance"></a>Fenntartott VM-példány vásárlása

Egy fenntartott VM-példányt vásárolhat a [Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). A foglalást [elöl vagy havi fizetéssel](../articles/billing/billing-monthly-payments-reservations.md)kell fizetni.

Ezek a követelmények a fenntartott VM-példányok vásárlására vonatkoznak:

- Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.
- Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása** beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés EA-rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **a minden szolgáltatás** > **foglalás**lehetőséget.
1. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson a **virtuális gép**elemre.
1. Adja meg a kötelező mezőket. A kiválasztott attribútumoknak megfelelő virtuálisgép-példányok futtatása a foglalási kedvezmény beszerzéséhez. A kedvezményt megkapó virtuálisgép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

| Mező      | Leírás|
|------------|--------------|
|Subscription|A foglalás kifizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési mód díja a foglalás díja. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben.|    
|Scope       |A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <ul><li>**Egy erőforráscsoport hatóköre** – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li>**Egyszeri előfizetés hatóköre** – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. Az utólagos elszámolású előfizetések esetében a számlázási hatókör a fiók rendszergazdája által létrehozott összes jogosult előfizetés.</li></ul>|
|Régió    |A foglalás által érintett Azure-régió.|    
|VM mérete     |A virtuálisgép-példányok mérete.|
|Optimalizálás:     |A VM-példány méretének rugalmassága alapértelmezés szerint ki van választva. Kattintson a **Speciális beállítások** lehetőségre a példány méretének rugalmassági értékének módosításához, hogy a foglalási kedvezményt más virtuális gépekre alkalmazza ugyanabban a virtuálisgép- [méretezési csoportban](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A kapacitás prioritása az üzemelő példányok adatközpont-kapacitását rangsorolja. További megbízhatóságot biztosít a virtuálisgép-példányok elindításához, amikor szüksége van rájuk. A kapacitás prioritása csak akkor érhető el, ha a foglalási hatókör egyetlen előfizetés. |
|Kifejezés        |Egy vagy három év.|
|Mennyiség    |A foglalás keretében megvásárolt példányok száma. A mennyiség azon futó virtuálisgép-példányok száma, amelyek megkapják a számlázási kedvezményt. Ha például 10 Standard_D2 virtuális gépet futtat az USA keleti régiójában, akkor a mennyiséget 10-re kell állítani, hogy maximalizálja az összes futó virtuális gép előnyeit. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalás kihasználtsága

A használati adatok a foglalási kedvezményt lefoglaló használati díj nulla értékeként érvényesek. Láthatja, hogy melyik virtuálisgép-példány fogadta el a foglalási kedvezményt az egyes foglalásokhoz.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatok között: az [Azure foglalási szolgáltatás használatának ismertetése a nagyvállalati beléptetéshez](../articles/billing/billing-understand-reserved-instance-usage-ea.md) , ha Ön EA-ügyfél. Ha egyéni előfizetéssel rendelkezik, tekintse meg [Az Azure foglalás használatának megismerése](../articles/billing/billing-understand-reserved-instance-usage.md)az utólagos elszámolású előfizetéshez című témakört.

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása a vásárlás után

A vásárlás után a következő típusú módosításokat végezheti el a foglalásban:

- Foglalási hatókör frissítése
- Példány méretének rugalmassága (ha van ilyen)
- Tulajdon

A foglalásokat feloszthatja kisebb adattömbökre, és összevonhatja a már felosztott foglalásokat is. A módosítások egyike sem eredményez új kereskedelmi tranzakciót, vagy nem módosítja a foglalás befejezési dátumát.

A vásárlás után a következő típusú módosításokat nem végezheti el közvetlenül:

- Egy meglévő foglalás régiója
- SKU
- Mennyiség
- Duration

Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére* .

## <a name="cancel-exchange-or-refund-reservations"></a>Megszakítási, Exchange-vagy visszatérítési foglalások

Bizonyos korlátozásokkal megszakíthatja, átválthatja vagy visszaállíthatja a foglalásokat. További információ: [önkiszolgáló cserék és visszatérítések Azure Reservations számára](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezeléséről a [Azure Reservations kezelése](../articles/billing/billing-manage-reserved-vm-instance.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
    - [Mi a Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Fenntartások kezelése az Azure-ban](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazási módjának megismerése](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Az előfizetések foglalási használatának ismertetése az utólagos elszámolású díjszabással](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [A Windows-szoftverek nem tartalmazzák a foglalásokat](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a partner Center Cloud Solution Provider (CSP) programban](https://docs.microsoft.com/partner-center/azure-reservations)
