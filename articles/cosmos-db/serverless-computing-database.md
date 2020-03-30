---
title: Kiszolgáló nélküli adatbázis-számítástechnika az Azure Cosmos DB és az Azure Functions segítségével
description: Ismerje meg, hogyan használható együtt az Azure Cosmos DB és az Azure Functions eseményvezérelt kiszolgáló nélküli számítástechnikai alkalmazások létrehozásához.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 70545020899b69508a4cedb0fd7cf5495c847104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462445"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Kiszolgáló nélküli adatbázis-számítástechnika az Azure Cosmos DB és az Azure Functions használatával

A kiszolgáló nélküli számítástechnika arról szól, hogy képes-e az egyes logikai darabokra összpontosítani, amelyek megismételhetők és állapotnélküliek. Ezek a darabok nem igényelnek infrastruktúra-kezelést, és csak a másodpercek vagy ezredmásodpercek alatt használnak erőforrásokat. A kiszolgáló nélküli számítástechnikai mozgás középpontjában olyan funkciók állnak, amelyeket az Azure Functions az Azure-ökoszisztémában tesz [elérhetővé.](https://azure.microsoft.com/services/functions) Ha többet szeretne megtudni az Azure-beli kiszolgáló nélküli végrehajtási környezetekről, tekintse meg [a kiszolgáló nélküli az Azure-ban](https://azure.microsoft.com/solutions/serverless/) lapot. 

Az [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) és az Azure Functions közötti natív integrációval adatbázis-eseményindítókat, bemeneti kötéseket és kimeneti kötéseket hozhat létre közvetlenül az Azure Cosmos DB-fiókjából. Az Azure Functions és az Azure Cosmos DB használatával eseményvezérelt kiszolgáló nélküli alkalmazásokat hozhat létre és helyezhet üzembe, amelyek alacsony késleltetéssel férnek hozzá a gazdag adatokhoz a globális felhasználói bázis számára.

## <a name="overview"></a>Áttekintés

Az Azure Cosmos DB és az Azure Functions lehetővé teszi az adatbázisok és kiszolgáló nélküli alkalmazások integrálását az alábbi módokon:

* Hozzon létre egy eseményvezérelt **Azure Functions eseményindítót a Cosmos DB számára.** Ez az eseményindító a [változásstreamek](change-feed.md) az Azure Cosmos-tároló változások figyeléséhez támaszkodik. Ha bármilyen módosítást hajt végre egy tárolón, a változáscsatorna-adatfolyam ot küldi el az eseményindító, amely meghívja az Azure-függvényt.
* Másik lehetőségként kösse össze az Azure-függvényt egy Azure Cosmos-tárolóhoz egy **bemeneti kötés**használatával. A bemeneti kötések egy tárolóból olvasnak adatokat, amikor egy függvény végrehajtásra kerül.
* Függvény kötése egy Azure Cosmos-tárolóhoz **kimeneti kötés**használatával. A kimeneti kötések adatokat írnak egy tárolóba, amikor egy függvény befejeződik.

> [!NOTE]
> Jelenleg az Azure Functions eseményindító, bemeneti kötések és kimeneti kötések a Cosmos DB csak az SQL API-val használható. Az összes többi Azure Cosmos DB API-k, az adatbázis eléréséhez a függvény az API statikus kliens használatával.


Az alábbi ábra a következő három integrációt szemlélteti: 

![Az Azure Cosmos DB és az Azure Functions integrálása](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Az Azure Functions eseményindító, bemeneti kötés és kimeneti kötés az Azure Cosmos DB a következő kombinációkban használható:

* A Cosmos DB Azure Functions-eseményindító egy másik Azure Cosmos-tárolóhoz való kimeneti kötéssel használható. Miután egy függvény végrehajt egy műveletet egy elemet a változás csatorna írhat, hogy egy másik tárolóba (írásban, hogy ugyanabból a tárolóból jött hatékonyan hozzon létre egy rekurzív hurok). Vagy használhatja az Azure Functions eseményindító cosmos DB hatékonyan áttelepíteni az összes módosított elemeket egy tárolóból egy másik tárolóba, egy kimeneti kötés használatával.
* Az Azure Cosmos DB bemeneti kötései és kimeneti kötései ugyanabban az Azure-függvényben használhatók. Ez jól működik azokban az esetekben, amikor meg szeretné találni bizonyos adatokat a bemeneti kötés, módosítsa azt az Azure Függvényben, majd mentse el ugyanabba a tárolóba vagy egy másik tárolóba, a módosítás után.
* Az Azure Cosmos-tárolóhoz való bemeneti kötés ugyanabban a függvényben használható, mint a Cosmos DB Azure Functions-eseményindítója, és kimeneti kötéssel vagy anélkül is használható. Ezzel a kombinációval naprakész pénzváltási információkat alkalmazhat (amelyek egy cseretárolóhoz kötött bemeneti kötéssel vannak behúzva) a bevásárlókosár-szolgáltatásúj rendeléseinek módosítási hírcsatornájára. A frissített bevásárlókosár-összeg az aktuális pénznemátváltással egy harmadik tárolóba írható egy kimeneti kötés használatával.

## <a name="use-cases"></a>Használati esetek

A következő használati esetek bemutatják, hogy miként hozhathatja ki a legtöbbet az Azure Cosmos DB-adataiból – az adatok eseményvezérelt Azure-funkciókhoz való csatlakoztatásával.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT-használati eset – Az Azure Functions eseményindító és kimeneti kötés a Cosmos DB számára

Az IoT-implementációkban meghívhat egy függvényt, ha az ellenőrző motor jelzőfénye megjelenik egy csatlakoztatott autóban.

**Végrehajtás:** Azure Functions-eseményindító és kimeneti kötés használata a Cosmos DB-hez

1. A **Cosmos DB Azure Functions eseményindítója** az autós riasztásokhoz kapcsolódó események, például a csatlakoztatott autóban érkező ellenőrző motorjelző esemény indítója.
2. Amikor az ellenőrző motor jelzőfény jön, az érzékelő adatok at küld az Azure Cosmos DB.
3. Az Azure Cosmos DB új érzékelőadat-dokumentumokat hoz létre vagy frissít, majd ezeket a módosításokat a Cosmos DB Azure Functions eseményindítójára továbbítja.
4. Az eseményindító meghívása az érzékelő adatgyűjtésének minden adatváltozásakor történik, mivel az összes változás a változáscsatorna-továbbítón keresztül történik.
5. A funkcióban egy küszöbérték-feltétel t használunk az érzékelő adatainak a jótállási részlegnek történő elküldéséhez.
6. Ha a hőmérséklet is egy bizonyos érték felett van, a rendszer riasztást küld a tulajdonosnak is.
7. A **függvény kimeneti kötése** frissíti az autórekordot egy másik Azure Cosmos-tárolóban az ellenőrző motor eseményével kapcsolatos információk tárolásához.

Az alábbi képen az Azure Portalon az eseményindító kód jelenik meg.

![Hozzon létre egy Azure Functions-eseményindítót a Cosmos DB számára az Azure Portalon](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Pénzügyi felhasználás esete - Időzítő trigger és bemeneti kötés

A pénzügyi megvalósítások, akkor meghív egy függvényt, ha a bankszámla egyenlege alá esik egy bizonyos összeget.

**Végrehajtás:** Időzítő-eseményindító egy Azure Cosmos DB bemeneti kötéssel

1. Egy [időzítő eseményindító](../azure-functions/functions-bindings-timer.md)használatával lekérheti az Azure Cosmos-tárolóban tárolt bankszámlaegyenleg-adatokat egy **bemeneti kötés**használatával.
2. Ha a mérleg a felhasználó által beállított alacsony egyenlegküszöbérték alatt van, majd kövesse nyomon az Azure-függvény egy műveletet.
3. A kimeneti kötés lehet egy [SendGrid-integráció,](../azure-functions/functions-bindings-sendgrid.md) amely e-mailt küld egy szolgáltatásfiókból az egyes alacsony egyenlegű fiókokhoz azonosított e-mail címekre.

Az alábbi képek az Azure Portalon jelenítimeg a kódot ebben a forgatókönyvben.

![Index.js fájl egy időzítő eseményindítóhoz pénzügyi forgatókönyvhöz](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Run.csx fájl időzítő eseményindítóhoz pénzügyi forgatókönyvhöz](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Játékhasználati eset – Az Azure Functions eseményindító és kimeneti kötést biztosít a Cosmos DB számára 

A játékokban, amikor új felhasználó jön létre, kereshet más felhasználókat, akik esetleg ismerik őket az [Azure Cosmos DB Gremlin API](graph-introduction.md)használatával. Ezután az eredményeket egy [Azure Cosmos DB SQL-adatbázisba] írhatja a könnyű lekérés érdekében.

**Végrehajtás:** Azure Functions-eseményindító és kimeneti kötés használata a Cosmos DB-hez

1. Az Azure Cosmos DB [graph-adatbázis](graph-introduction.md) használatával az összes felhasználó tárolására, hozhat létre egy új függvényt egy Azure Functions eseményindító a Cosmos DB használatával. 
2. Amikor egy új felhasználót szúr be, a függvény meghívásra kerül, majd az eredményt **egy kimeneti kötés**sel tárolja.
3. A függvény lekérdezi a gráf adatbázisát, hogy megkeresse az összes olyan felhasználót, aki közvetlenül kapcsolódik az új felhasználóhoz, és visszaadja az adatkészletet a függvénynek.
4. Ezeket az adatokat ezután egy Azure Cosmos DB tárolja, amely ezután könnyen lekérheti bármely előtér-alkalmazás, amely megmutatja az új felhasználó a csatlakoztatott barátok.

### <a name="retail-use-case---multiple-functions"></a>Kiskereskedelmi használati eset - több funkció

A kiskereskedelmi implementációkban, amikor egy felhasználó hozzáad egy cikket a kosárhoz, most már rugalmasan hozhat létre és hívhat meg függvényeket a választható üzleti folyamat összetevőihez.

**Végrehajtás:** Több Azure-függvény aktiválja a Cosmos DB-t egy tároló hallgatása

1. Több Azure-függvényt is létrehozhat, ha a Cosmos DB-hez hozzáadja az Azure Functions-eseményindítókat , amelyek mindegyike ugyanazt a bevásárlókosár-adatokat figyeli. Vegye figyelembe, hogy ha több függvény ugyanazt a módosítási hírcsatornát figyeli, minden függvényhez új címbérlet-gyűjtemény szükséges. A bérletgyűjteményekről a [Hírcsatorna-feldolgozó kód módosítása könyvtár ismertetése című témakörben](change-feed-processor.md)talál további információt.
2. Amikor egy új cikket ad hozzá a felhasználók bevásárlókosárhoz, minden funkciót a bevásárlókosár-tárolóból származó módosítási hírcsatorna egymástól függetlenül hív meg.
   * Az egyik funkció az aktuális kosár tartalmát használhatja a felhasználó érdeklődésének megfelelő egyéb elemek megjelenítésének megváltoztatására.
   * Egy másik funkció frissítheti a készletösszegeket.
   * Egy másik funkció elküldheti az ügyfelek adatait bizonyos termékekről a marketing osztálynak, aki promóciós levelezőt küld nekik. 

     Bármely részleg létrehozhat egy Azure-függvényt a Cosmos DB számára a változáscsatorna meghallgatásával, és győződjön meg arról, hogy nem késlelteti a kritikus rendelésfeldolgozási eseményeket a folyamatban.

Az összes ilyen használati esetekben, mivel a függvény leválasztott a saját maga az alkalmazás, nem kell mindig új alkalmazáspéldányokat felpörgetni. Ehelyett az Azure Functions előpörgeti az egyes funkciókat, hogy szükség szerint elvégezze a különálló folyamatokat.

## <a name="tooling"></a>Eszközök

Az Azure Cosmos DB és az Azure Functions közötti natív integráció az Azure Portalon és a Visual Studio 2019-ben érhető el.

* Az Azure Functions portálon létrehozhat egy eseményindítót. Rövid útmutató: [Hozzon létre egy Azure Functions-eseményindítót a Cosmos DB számára az Azure Portalon.](https://aka.ms/cosmosdbtriggerportalfunc)
* Az Azure Cosmos DB portálon hozzáadhat egy Azure Functions-eseményindítót a Cosmos DB-hez egy meglévő Azure Function alkalmazáshoz ugyanabban az erőforráscsoportban.
* A Visual Studio 2019-ben az Azure Functions Tools használatával hozhatja létre az [eseményindítót:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Miért válassza az Azure Functions-integrációt a kiszolgáló nélküli számítástechnika érdekében?

Az Azure Functions lehetővé teszi, hogy méretezhető munkaegységeket hozzon létre, vagy tömör logikai darabokat, amelyek igény szerint futtathatók, infrastruktúra kiépítése vagy kezelése nélkül. Az Azure Functions használatával nem kell létrehoznia egy teljes értékű alkalmazást az Azure Cosmos-adatbázis változásaira való reagáláshoz, kis újrafelhasználható függvényeket hozhat létre az adott feladatokhoz. Emellett az Azure Cosmos DB-adatokat is használhatja egy Azure-függvény bemeneteként vagy kimeneteként olyan eseményekre adott válaszként, mint például egy HTTP-kérés vagy egy időzített eseményindító.

Az Azure Cosmos DB a kiszolgáló nélküli számítástechnikai architektúra ajánlott adatbázisa a következő okok miatt:

* **Azonnali hozzáférés az összes adathoz:** Részletes hozzáféréssel rendelkezik minden tárolt értékhez, mivel az Azure Cosmos DB alapértelmezés szerint [automatikusan indexeli](index-policy.md) az összes adatot, és ezeket az indexeket azonnal elérhetővé teszi. Ez azt jelenti, hogy folyamatosan lekérdezheti, frissítheti és új elemeket adhat hozzá az adatbázishoz, és azonnali hozzáférést biztosíthat az Azure Functions-en keresztül.

* **Séma nélküli**. Az Azure Cosmos DB nem sémamentes, így egyedülálló módon képes kezelni az Azure-függvényből származó adatkimeneteket. Ez a "bármit kezelni" megközelítés egyszerűvé teszi, hogy hozzon létre egy különböző funkciók, amelyek az Azure Cosmos DB kimenetét.

* **Méretezhető átviteli fokkal**. Az átviteli nagyátáraaz Azure Cosmos DB-ben azonnal fel- és leskálázható. Ha több száz vagy több ezer függvény lekérdezése és írása ugyanabba a tárolóba, felskálázhatja a [RU/s](request-units.md) a terhelés kezeléséhez. Minden funkció párhuzamosan működhet a kiosztott RU/s használatával, és az adatok garantáltan [konzisztensek.](consistency-levels.md)

* **Globális replikáció**. Az Azure Cosmos DB-adatokat világszerte replikálhatja a késés csökkentése [érdekében,](distribute-data-globally.md) és a felhasználók tartózkodási helyéhez legközelebb eső adatok földrajzi helyének helyének helyének használatával. Mint minden Azure Cosmos DB-lekérdezések, az eseményvezérelt eseményindítók ból származó adatok a felhasználóhoz legközelebb eső Azure Cosmos DB adatokat.

Ha az Azure Functions szolgáltatással szeretne integrálódni az adatok tárolásához, és nincs szüksége mély indexelésre, vagy ha mellékleteket és médiafájlokat kell tárolnia, az [Azure Blob Storage-eseményindító](../azure-functions/functions-bindings-storage-blob.md) jobb megoldás lehet.

Az Azure-funkciók előnyei: 

* **Eseményvezérelt**. Az Azure Functions eseményvezérelt, és az Azure Cosmos DB módosítási hírcsatornáját hallgathatja. Ez azt jelenti, hogy nem kell zenehallgatási logikát létrehoznia, csak tartsa szemmel a figyelt módosításokat. 

* **Nincs korlátozás**. A függvények párhuzamosan hajthatók végre, és a szolgáltatás annyit pörget, amennyire szüksége van. Te állítod be a paramétereket.

* **Jó a gyors feladatokhoz.** A szolgáltatás új függvénypéldányokat hoz létre, amikor egy esemény aktiválódik, és a függvény befejeződése után bezárja azokat. Csak azért kell fizetnie, amíg a funkciók futnak.

Ha nem biztos abban, hogy a Flow, a Logic Apps, az Azure Functions vagy a WebJobs a legjobb a megvalósításhoz, olvassa el [a Choose between Flow, logic apps, functions és webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)lehetőséget.

## <a name="next-steps"></a>További lépések

Most csatlakoztassuk az Azure Cosmos DB-t és az Azure Functions-et a valóságnak: 

* [Hozzon létre egy Azure Functions-eseményindítót a Cosmos DB számára az Azure Portalon](https://aka.ms/cosmosdbtriggerportalfunc)
* [Azure Functions HTTP-trigger létrehozása Azure Cosmos DB bemeneti kötéssel](https://aka.ms/cosmosdbinputbind)
* [Az Azure Cosmos DB kötések és eseményindítók](../azure-functions/functions-bindings-cosmosdb-v2.md)