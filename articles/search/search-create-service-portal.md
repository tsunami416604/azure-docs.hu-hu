---
title: 'Rövid útmutató: Keresési szolgáltatás létrehozása a portálon'
titleSuffix: Azure Cognitive Search
description: Ebben a portálon rövid útmutató, megtudhatja, hogyan állíthat be egy Azure Cognitive Search erőforrás az Azure Portalon. Válassza ki az erőforráscsoportokat, régiókat és termékváltozatokat vagy tarifacsomagot.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77209358"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search szolgáltatást a portálon

Az Azure Cognitive Search egy önálló erőforrás, amely a keresési élményt egyéni alkalmazásokba csatlakoztatja. Az Azure Cognitive Search könnyen integrálható más Azure-szolgáltatásokkal, a hálózati kiszolgálókon lévő alkalmazásokkal vagy más felhőplatformokon futó szoftverekkel.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy erőforrást az [Azure Portalon.](https://portal.azure.com/)

[![Animált GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Szívesebben használja a PowerShellt? Használja az Azure Resource Manager [szolgáltatássablonját](https://azure.microsoft.com/resources/templates/101-azure-search-create/). Az első lépésekhez az [Azure Cognitive Search kezelése a PowerShell segítségével.](search-manage-powershell.md)

## <a name="subscribe-free-or-paid"></a>Feliratkozás (ingyenes vagy fizetős)

[Nyisson meg egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), és fordítsa az ingyenes krediteket a fizetős Azure-szolgáltatások kipróbálására. A kreditek felhasználása után megtarthatja a fiókot, és továbbra is használhatja az olyan ingyenes Azure-szolgáltatásokat, mint a Websites. A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Választhatja az [MSDN-előfizetői értékelemek aktiválását](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) is. MSDN-előfizetéssel havonta kap fizetős Azure-szolgáltatásokra fordítható krediteket. 

## <a name="find-azure-cognitive-search"></a>Az Azure kognitív keresésének keresése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson a pluszjelre ("+ Erőforrás létrehozása") a bal felső sarokban.
3. A keresősáv segítségével keresse meg az "Azure Cognitive Search" kifejezést, vagy keresse meg az erőforrást a **Web** > **Azure Cognitive Search segítségével.**

![Erőforrás létrehozása a portálon](./media/search-create-service-portal/find-search3.png "Erőforrás létrehozása a portálon")

## <a name="choose-a-subscription"></a>Előfizetés kiválasztása

Ha egynél több előfizetéssel rendelkezik, válasszon egyet a keresési szolgáltatásához.

## <a name="set-a-resource-group"></a>Erőforráscsoport beállítása

Az erőforráscsoport egy tároló, amely az Azure-megoldás kapcsolódó erőforrásait tárolja. A keresési szolgáltatáshoz szükséges. Hasznos lehet az erőforrások teljes körű kezeléséhez is, beleértve a költségeket is. Egy erőforráscsoport állhat egy szolgáltatásból vagy több együtt használt szolgáltatásból. Ha például az Azure Cognitive Search használatával indexel egy Azure Cosmos DB-adatbázist, mindkét szolgáltatást ugyanahhoz az erőforráscsoporthoz rendelheti felügyeleti célokra. 

Ha nem egyetlen csoportba egyesíti az erőforrásokat, vagy ha a meglévő erőforráscsoportok nincsenek kitöltve a nem kapcsolódó megoldásokban használt erőforrásokkal, hozzon létre egy új erőforráscsoportot csak az Azure Cognitive Search erőforráshoz. 

![Új erőforráscsoport létrehozása](./media/search-create-service-portal/new-resource-group.png "Új erőforráscsoport létrehozása")

Idővel nyomon követheti az aktuális és a tervezett költségeket, vagy megtekintheti az egyes erőforrások díjait. A következő képernyőképen látható, hogy milyen típusú költségadatokat láthat, ha több erőforrást egyesít egy csoportba.

![Költségek kezelése erőforráscsoport szintjén](./media/search-create-service-portal/resource-group-cost-management.png "Költségek kezelése erőforráscsoport szintjén")

> [!TIP]
> Az erőforráscsoportok egyszerűsítik a karbantartást, mivel a csoport törlése törli a benne lévő összes szolgáltatást. Több szolgáltatást igénybe vevő prototípus-projektek végén könnyebb mindent törölni, ha az összeset egy erőforráscsoportban helyezi el.

## <a name="name-the-service"></a>A szolgáltatás elnevezése

Az példány részletei mezőben adjon meg egy szolgáltatásnevet az **URL-cím** mezőben. A név annak az URL-végpontnak a része, amelyhez API-hívásokat adnak ki: `https://your-service-name.search.windows.net`. Ha például azt szeretné, hogy `https://myservice.search.windows.net`a végpont `myservice`legyen, írja be a értéket.

A szolgáltatásnévre vonatkozó követelmények:

* Egyedinek kell lennie a search.windows.net névtérben
* 2 és 60 karakter közötti hosszúságúnak kell lennie.
* Kisbetűket, számjegyeket vagy kötőjeleket ("-") kell használnia.
* Ne használjon kötőjeleket ("-") az első 2 karakterben vagy az utolsó karakterként
* Nem használhat egymást követő kötőjeleket ("--") sehol

> [!TIP]
> Ha úgy gondolja, hogy több szolgáltatást fog használni, javasoljuk, hogy a régiót (vagy helyet) a szolgáltatás nevében elnevezési konvencióként adja meg. Az azonos régión belüli szolgáltatások díjmentesen cserélhetnek adatokat, így ha az Azure Cognitive Search az `mysearchservice-westus` USA nyugati régiójában található, és más szolgáltatásai is vannak az USA nyugati régiójában, egy név, mint például mentheti meg az utat a tulajdonságok oldalára, amikor eldönti, hogyan egyesítheti vagy csatolhatja az erőforrásokat.

## <a name="choose-a-location"></a>Hely kiválasztása

Azure-szolgáltatásként az Azure Cognitive Search a világ minden táján üzemeltethető adatközpontokban. A támogatott régiók listája megtalálható az [árképzési oldalon.](https://azure.microsoft.com/pricing/details/search/) 

A sávszélesség-díjak at minimalizálhatja vagy elkerülheti, ha ugyanazt a helyet választja több szolgáltatáshoz. Ha például egy másik Azure-szolgáltatás (Azure Storage, Azure Cosmos DB, Azure SQL Database) által biztosított adatokat indexel, az Azure Cognitive Search szolgáltatás létrehozása ugyanabban a régióban elkerüli a sávszélesség-díjakat (a kimenő adatokért nem kell díjat fizetni, ha szolgáltatások ugyanabban a régióban találhatók).

Ha a ai-bővítés, hozza létre a keresési szolgáltatás ugyanabban a régióban, mint a Cognitive Services. *Az Azure Cognitive Search és Cognitive Services azonos régióbeli közös helye a a i.aI gazdagításának követelménye.*

> [!Note]
> Közép-India jelenleg nem érhető el az új szolgáltatások. A már Közép-Indiában már szolgáltatások esetén korlátozás nélkül skálázható, és a szolgáltatás teljes mértékben támogatott az adott régióban. A korlátozás ebben a régióban ideiglenes, és csak az új szolgáltatásokra korlátozódik. Ezt a feljegyzést eltávolítjuk, ha a korlátozás már nem érvényes.

## <a name="choose-a-pricing-tier-sku"></a>Tarifacsomag (Termékváltozat) kiválasztása

[Az Azure Cognitive Search jelenleg több díjszabási szinten érhető el:](https://azure.microsoft.com/pricing/details/search/)ingyenes, alapszintű vagy standard. Mindegyik szintet saját [kapacitás és korlátozások](search-limits-quotas-capacity.md) jellemzik. Útmutatást talál a [Tarifacsomag vagy SKU kiválasztása](search-sku-tier.md) című cikkben.

Alapszintű és standard a leggyakoribb választás az éles számítási feladatok, de a legtöbb ügyfél kezdődik az ingyenes szolgáltatás. A rétegek közötti főbb különbségek a partíció mérete és sebessége, valamint a létrehozható objektumok számának korlátozása.

Ne feledje, hogy a szolgáltatási szint létrehozása után nem módosítható. Ha magasabb vagy alacsonyabb szintre van szüksége, újra létre kell hoznia a szolgáltatást.

## <a name="create-your-service"></a>A szolgáltatás létrehozása

Miután megadta a szükséges bemeneteket, folytassa a szolgáltatás létrehozása. 

![A szolgáltatás áttekintése és létrehozása](./media/search-create-service-portal/new-service3.png "A szolgáltatás áttekintése és létrehozása")

A szolgáltatás perceken belül üzembe kerül. Az Azure-értesítések en keresztül figyelheti a folyamatot. Fontolja meg a szolgáltatás rögzítését az irányítópulton, hogy a jövőben könnyen hozzáférhessen.

![A szolgáltatás figyelése és rögzítése](./media/search-create-service-portal/monitor-notifications.png "A szolgáltatás figyelése és rögzítése")

## <a name="get-a-key-and-url-endpoint"></a>Kulcs és URL-végpont beszerezni

Ha csak a portált használja, az új szolgáltatáshoz való programozott hozzáférés megköveteli, hogy megadja az URL-végpontot és egy hitelesítési api-kulcsot.

1. Az **Áttekintés** lapon keresse meg és másolja az URL-végpontot a lap jobb oldalán.

2. A **Kulcsok** lapon másolja az egyik felügyeleti kulcsot (azok egyenértékűek). Rendszergazdai api-kulcsok szükségesek a szolgáltatás objektumainak létrehozásához, frissítéséhez és törléséhez. Ezzel szemben a lekérdezési kulcsok olvasási hozzáférést biztosítanak az indextartalomhoz.

   ![Szolgáltatás áttekintő lapja URL-végpontdal](./media/search-create-service-portal/get-url-key.png "URL-végpont és egyéb szolgáltatásadatok")

A portálalapú feladatokhoz nincs szükség végpontra és kulcsra. A portál már kapcsolódik az Azure Cognitive Search erőforrás felügyeleti jogosultságokkal. A portál forgatókönyve, kezdje [a rövid útmutató: Hozzon létre egy Azure Cognitive Search index a portálon.](search-get-started-portal.md)

## <a name="scale-your-service"></a>A szolgáltatás méretezése

A szolgáltatást annak üzembe helyezése után igényei szerint méretezheti. Ha az Azure Cognitive Search szolgáltatás standard szintjét választotta, a szolgáltatást két dimenzióban skálázhatja: replikák és partíciók. Ha az alapszintű csomagot választotta volna, akkor csak replikákat adhatna hozzá. Ingyenes szolgáltatás üzembe helyezése esetén a méretezés nem elérhető.

A ***partíciókkal*** a szolgáltatás több dokumentum tárolására és keresésére alkalmas.

A ***replikákkal*** a szolgáltatás több keresési kérelem kiszolgálására alkalmas.

Az erőforrások hozzáadása növeli a havi költségeket. A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segíthet áttekinteni az erőforrások hozzáadásának számlázási vonatkozásait. Vegye figyelembe, hogy az erőforrások mennyisége a terheléshez igazítható. Például növelheti az erőforrások mennyiségét egy teljes körű első index létrehozásához, majd később csökkentheti azt a növekményes indexeléshez jobban megfelelő szintre.

> [!Important]
> A szolgáltatásnak [csak olvasható SLA esetén 2, írási/olvasási SLA esetén 3 replikával](https://azure.microsoft.com/support/legal/sla/search/v1_0/) kell rendelkeznie.

1. Nyissa meg a keresési szolgáltatás oldalát az Azure Portalon.
2. A bal oldali navigációs ablaktáblán válassza a **Beállítások** > **méretezése**lehetőséget.
3. A csúszka segítségével bármelyik típusú erőforrásokat hozzáadhatja.

![Kapacitás hozzáadása](./media/search-create-service-portal/settings-scale.png "Kapacitás hozzáadása replikák és partíciók között")

> [!Note]
> Partíciónkénti tárolás és a sebesség magasabb szinteken növekszik. További információt a kapacitás és a korlátok című témakörben [talál.](search-limits-quotas-capacity.md)

## <a name="when-to-add-a-second-service"></a>Mikor érdemes hozzáadni egy második szolgáltatást?

A legtöbb ügyfél csak egy olyan szolgáltatást használ, amelyet egy olyan szinten osztanak ki, amely biztosítja az [erőforrások megfelelő egyensúlyát](search-sku-tier.md). Egy szolgáltatásban több index is fenntartható [a választott csomag korlátozásainak megfelelő számban](search-capacity-planning.md), ha elkülönülnek egymástól. Az Azure Cognitive Search, kérelmek csak egy index, minimálisra csökkentheti annak az esélyét, véletlen vagy szándékos adatok lekérése más indexek ugyanabban a szolgáltatásban.

Bár az ügyfelek többsége csak egy szolgáltatást használ, szükség lehet a szolgáltatás redundanciájára, ha az üzemeltetési körülmények között az alábbiak szerepelnek:

* Vészhelyreállítás (adatközpont-kimaradás). Az Azure Cognitive Search nem biztosít azonnali feladatátvételt kimaradás esetén. Javaslatokat és útmutatást a [Szolgáltatásfelügyeletről](search-manage.md) szóló cikkben talál.
* A több-bérlős modellezés vizsgálatával megállapította, hogy az optimális kialakítás további szolgáltatásokat is tartalmaz. További információ: [Tervezés több bérlő esetén](search-modeling-multitenant-saas-applications.md).
* Globálisan telepített alkalmazások esetén előfordulhat, hogy több régióban is szüksége van az Azure Cognitive Search egy példányára az alkalmazás nemzetközi forgalmának késésének minimalizálása érdekében.

> [!NOTE]
> Az Azure Cognitive Search, nem különíthető indexelési és lekérdezési műveletek; így soha nem hozna létre több szolgáltatást a szegregált számítási feladatokhoz. Az indexek lekérdezése mindig abban a szolgáltatásban történik, amelyben létre lettek hozva (nem lehet egy indexet egy szolgáltatásban létrehozni és egy másikba átmásolni).

A magas rendelkezésre álláshoz nincs szükség második szolgáltatásra. A lekérdezések magas rendelkezésre állása úgy érhető el, hogy egyazon szolgáltatáson belül 2 vagy több replikát használ. A replikafrissítések szekvenciálisak, ami azt jelenti, hogy legalább egy működésbe lép a szolgáltatásfrissítés üzembe helyezéskor. Az üzemidőről további információt a [Szolgáltatásiszint-szerződések című](https://azure.microsoft.com/support/legal/sla/search/v1_0/)témakörben talál.

## <a name="next-steps"></a>További lépések

A szolgáltatás kiépítése után folytathatja a portálon az első index létrehozásához.

> [!div class="nextstepaction"]
> [Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet a portálon](search-get-started-portal.md)
