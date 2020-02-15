---
title: 'Gyors útmutató: keresési szolgáltatás létrehozása a portálon'
titleSuffix: Azure Cognitive Search
description: Ebből a portálból megtudhatja, hogyan állíthat be egy Azure Cognitive Search-erőforrást a Azure Portalban. Válassza az erőforráscsoportok, a régiók, az SKU vagy az árképzési szintet.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209358"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Rövid útmutató: Azure Cognitive Search szolgáltatás létrehozása a portálon

Az Azure Cognitive Search egy önálló erőforrás, amely a keresési élmény egyéni alkalmazásokba való csatlakoztatására szolgál. Az Azure Cognitive Search egyszerűen integrálható más Azure-szolgáltatásokkal, a hálózati kiszolgálókon futó alkalmazásokkal vagy más felhőalapú platformokon futó szoftverekkel.

Ebből a cikkből megtudhatja, hogyan hozhat létre erőforrásokat a [Azure Portalban](https://portal.azure.com/).

[Animált GIF ![](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Szívesebben használja a PowerShellt? Használja az Azure Resource Manager [szolgáltatássablonját](https://azure.microsoft.com/resources/templates/101-azure-search-create/). Az első lépésekkel kapcsolatos segítségért lásd: az [Azure Cognitive Search kezelése a PowerShell](search-manage-powershell.md)-lel.

## <a name="subscribe-free-or-paid"></a>Feliratkozás (ingyenes vagy fizetős)

[Nyisson meg egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), és fordítsa az ingyenes krediteket a fizetős Azure-szolgáltatások kipróbálására. A kreditek felhasználása után megtarthatja a fiókot, és továbbra is használhatja az olyan ingyenes Azure-szolgáltatásokat, mint a Websites. A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Választhatja az [MSDN-előfizetői értékelemek aktiválását](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) is. MSDN-előfizetéssel havonta kap fizetős Azure-szolgáltatásokra fordítható krediteket. 

## <a name="find-azure-cognitive-search"></a>Azure-Cognitive Search keresése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Kattintson a plusz jelre ("+ Create Resource") a bal felső sarokban.
3. A keresősáv segítségével keresse meg az "Azure Cognitive Search", vagy navigáljon az erőforráshoz a **Web** > **Azure Cognitive Search**használatával.

![Erőforrás létrehozása a portálon](./media/search-create-service-portal/find-search3.png "Erőforrás létrehozása a portálon")

## <a name="choose-a-subscription"></a>Előfizetés kiválasztása

Ha egynél több előfizetéssel rendelkezik, válasszon egyet a keresési szolgáltatáshoz.

## <a name="set-a-resource-group"></a>Erőforráscsoport beállítása

Az erőforráscsoportok olyan tárolók, amelyek az Azure-megoldáshoz kapcsolódó erőforrásokat tárolnak. A keresési szolgáltatáshoz szükséges. Emellett az erőforrások teljes körű felügyeletére is hasznos, beleértve a költségeket is. Egy erőforráscsoport állhat egy szolgáltatásból vagy több, együtt használt szolgáltatásból is. Ha például Azure Cognitive Searcht használ egy Azure Cosmos DB-adatbázis indexeléséhez, akkor az adott erőforráscsoport mindkét szolgáltatását felügyeleti célokra is elvégezheti. 

Ha egyetlen csoporthoz sem egyesít erőforrásokat, vagy ha a meglévő erőforráscsoportok a nem kapcsolódó megoldásokban használt erőforrásokkal vannak kitöltve, hozzon létre egy új erőforráscsoportot az Azure Cognitive Search-erőforráshoz. 

![Új erőforráscsoport létrehozása](./media/search-create-service-portal/new-resource-group.png "Új erőforráscsoport létrehozása")

Idővel nyomon követheti az aktuális és a tervezett költségeket, vagy megtekintheti az egyes erőforrások díját. Az alábbi képernyőképen látható, hogy milyen költségadatok várhatóak, ha több erőforrást egyesít egy csoportba.

![Költségek kezelése az erőforráscsoport szintjén](./media/search-create-service-portal/resource-group-cost-management.png "Költségek kezelése az erőforráscsoport szintjén")

> [!TIP]
> Az erőforráscsoportok leegyszerűsítik a tisztítást, mert egy csoport törlése törli a benne lévő összes szolgáltatást. Több szolgáltatást igénybe vevő prototípus-projektek végén könnyebb mindent törölni, ha az összeset egy erőforráscsoportban helyezi el.

## <a name="name-the-service"></a>A szolgáltatás neve

A példány részletei területen adja meg a szolgáltatás nevét az **URL** mezőben. A név annak az URL-végpontnak a része, amelyen az API-hívások ki vannak bocsátva: `https://your-service-name.search.windows.net`. Ha például azt szeretné, hogy a végpont `https://myservice.search.windows.net`legyen, adja meg `myservice`.

A szolgáltatásnévre vonatkozó követelmények:

* Egyedinek kell lennie a search.windows.net névtérben
* Hosszának 2 és 60 karakter közöttinek kell lennie
* Kisbetűket, számokat vagy kötőjeleket ("-") kell használnia
* Ne használjon kötőjeleket ("-") az első 2 karakterben, vagy az utolsó egy karaktert.
* Bárhol nem használhat egymást követő kötőjeleket ("--")

> [!TIP]
> Ha úgy gondolja, hogy több szolgáltatást fog használni, javasoljuk, hogy a szolgáltatás neveként a régiót (vagy helyet) nevezze el elnevezési konvencióként. Az azonos régión belüli szolgáltatások díjmentesen cserélhetik az adatcserét, így ha az Azure Cognitive Search az USA nyugati régiójában van, és más szolgáltatásokkal is rendelkezik az USA nyugati régiójában, akkor a név, például a `mysearchservice-westus` mentheti a Tulajdonságok lapját, amikor az erőforrások egyesítésével vagy csatlakoztatásával dönt.

## <a name="choose-a-location"></a>Válasszon helyet

Azure-szolgáltatásként az Azure Cognitive Search világszerte elérhető adatközpontokban üzemeltethető. A támogatott régiók listája a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. 

A sávszélességgel kapcsolatos díjak minimalizálásához vagy elkerüléséhez válassza ki ugyanazt a helyet több szolgáltatás számára. Ha például egy másik Azure-szolgáltatás (Azure Storage, Azure Cosmos DB, Azure SQL Database) által biztosított adatok indexelését végzi, az Azure Cognitive Search szolgáltatás ugyanabban a régióban való létrehozása elkerüli a sávszélességi díjakat (a kimenő adatokért nem számítunk fel díjat, ha a szolgáltatások ugyanabban a régióban találhatók).

Ha AI-bővítést használ, hozza létre a keresési szolgáltatást ugyanabban a régióban, mint Cognitive Services. *Az Azure Cognitive Search és az ugyanazon régióban lévő Cognitive Services együttes elhelyezése az AI-bővítés követelménye*.

> [!Note]
> Közép-India jelenleg nem érhető el az új szolgáltatásokhoz. Az India középső régiójában már meglévő szolgáltatások esetében korlátozás nélkül is méretezhető, és a szolgáltatás teljes mértékben támogatott az adott régióban. A régió korlátozásai ideiglenesek, és csak az új szolgáltatásokra korlátozódnak. Ezt a megjegyzést akkor távolítjuk el, ha a korlátozás már nem érvényes.

## <a name="choose-a-pricing-tier-sku"></a>Válasszon árképzési szintet (SKU)

Az [Azure Cognitive Search jelenleg több díjszabási szinten is elérhető](https://azure.microsoft.com/pricing/details/search/): ingyenes, alapszintű vagy standard. Mindegyik szintet saját [kapacitás és korlátozások](search-limits-quotas-capacity.md) jellemzik. Útmutatást talál a [Tarifacsomag vagy SKU kiválasztása](search-sku-tier.md) című cikkben.

Az alapszintű és a standard az éles munkaterhelések leggyakoribb lehetőségei, de a legtöbb ügyfél az ingyenes szolgáltatással kezdődik. A szintek közötti fő különbségek a partíció mérete és sebessége, valamint a létrehozható objektumok számának korlátai.

Ne feledje, hogy a szolgáltatás létrehozása után nem módosítható az árképzési szintek. Ha magasabb vagy alacsonyabb szintű kapacitásra van szüksége, akkor újra létre kell hoznia a szolgáltatást.

## <a name="create-your-service"></a>A szolgáltatás létrehozása

Miután megadta a szükséges bemeneteket, lépjen tovább, és hozza létre a szolgáltatást. 

![A szolgáltatás áttekintése és létrehozása](./media/search-create-service-portal/new-service3.png "A szolgáltatás áttekintése és létrehozása")

A szolgáltatás percek alatt üzembe helyezhető. Az Azure-értesítéseken keresztül nyomon követheti a folyamat előrehaladását. Érdemes lehet a szolgáltatást az irányítópultra rögzíteni, hogy később könnyebben hozzáférhessen.

![A szolgáltatás monitorozása és rögzítése](./media/search-create-service-portal/monitor-notifications.png "A szolgáltatás monitorozása és rögzítése")

## <a name="get-a-key-and-url-endpoint"></a>Kulcs és URL-cím végpontjának beolvasása

Ha nem használja a portált, az új szolgáltatáshoz való programozott hozzáféréshez az URL-végpontot és egy hitelesítési API-kulcsot kell megadnia.

1. Az **Áttekintés** oldalon keresse meg és másolja ki az URL-végpontot az oldal jobb oldalán.

2. A **kulcsok** lapon másolja át a rendszergazdai kulcsok egyikét (ezek egyenértékűek). Felügyeleti API-kulcsok szükségesek a szolgáltatásban található objektumok létrehozásához, frissítéséhez és törléséhez. Ezzel szemben a lekérdezési kulcsok olvasási hozzáférést biztosítanak a tartalom indexeléséhez.

   ![A szolgáltatás áttekintő lapja URL-végponttal](./media/search-create-service-portal/get-url-key.png "URL-végpont és egyéb szolgáltatás részletei")

A portálon alapuló feladatokhoz nincs szükség végpontra és kulcsra. A portál már társítva van az Azure Cognitive Search-erőforráshoz rendszergazdai jogosultságokkal. A portál bemutatójának első lépései a gyors üzembe helyezési útmutató [: Azure Cognitive Search index létrehozása a portálon](search-get-started-portal.md).

## <a name="scale-your-service"></a>A szolgáltatás méretezése

A szolgáltatást annak üzembe helyezése után igényei szerint méretezheti. Ha az Azure Cognitive Search szolgáltatás standard csomagját választotta, akkor a szolgáltatás két dimenzióban méretezhető: replikák és partíciók. Ha az alapszintű csomagot választotta volna, akkor csak replikákat adhatna hozzá. Ingyenes szolgáltatás üzembe helyezése esetén a méretezés nem elérhető.

A ***partíciókkal*** a szolgáltatás több dokumentum tárolására és keresésére alkalmas.

A ***replikákkal*** a szolgáltatás több keresési kérelem kiszolgálására alkalmas.

Az erőforrások hozzáadása növeli a havi költségeket. A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segíthet áttekinteni az erőforrások hozzáadásának számlázási vonatkozásait. Vegye figyelembe, hogy az erőforrások mennyisége a terheléshez igazítható. Például növelheti az erőforrások mennyiségét egy teljes körű első index létrehozásához, majd később csökkentheti azt a növekményes indexeléshez jobban megfelelő szintre.

> [!Important]
> A szolgáltatásnak [csak olvasható SLA esetén 2, írási/olvasási SLA esetén 3 replikával](https://azure.microsoft.com/support/legal/sla/search/v1_0/) kell rendelkeznie.

1. Nyissa meg a keresési szolgáltatás oldalát az Azure Portalon.
2. A bal oldali navigációs panelen válassza a **Beállítások** > **Méretezés** lehetőséget.
3. A csúszka segítségével bármelyik típusú erőforrásokat hozzáadhatja.

![Kapacitás hozzáadása](./media/search-create-service-portal/settings-scale.png "Kapacitás hozzáadása replikák és partíciók használatával")

> [!Note]
> A partíción belüli tárolás és a gyorsabb növekedés magasabb szintű szinten történik. További információ: [kapacitás és korlátok](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Mikor érdemes hozzáadni egy második szolgáltatást?

A legtöbb ügyfél csak egy olyan szolgáltatást használ, amely az [erőforrások megfelelő egyensúlyát](search-sku-tier.md)biztosító szinten van kiépítve. Egy szolgáltatásban több index is fenntartható [a választott csomag korlátozásainak megfelelő számban](search-capacity-planning.md), ha elkülönülnek egymástól. Az Azure Cognitive Searchban a kérelmek csak egyetlen indexre irányíthatók, ami minimalizálja az egyazon szolgáltatásban lévő más indexekről érkező véletlen vagy szándékos adatlekérdezés esélyét.

Bár az ügyfelek többsége csak egy szolgáltatást használ, szükség lehet a szolgáltatás redundanciájára, ha az üzemeltetési körülmények között az alábbiak szerepelnek:

* Vészhelyreállítás (adatközpont-kimaradás). Az Azure Cognitive Search áramszünet esetén nem biztosít azonnali feladatátvételt. Javaslatokat és útmutatást a [Szolgáltatásfelügyeletről](search-manage.md) szóló cikkben talál.
* A több-bérlős modellezés vizsgálatával megállapította, hogy az optimális kialakítás további szolgáltatásokat is tartalmaz. További információ: [Tervezés több bérlő esetén](search-modeling-multitenant-saas-applications.md).
* A globálisan telepített alkalmazások esetében előfordulhat, hogy az Azure-Cognitive Search több régióban kell megkövetelni, hogy csökkentse az alkalmazás nemzetközi forgalmának késését.

> [!NOTE]
> Az Azure Cognitive Searchban nem különítheti el az indexelési és lekérdezési műveleteket; így soha nem hozhat létre több szolgáltatást az elkülönített munkaterhelésekhez. Az indexek lekérdezése mindig abban a szolgáltatásban történik, amelyben létre lettek hozva (nem lehet egy indexet egy szolgáltatásban létrehozni és egy másikba átmásolni).

A magas rendelkezésre álláshoz nincs szükség második szolgáltatásra. A lekérdezések magas rendelkezésre állása úgy érhető el, hogy egyazon szolgáltatáson belül 2 vagy több replikát használ. A replika frissítései szekvenciálisak, ami azt jelenti, hogy legalább egy működőképes a szolgáltatás frissítésekor. További információ az üzemidőről: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Következő lépések

A szolgáltatás kiépítés után folytathatja a portálon az első index létrehozását.

> [!div class="nextstepaction"]
> [Rövid útmutató: Azure Cognitive Search index létrehozása a portálon](search-get-started-portal.md)
