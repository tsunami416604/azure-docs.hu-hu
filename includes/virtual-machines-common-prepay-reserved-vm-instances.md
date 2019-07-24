---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/11/2019
ms.openlocfilehash: 766856438b22661b961bfbadc0b63376031622f6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850769"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Előre fizetés a Virtual Machineshoz Azure Reserved VM Instances (RI)

Fizessen elő a virtuális gépekre, és pénzt takaríthat meg az Azure Reserved Virtual Machine (VM) példányaival. További információ: [Azure Reserved VM instances ajánlat](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Egy fenntartott VM-példányt vásárolhat a [Azure Portalban](https://portal.azure.com). Példány vásárlása:

- Legalább egy vállalati előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.
- Vállalati előfizetések esetén az [EA portálon](https://ea.azure.com)engedélyezni kell a **fenntartott példányok hozzáadását** . Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak foglalásokat.

A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő futó virtuális gépek számára. A foglalás hatókörét [Azure Portal](https://portal.azure.com), a PowerShell, a CLI vagy az API használatával frissítheti.

## <a name="determine-the-right-vm-size-before-you-buy"></a>A virtuális gép megfelelő méretének meghatározása a vásárlás előtt

A foglalás megvásárlása előtt meg kell határoznia a szükséges virtuális gép méretét. A következő szakaszban a virtuális gép megfelelő méretének meghatározásához talál segítséget.

### <a name="use-reservation-recommendations"></a>Foglalási javaslatok használata

A foglalási javaslatok segítségével megadhatja a megvásárolni kívánt foglalásokat.

- A vásárlási javaslatok és a javasolt mennyiség akkor jelenik meg, ha egy virtuális gép számára fenntartott példányt vásárol a Azure Portal.
- Azure Advisor az egyes előfizetésekre vonatkozó vásárlási javaslatokat tartalmaz.  
- Az API-k használatával vásárlási javaslatokat kaphat a megosztott hatókörhöz és az egyszeri előfizetések hatóköréhez is. További információ: fenntartott [példányok vásárlására vonatkozó ajánlás API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)-k nagyvállalati ügyfelek számára.
- A nagyvállalati szerződéssel rendelkező ügyfelek esetében a megosztott és az egyszeri előfizetési hatókörökkel kapcsolatos vásárlási javaslatok a [Azure Consumption Insights Power bi a Content Pack csomaggal](/power-bi/service-connect-to-azure-consumption-insights)érhetők el.

### <a name="services-that-get-vm-reservation-discounts"></a>A virtuális gépek foglalási kedvezményeit lekérdező szolgáltatások

A VM-foglalások a több szolgáltatásból kibocsátott virtuálisgép-használatra vonatkozhatnak, nem csupán a virtuális gépek üzembe helyezésére. A foglalási kedvezményeket lefoglaló erőforrások a példány méretének rugalmassági beállításától függően változnak.

#### <a name="instance-size-flexibility-setting"></a>Példány méretének rugalmassági beállítása

A példány méretének rugalmassága beállítás határozza meg, hogy mely szolgáltatások kapják meg a fenntartott példányok kedvezményeit.

Azt jelzi, hogy be van-e kapcsolva a beállítás, és a foglalási kedvezmények automatikusan érvényesek- `Microsoft.Compute`e a *ConsumedService* a megfelelő virtuális gépek használatára. Ezért a *ConsumedService* értéknél keresse meg a használati adatokat. Néhány példa:

- Virtual machines (Virtuális gépek)
- Virtuálisgép-méretezési csoportok
- Tároló szolgáltatás
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

A példányok méretének rugalmasságával kapcsolatos további információkért lásd: [virtuális gépek méretének rugalmassága fenntartott VM](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)-példányokkal.

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése
A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia.

A használati adatok a használati fájlban és az API-kon érhetők el. Ezeket együtt használva meghatározhatja, hogy melyik foglalást szeretné megvásárolni. Ellenőrizze, hogy vannak-e napi szinten magas kihasználtságú virtuálisgép-példányok a megvásárolni kívánt foglalások mennyiségének meghatározásához.

Kerülje a `Meter` használati adatok alkategóriáját és `Product` mezőit. Nem tesznek különbséget a Premium Storage-t használó virtuális gépek méretei között. Ha ezeket a mezőket használja a foglalás megvásárlásához szükséges virtuális gép méretének meghatározásához, akkor a mérete nem megfelelő. Ezután nem fogja megkapni a várt foglalási kedvezményt. Ehelyett tekintse `AdditionalInfo` meg a használati fájl vagy a használati API mezőjét a virtuális gép megfelelő méretének meghatározásához.

### <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott VM-példányok a legtöbb virtuálisgép-mérethez érhetők el, néhány kivétellel. A foglalási kedvezmények nem vonatkoznak a következő virtuális gépekre:

- **VM-sorozat** – sorozat, Av2 sorozat vagy G sorozat.

- **Előzetes** verzióban elérhető virtuális gépek – minden olyan VM-sorozat vagy-méret, amely előzetes verzióban érhető el.

- **Felhők** – a foglalások nem vásárolhatók meg németországi vagy kínai régiókban.

- Nem **elegendő kvóta** – egy adott előfizetésre vonatkozó foglalásnak az új ri-előfizetésben elérhető vCPU-kvótával kell rendelkeznie. Ha például a célként megadott előfizetés 10 vCPU a D sorozathoz, akkor nem vásárolhat le 11 Standard_D1-példányra vonatkozó foglalást. A foglalások kvótájának keresése magában foglalja az előfizetésben már üzembe helyezett virtuális gépeket. Ha például az előfizetés 10 vCPU rendelkezik a D sorozathoz, és két standard_D1 példánya van telepítve, akkor az előfizetésben 10 standard_D1-példány foglalását is megvásárolhatja. A probléma megoldásához [létrehozhat árajánlat](../articles/azure-supportability/resource-manager-core-quotas-request.md) -növelési kérést.

- **Kapacitás korlátozásai** – ritka körülmények között az Azure korlátozza a virtuálisgép-méretek részhalmazára vonatkozó új foglalások megvásárlását a régió alacsony kapacitása miatt.

## <a name="buy-a-reserved-vm-instance"></a>Fenntartott VM-példány vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **a minden szolgáltatás** > **foglalás**lehetőséget.
1. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson a **virtuális gép**elemre.
1. Adja meg a kötelező mezőket. A kiválasztott attribútumoknak megfelelő virtuálisgép-példányok futtatása a foglalási kedvezmény beszerzéséhez. A kedvezményt megkapó virtuálisgép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

| Mező      | Leírás|
|------------|--------------|
|Subscription|A foglalás kifizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a foglalás előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben.|    
|Scope       |A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <ul><li>**Egy erőforráscsoport hatóköre** – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li>**Egyszeri előfizetés hatóköre** – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Nagyvállalati Szerződés ügyfelek esetében a számlázási környezet a beléptetés. Az utólagos elszámolású előfizetések esetében a számlázási hatókör a fiók rendszergazdája által létrehozott összes jogosult előfizetés.</li></ul>|
|Régió    |A foglalás által érintett Azure-régió.|    
|Virtuális gép mérete     |A virtuálisgép-példányok mérete.|
|Optimalizálás a következőhöz:     |A VM-példány méretének rugalmassága alapértelmezés szerint ki van választva. Kattintson a **Speciális beállítások** lehetőségre a példány méretének rugalmassági értékének módosításához, hogy a foglalási kedvezményt más virtuális gépekre alkalmazza ugyanabban a virtuálisgép- [méretezési csoportban](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). A kapacitás prioritása az üzemelő példányok adatközpont-kapacitását rangsorolja. További megbízhatóságot biztosít a virtuálisgép-példányok elindításához, amikor szüksége van rájuk. A kapacitás prioritása csak akkor érhető el, ha a foglalási hatókör egyetlen előfizetés. |
|Kifejezés        |Egy vagy három év.|
|Mennyiség    |A foglalás keretében megvásárolt példányok száma. A mennyiség azon futó virtuálisgép-példányok száma, amelyek megkapják a számlázási kedvezményt. Ha például 10 Standard_D2 virtuális gépet futtat az USA keleti régiójában, akkor a mennyiséget 10-re kell állítani, hogy maximalizálja az összes futó virtuális gép előnyeit. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

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

Ha szeretné módosítani a  módosításokat, lehetősége van a foglalások cseréjére.

## <a name="cancellations-and-exchanges"></a>Megszakítások és cserék

A foglalás lemondásáért 12%-os korai felmondási díj számítható fel. A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számolja. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. A lemondás lekéréséhez lépjen a Azure Portal foglalására, és válassza a **visszatérítés** lehetőséget a támogatási kérelem létrehozásához.

Ha módosítania kell a fenntartott VM-példányok foglalását egy másik régióba, a virtuálisgép-méretezési csoportra vagy a kifejezésre, akkor azt kicserélheti. Az Exchange-nek egy másik, egyenlő vagy nagyobb értékű foglalásnak kell lennie. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az új foglalás létrehozásakor az egy vagy három éves időszak kezdődik. Az Exchange igényléséhez lépjen a foglalás a Azure Portal, majd válassza az **Exchange** elemet a támogatási kérelem létrehozásához.

A foglalások cseréjével és visszatérítésével kapcsolatos további információkért lásd: a [foglalási cserék és](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)a visszafizetések.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezeléséről a [Azure Reservations kezelése](../articles/billing/billing-manage-reserved-vm-instance.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
    - [Mi a Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Fenntartások kezelése az Azure-ban](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazási módjának megismerése](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Az előfizetések foglalási használatának ismertetése az utólagos elszámolású díjszabással](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalási használatának ismertetése](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [A Windows-szoftverek nem tartalmazzák a foglalásokat](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations a partner Center Cloud Solution Provider (CSP) programban](https://docs.microsoft.com/partner-center/azure-reservations)
