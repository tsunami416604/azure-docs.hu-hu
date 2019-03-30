---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/22/2019
ms.openlocfilehash: 32d5d0d25c843be1cba1916e7679faa930e8e645
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671798"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel

Fizessen elő a virtuális gépekhez, és pénzt takaríthat meg az Azure Reserved Virtual Machine (VM) Instances. További információkért lásd: [Azure Reserved VM Instances ajánlat](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Megvásárolhatja a fenntartott VM-példány a [az Azure portal](https://portal.azure.com). Egy példány vásárlása:

- Meg kell egy tulajdonosi szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre.
- Vállalati előfizetés esetén **fenntartott példányok hozzáadása** engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com). Vagy, ha ez a beállítás le van tiltva, az előfizetés egy nagyvállalati szerződés rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök is vásárolhatnak foglalást.

Rendszer automatikusan alkalmazza a foglalási kedvezményt a futó virtuális gépek, amelyek megfelelnek a Foglalás hatóköre és attribútumok számát. Frissítheti a keresztül a foglalási hatókört [az Azure portal](https://portal.azure.com), PowerShell, CLI, vagy az API-n keresztül.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Határozza meg a megfelelő Virtuálisgép-méret, vásárlás előtt

Mielőtt vásárol egy foglalást, meg kell határoznia a szükséges virtuális gép méretét. Az alábbi szakaszok segítségével meghatározhatja, hogy a megfelelő Virtuálisgép-méretet.

### <a name="use-reservation-recommendations"></a>Foglalás javaslatok használata

Foglalás javaslatok használhatja annak meghatározásához, a foglalásokat, meg kell vásárolnia.

- Vásárlási javaslatok és ajánlott mennyiség megjelenítése vannak megvásárlásakor a fenntartott Virtuálisgép-példány az Azure Portalon.
- Az Azure Advisor biztosít az egyes előfizetések vásárlási javaslatok.  
- Az API-k segítségével vásárlási javaslatok megosztott hatókörrel és a egy előfizetési hatókört. További információkért lásd: [fenntartott példány vásárlási javaslatok API-k a vállalati ügyfelek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- A nagyvállalati szerződéssel rendelkező ügyfelek a beszerzési javaslatok megosztott, és a egy előfizetési hatókört érhetők el, a [Azure Consumption Insights Power BI-tartalomcsomag](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése
A használati adatok meghatározásához, hogy melyik foglalásokat, meg kell vásárolnia kell elemezni.

Használati adatok a használati fájlban és API-k érhető el. Ezek segítségével együtt határozza meg, melyik foglalás megvásárlásához. Ellenőrizze a Virtuálisgép-példányok, amelyek magas kihasználtsága alapján napokra lebontva megvásárolni a foglalást mennyiségének meghatározásához.

Kerülje a `Meter` alkategória és `Product` mezők a használati adatok. Ezek nincs megkülönböztetni a premium storage használata Virtuálisgép-méretek. Ha ezek a mezők segítségével állapítja meg, a virtuális gép méretét a foglalásvásárlást, előfordulhat, hogy a megfelelő méretű vásárolhat. Ezután a foglalási kedvezményt várhatóan nem kap. Helyette tekintse meg a `AdditionalInfo` mezőt a használati fájlban vagy a usage API-t a megfelelő virtuális gép méretének meghatározásához.

### <a name="purchase-restriction-considerations"></a>Vásárlás eszközkorlátozásokra vonatkozó szempontok

Fenntartott VM-példányok néhány kivétellel a legtöbb Virtuálisgép-méretek érhetők el. Foglalás kedvezmények a következő virtuális gépek nem érvényesek:

- **Virtuálisgép-sorozatok** -A-sorozatú, az Av2 sorozat vagy G-sorozat.

- **Előzetes verzióban érhető el virtuális gépek** – bármely Virtuálisgép-sorozat vagy -méretet, előzetes verzióként érhető el.

- **Felhők** -foglalások nem érhetők el, német és kínai régiókban vásárolható.

- **A kvóta nem elegendő** – egy foglalást, ami egy előfizetéshez rendelkeznie kell az új fenntartott példány esetében az előfizetés vCPU-kvóta. Például ha a célként megadott előfizetés kvótája 10 vcpu-k számára a D-sorozat, majd, nem vásárolhat egy foglalást, 11 Standard_D1-példányok. A kvóta ellenőrzése lefoglalási a már üzembe helyezte az előfizetésben található virtuális gépeket tartalmaz. Például ha az előfizetés 10 vcpu-k kvótával rendelkezik a D-sorozat esetében, és üzembe helyezett két standard_D1 példánnyal rendelkezik, majd vásárolhat egy foglalást, ebben az előfizetésben 10 standard_D1-példányokhoz. Is [ajánlat növelése kérelem létrehozása](../articles/azure-supportability/resource-manager-core-quotas-request.md) a probléma megoldásához.

- **Kapacitás korlátozások** – a ritka esetekben, az Azure korlátai részére a virtuális gép új foglalások vásárlásának úgy méretezi, egy régióban alacsony kapacitás miatt.

## <a name="buy-a-reserved-vm-instance"></a>A fenntartott VM-példány vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** , egy új foglalást vásárolhat.
4. Adja meg a kötelező mezőket. Futó virtuális Gépre, válassza ki az attribútumok minősítéséhez lekérése a foglalási kedvezményt a megfelelő példányok. A Virtuálisgép-példányok, amely a kedvezmény a tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

    | Mező      | Leírás|
    |------------|--------------|
    |Name (Név)        |A Foglalás neve.|
    |Előfizetés|Kell fizetnem a Foglalás használt előfizetés. A fizetési módot, az előfizetés fel van töltve a Foglalás az előzetes költségek. Az előfizetés típusúnak kell lennie a nagyvállalati szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P) vagy a használatalapú fizetés (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|    
    |Hatókör       |A Foglalás hatóköre egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <ul><li>Ebben az előfizetésben virtuális gépek egyetlen előfizetés – a foglalási kedvezményt alkalmazza. </li><li>Közös – a foglalási kedvezményt alkalmazza a rendszer olyan előfizetéseket, a számlázási környezetben futó virtuális gépek. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és a regisztráció belüli összes előfizetésre tartalmazza. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.</li></ul>|
    |Régió    |Az Azure-régió, amelyet a Foglalás jelez.|    
    |Virtuális gép mérete     |A Virtuálisgép-példányok méretétől.|
    |Optimalizálás:     |Virtuálisgép-példányok rugalmassága mérete alkalmazza a foglalási kedvezményt többi virtuális géphez ugyanazon [VM-méret csoport](https://aka.ms/RIVMGroups). Kapacitás prioritása priorizálja az Adatközpont-kapacitás az üzemelő példányok. Ez kínál a további megbízhatósági arra, hogy indítsa el a Virtuálisgép-példányok, amikor szüksége van rájuk. Kapacitás prioritása csak akkor használható, ha a Foglalás hatóköre egy előfizetés. |
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |A Foglalás belül megvásárolt példányok száma. A mennyiség a futó kérheti le a számlázási kedvezményt Virtuálisgép-példányok számát. Például ha 10 Standard_D2 virtuális gépeket futtat az USA keleti régiójában, majd kell megadni mennyiség 10 az összes futó gépek juttatása maximalizálása érdekében. |
5. Megtekintheti a fenntartás költsége kiválasztásakor **költség kiszámítása**.

    ![A foglalásvásárlás elküldése előtt képernyőképe](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Válassza a **Beszerzés** lehetőséget.
7. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

    ![A foglalásvásárlás elküldése után képernyőképe](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="change-a-reservation-after-purchase"></a>Egy foglalás megvásárlása után módosítása

Egy foglalás megvásárlása után a következő típusú módosításokat végezhet:

- Frissítse a Foglalás hatóköre
- Példány mérete rugalmasan (ha van)
- Tulajdonjog

Is oszthatja a kisebb méretű adattömböket és foglalások már fel egyesítéses foglalást. A módosítások egyike miatt egy új kereskedelmi ügylet, vagy módosítsa a foglalási záró dátumát.

Miután megvásárolta, közvetlenül a következő típusú módosításokat nem teheti:

- Egy meglévő foglalás régió
- SKU
- Mennyiség
- Időtartam

Azonban továbbra is *exchange* egy foglalást, ha módosítani szeretné.

## <a name="cancellations-and-exchanges"></a>Lemondás és cseréje

A foglalás lemondásáért 12%-os korai felmondási díj számítható fel. A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számolja. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. Visszavonást, nyissa meg az Azure Portalon, és válassza a Foglalás **visszatérítés** támogatási kérelmet szeretne létrehozni.

Ha a fenntartott virtuálisgép-példányok foglalása kapcsán módosítania kell a régiót, a virtuális gép méretcsoportját vagy az időszakot, akkor kicserélheti a foglalást egy azonos vagy magasabb értékűre. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves távon elindítja az új Foglalás létrehozásakor. Egy exchange kérést, nyissa meg a foglalást az Azure Portalon, és válassza **Exchange** támogatási kérelmet szeretne létrehozni.

Exchange-hez vagy a visszatérítés foglalások kapcsolatos további információkért lásd: [foglalás cseréje és a visszatérítéseket](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](../articles/billing/billing-manage-reserved-vm-instance.md).
- Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mik az Azure-foglalásokat?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Az Azure-ban foglalások kezelése](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [A használatalapú fizetéses előfizetést foglalás használati adatai](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [A nagyvállalati beléptetés foglalás használati adatai](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Windows szoftverek díjait nem tartalmazza a foglalások](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)
