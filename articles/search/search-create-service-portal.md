---
title: Azure Search szolgáltatás létrehozása a portálon – Azure Search
description: Azure Search erőforrás kiépítése a Azure Portal. Válassza az erőforráscsoportok, a régiók, az SKU vagy az árképzési szintet.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0649fea0b598ffaaaf2611c9d1324174105ee5d4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931539"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Azure Search szolgáltatás létrehozása a portálon

A Azure Search egy önálló erőforrás, amely az egyéni alkalmazások keresési funkcióinak csatlakoztatására szolgál. Bár a Azure Search egyszerűen integrálható más Azure-szolgáltatásokkal, önálló összetevőként is használható, vagy integrálható a hálózati kiszolgálókon lévő alkalmazásokkal, illetve más felhőalapú platformokon futó szoftverekkel.

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Search erőforrást a [Azure Portalban](https://portal.azure.com/).

[![Animált GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Szívesebben használja a PowerShellt? Használja az Azure Resource Manager [szolgáltatássablonját](https://azure.microsoft.com/resources/templates/101-azure-search-create/). Segítség az első lépésekhez: [Azure Search kezelése a PowerShell](search-manage-powershell.md)-lel.

## <a name="subscribe-free-or-paid"></a>Feliratkozás (ingyenes vagy fizetős)

[Nyisson meg egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), és fordítsa az ingyenes krediteket a fizetős Azure-szolgáltatások kipróbálására. A kreditek felhasználása után megtarthatja a fiókot, és továbbra is használhatja az olyan ingyenes Azure-szolgáltatásokat, mint a Websites. A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Választhatja az [MSDN-előfizetői értékelemek aktiválását](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) is. MSDN-előfizetéssel havonta kap fizetős Azure-szolgáltatásokra fordítható krediteket. 

## <a name="find-azure-search"></a>Az Azure Search megkeresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a plusz jelre ("+ Create Resource") a bal felső sarokban.
3. A keresősáv használatával keresse meg a "Azure Search", vagy keresse meg az erőforrást a **webes** > **Azure Searchon**keresztül.

![Navigáljon egy Azure Search erőforráshoz](./media/search-create-service-portal/find-search3.png "Azure Search navigációs útvonala")

## <a name="select-a-subscription"></a>Válasszon egy előfizetést

Ha egynél több előfizetéssel rendelkezik, akkor olyat válasszon, amelyhez adat- vagy fájltárolási szolgáltatások is tartoznak. Az Azure Search képes automatikusan felderíteni az Azure Table és a blob Storage, a SQL Database [](search-indexer-overview.md)és az Azure Cosmos db indexelést az indexelő használatával, de csak az azonos előfizetésben lévő szolgáltatásokhoz.

## <a name="set-a-resource-group"></a>Erőforráscsoport beállítása

Egy erőforráscsoport szükséges, amely az erőforrások teljes körű felügyeletéhez használható, beleértve a költséghatékonyságot is. Egy erőforráscsoport állhat egy szolgáltatásból vagy több, együtt használt szolgáltatásból is. Ha például Azure Searcht használ egy Azure Cosmos DB-adatbázis indexeléséhez, akkor az adott erőforráscsoport mindkét szolgáltatását felügyeleti célokra is elvégezheti. 

Ha egyetlen csoporthoz sem egyesít erőforrásokat, vagy ha a meglévő erőforráscsoportok a nem kapcsolódó megoldásokban használt erőforrásokkal vannak kitöltve, hozzon létre egy új erőforráscsoportot a Azure Search erőforráshoz. 

A szolgáltatás használata során nyomon követheti az aktuális és a tervezett költségeket (a képernyőképen látható módon), vagy görgessen le az egyes erőforrások díjainak megtekintéséhez.

![Költségek kezelése az erőforráscsoport szintjén](./media/search-create-service-portal/resource-group-cost-management.png "Költségek kezelése az erőforráscsoport szintjén")

> [!TIP]
> Egy erőforráscsoport törlésekor a benne lévő szolgáltatások is törölve lesznek. Több szolgáltatást igénybe vevő prototípus-projektek végén könnyebb mindent törölni, ha az összeset egy erőforráscsoportban helyezi el.

## <a name="name-the-service"></a>A szolgáltatás neve

A példány részletei területen adja meg a szolgáltatás nevét az **URL** mezőben. A név annak az URL-végpontnak a része, amelyen az API- `https://your-service-name.search.windows.net`hívások ki vannak bocsátva:. Ha például azt szeretné, hogy a végpont legyen, `https://myservice.search.windows.net`a következőt kell `myservice`megadnia:.

A szolgáltatásnévre vonatkozó követelmények:

* Egyedinek kell lennie a search.windows.net névtérben
* Hossza 2 és 60 karakter között lehet
* Kisbetűkből, számjegyekből vagy kötőjelekből ("-") állhat
* Kerülje a kötőjel ("-") használatát az első 2 és az utolsó karakterben
* Sehol sem tartalmazhat két egymást követő kötőjelet ("--")

> [!TIP]
> Ha úgy gondolja, hogy több szolgáltatást fog használni, javasoljuk, hogy a szolgáltatás neveként a régiót (vagy helyet) nevezze el elnevezési konvencióként. Az azonos régión belüli szolgáltatások díjmentesen cserélhetik az adatcserét, így ha Azure Search az USA nyugati régiójában van, és más szolgáltatásokkal is rendelkezik az USA `mysearchservice-westus` nyugati régiójában, akkor egy olyan név is, mint a tulajdonságok oldalának eldöntése során, hogyan lehet erőforrásokat egyesíteni vagy csatolni.

## <a name="choose-a-location"></a>Hely kiválasztása

Az Azure Search mint Azure-szolgáltatás világszerte sok adatközpontban üzemeltethető. A támogatott régiók listája a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. 

A sávszélességgel kapcsolatos díjak minimalizálásához vagy elkerüléséhez válassza ki ugyanazt a helyet több szolgáltatás számára. Ha például egy másik Azure-szolgáltatás (Azure Storage, Azure Cosmos DB, Azure SQL Database) által biztosított adatok indexelését végzi, a Azure Search szolgáltatás ugyanabban a régióban való létrehozása elkerüli a sávszélességgel kapcsolatos költségeket (a kimenő adatokért nem számítunk fel díjat, ha a szolgáltatások be vannak Ugyanez a régió).

Emellett, ha kognitív keresési AI-bővítést használ, a szolgáltatást a Cognitive Services erőforrással megegyező régióban hozza létre. *Az Azure Search és Cognitive Services közös helye az adott régióban az AI-* bővítés követelménye.

> [!Note]
> Közép-India jelenleg nem érhető el az új szolgáltatásokhoz. Az India középső régiójában már meglévő szolgáltatások esetében korlátozás nélkül is méretezhető, és a szolgáltatás teljes mértékben támogatott az adott régióban. A régió korlátozásai ideiglenesek, és csak az új szolgáltatásokra korlátozódnak. Ezt a megjegyzést akkor távolítjuk el, ha a korlátozás már nem érvényes.

## <a name="choose-a-pricing-tier-sku"></a>Válasszon árképzési szintet (SKU)

[Azure Search jelenleg több díjszabási szinten is elérhető](https://azure.microsoft.com/pricing/details/search/): Ingyenes, alapszintű vagy standard. Mindegyik szintet saját [kapacitás és korlátozások](search-limits-quotas-capacity.md) jellemzik. Útmutatást talál a [Tarifacsomag vagy SKU kiválasztása](search-sku-tier.md) című cikkben.

Az alapszintű és a standard az éles munkaterhelések leggyakoribb lehetőségei, de a legtöbb ügyfél az ingyenes szolgáltatással kezdődik. A szintek közötti fő különbségek a partíció mérete és sebessége, valamint a létrehozható objektumok számának korlátai.

Ne feledje, hogy a szolgáltatás létrehozása után nem módosítható az árképzési szintek. Ha később magasabb vagy alacsonyabb csomagra kíván áttérni, akkor újra létre kell hoznia a szolgáltatást.

## <a name="create-your-service"></a>A szolgáltatás létrehozása

Miután megadta a szükséges bemeneteket, lépjen tovább, és hozza létre a szolgáltatást. 

![A szolgáltatás áttekintése és létrehozása](./media/search-create-service-portal/new-service3.png "A szolgáltatás áttekintése és létrehozása")

A szolgáltatás percek alatt üzembe helyezhető, amelyet az Azure-értesítéseken keresztül figyelheti. Érdemes lehet a szolgáltatást az irányítópultra rögzíteni, hogy később könnyebben hozzáférhessen.

![A szolgáltatás monitorozása és rögzítése](./media/search-create-service-portal/monitor-notifications.png "A szolgáltatás monitorozása és rögzítése")

## <a name="get-a-key-and-url-endpoint"></a>Kulcs és URL-cím végpontjának beolvasása

Ha nem használja a portált, az új szolgáltatáshoz való programozott hozzáféréshez az URL-végpontot és egy hitelesítési API-kulcsot kell megadnia.

1. A szolgáltatás áttekintése lapon keresse meg és másolja ki az URL-végpontot az oldal jobb oldalán.

2. A bal oldali navigációs panelen válassza a **kulcsok** lehetőséget, majd másolja az egyik rendszergazdai kulcsot (ezek egyenértékűek). Felügyeleti API-kulcsok szükségesek a szolgáltatásban található objektumok létrehozásához, frissítéséhez és törléséhez.

   ![A szolgáltatás áttekintő lapja URL-végponttal](./media/search-create-service-portal/get-url-key.png "URL-végpont és egyéb szolgáltatás részletei")

A portálon alapuló feladatokhoz nincs szükség végpontra és kulcsra. A portál már társítva van a Azure Search-erőforráshoz rendszergazdai jogosultságokkal. A portál bemutatójának első [lépései: Hozzon létre egy Azure Search indexet](search-get-started-portal.md)a portálon.

## <a name="scale-your-service"></a>A szolgáltatás méretezése

A szolgáltatást annak üzembe helyezése után igényei szerint méretezheti. Ha a standard szintű csomagot választotta a Azure Search szolgáltatáshoz, akkor két dimenzióban méretezheti a szolgáltatást: replikák és partíciók. Ha az alapszintű csomagot választotta volna, akkor csak replikákat adhatna hozzá. Ingyenes szolgáltatás üzembe helyezése esetén a méretezés nem elérhető.

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

A legtöbb ügyfél csak egy olyan szolgáltatást használ, amely az [erőforrások megfelelő egyensúlyát](search-sku-tier.md)biztosító szinten van kiépítve. Egy szolgáltatásban több index is fenntartható [a választott csomag korlátozásainak megfelelő számban](search-capacity-planning.md), ha elkülönülnek egymástól. Az Azure Search szolgáltatásban a kérelmek csak egy indexre irányulhatnak. Így kisebb esély van adatok véletlen vagy szándékos lekérésére az egyazon szolgáltatáshoz tartozó más indexekből.

Bár az ügyfelek többsége csak egy szolgáltatást használ, szükség lehet a szolgáltatás redundanciájára, ha az üzemeltetési körülmények között az alábbiak szerepelnek:

* Vészhelyreállítás (adatközpont-kimaradás). Kimaradás esetén az Azure Search nem biztosít azonnali feladatátvételt. Javaslatokat és útmutatást a [Szolgáltatásfelügyeletről](search-manage.md) szóló cikkben talál.
* A több-bérlős modellezés vizsgálatával megállapította, hogy az optimális kialakítás további szolgáltatásokat is tartalmaz. További információ: [Tervezés több bérlő esetén](search-modeling-multitenant-saas-applications.md).
* Ha globálisan üzembe helyezett alkalmazás esetén minimalizálni kívánja az alkalmazás belső forgalmának késését, akkor az Azure Search több példányára is szükség lehet különböző régiókban.

> [!NOTE]
> A Azure Searchban nem lehet elkülöníteni az indexelési és lekérdezési műveleteket; így soha nem hozhat létre több szolgáltatást az elkülönített munkaterhelésekhez. Az indexek lekérdezése mindig abban a szolgáltatásban történik, amelyben létre lettek hozva (nem lehet egy indexet egy szolgáltatásban létrehozni és egy másikba átmásolni).

A magas rendelkezésre álláshoz nincs szükség második szolgáltatásra. A lekérdezések magas rendelkezésre állása úgy érhető el, hogy egyazon szolgáltatáson belül 2 vagy több replikát használ. A replikák frissítése egymást követően történik, tehát legalább egy mindig működni fog a szolgáltatásfrissítés végrehajtása közben. A hasznos üzemidőről a [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/) oldalán tájékozódhat bővebben.

## <a name="next-steps"></a>További lépések

Egy Azure Search szolgáltatás kiépítés után folytathatja az első index létrehozását a portálon.

> [!div class="nextstepaction"]
> [Rövid útmutató: Azure Search index létrehozása a portálon](search-get-started-portal.md)
