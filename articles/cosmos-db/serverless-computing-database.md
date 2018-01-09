---
title: "Számítási kiszolgáló nélküli adatbázis - Azure Functions és Azure Cosmos DB |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure Cosmos adatbázis és az Azure Functions együtt is használható eseményvezérelt kiszolgáló nélküli számítógépes-alkalmazásai létrehozására."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mimig
ms.openlocfilehash: 39481322d88a7674fce712dc24ef1f32f627f3cc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Az Azure Cosmos DB: Kiszolgáló nélküli adatbázis számítási Azure Functions használatával

Kiszolgáló nélküli számítástechnikai legyen a lehetőség, hogy a logikai egyedi kódrészletek, amelyek megismételhető és állapot nélküli. Darabot igényel semmilyen infrastruktúra felügyelete, és erőforrások csak esetében a másodperc, ezredmásodperc, akkor futtassa a használják. A kiszolgáló nélküli számítógépes mozgása középpontjában van funkciók is elérhetők az Azure-ökoszisztéma által [Azure Functions](https://azure.microsoft.com/services/functions).

A natív integrációjával [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) és az Azure Functions, létrehozhat adatbázis eseményindítók, kötések bemeneti és kimeneti kötések közvetlenül Azure Cosmos DB fiókjából. Az Azure Functions és az Azure Cosmos DB, hozzon létre és eseményvezérelt kiszolgáló nélküli alkalmazások telepítése a alacsony késésű hozzáférést egy globális felhasználói bázis-részletes adatait.

## <a name="overview"></a>Áttekintés

Az Azure Cosmos-adatbázis és az Azure Functions lehetővé teszik az adatbázisokról és a kiszolgáló nélküli alkalmazások integrálása az alábbi módokon:

* Hozzon létre egy eseményvezérelt **Azure Cosmos DB eseményindító** egy Azure függvényben. Ehhez az eseményindítóhoz támaszkodik [adatcsatorna módosítása](change-feed.md) adatfolyamokat a módosítások Azure Cosmos DB tárolója figyelésére. Amikor végzett módosítások tárolót, a módosítás adatcsatorna-adatfolyam elküldi eseményindító, amely meghívja az Azure-függvényt.
* Azt is megteheti, egy Azure-függvény kötése egy Azure Cosmos DB gyűjtemény használja egy **kötés bemeneti**. Bemeneti kötések adatokat olvasni egy tároló, a függvény végrehajtásakor a.
* Egy Azure Cosmos DB gyűjtemény használja kötést létrehozni a következő függvényt egy **kimeneti kötése**. Kimeneti kötések adatokat írni egy tároló, egy olyan függvényt befejezéséről.

> [!NOTE]
> Jelenleg az Azure Cosmos DB eseményindító, a bemeneti kötések és a kimeneti kötések működik, csak SQL API-t és a Graph API fiókokhoz.

A következő ábra szemlélteti a három integrációja: 

![Hogyan Azure Cosmos adatbázis és az Azure Functions integrálása](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Az Azure Cosmos DB eseményindító, a bemeneti kötése és a kimeneti kötése a következő kombinációkban használhatók:
* Egy Azure Cosmos DB eseményindító használható egy másik Azure Cosmos DB tárolóhoz kimeneti kötést. Egy függvény műveletet végezne a módosítás hírcsatorna elem után írhat egy másik tárolóhoz (érkezett tárolóhoz hatékonyan eredményezne rekurzív hurkot írása). Vagy egy Azure Cosmos DB eseményindító segítségével hatékonyan át minden módosítatlan elemekkel több tároló egy másik tárolóhoz egy kimeneti kötés használatával.
* Kötések bemeneti és kimeneti kötései Azure Cosmos DB azonos Azure függvényben használható. Ez a módszer jól azokban az esetekben, amikor bizonyos adatok található a bemeneti kötése, módosítsa az Azure-függvény, és mentse a tárolóhoz, vagy egy másik tárolóhoz, a módosítás után.
* Egy bemeneti kötése egy Cosmos-DB Azure-tárolót egy Azure Cosmos DB eseményindító ugyanabban a függvényben használható, és használható vagy azok nélkül is kötelező kimenettel. Segítségével kombinációban történő alkalmazására naprakész pénznem exchange (lekért be egy bemeneti kötése egy exchange-tárolót) a módosítás hírcsatorna új rendelések a vásárlási bevásárlókocsiból szolgáltatásban. A frissített bevásárlási bevásárlókocsiból teljes, a jelenlegi pénznemátváltásoknál alkalmazza, a csak írható harmadik tárolóhely egy kimeneti kötése.

## <a name="use-cases"></a>Használati esetek

A következő használati esetek bemutatják, hogy a legjobban az Azure Cosmos DB adatok - adataihoz kapcsolódva eseményvezérelt Azure Functions számos módja.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Az IoT-és nagybetűhasználattal - Azure Cosmos DB eseményindító, és kimeneti kötése

Az IoT-implementációk esetén egy olyan függvényt Ha a jelölőnégyzet motor világos megjelenik egy csatlakoztatott autó hívhat meg.

**Végrehajtás:** egy Azure Cosmos DB eseményindító és egy kimeneti kötése

1. Egy **Azure Cosmos DB eseményindító** autó riasztások, például a jelölőnégyzet motor világos várható csatlakoztatott autót kapcsolódó események kiváltásához szolgál.
2. Ha a jelölőnégyzet motor világos, az érzékelők adatain Azure Cosmos DB érkezik.
3. Azure Cosmos-adatbázis létrehozása vagy frissítése. új érzékelő adatokat tartalmazó dokumentumok, majd ezeket a módosításokat átvitt Azure Cosmos DB eseményindító.
4. Az eseményindító meghívták minden adat-változás az érzékelő adatokat gyűjteményhez, mivel minden módosítás a hírcsatorna módosítás keresztül átvitt.
5. Küszöbérték feltétel az érzékelő adatokat küldeni a jótállás részleg szerepel a függvényben.
6. Ha a hőmérséklet is keresztül a megadott érték, egy riasztás is lett van küldve a tulajdonos.
7. A **kimeneti kötése** a függvény a frissíti a car bejegyzést egy másik Azure Cosmos DB gyűjtemény ellenőrzés motor esemény adatainak tárolásához.

A következő kép bemutatja az Azure-portálon az eseményindító írt kód.

![Egy Azure Cosmos DB eseményindító létrehozása az Azure portálon](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Pénzügyi nagybetűhasználattal - időzítő indítófeltételt, és adjon meg kötés

Pénzügyi implementációkban függvény amikor bizonyos alá tartozik egy bank egyenleg hívhat meg.

**Végrehajtás:** egy időzítő indítófeltételt egy Azure Cosmos DB a bemeneti kötése

1. Használatával egy [időzítő indítófeltételt](../azure-functions/functions-bindings-timer.md), a bank egyenleg fiókadatok egy Azure Cosmos DB tárolóban segítségével rendszeres időközönként le egy **kötés bemeneti**.
2. Az egyenleg megadott érték a felhasználó által megadott alacsony egyenleg küszöbérték alá, kövesse művelet, az Azure-függvény.
3. A kimeneti kötés lehet egy [SendGrid integrációs](../azure-functions/functions-bindings-sendgrid.md) , amely egy e-mailt küld a szolgáltatásfiók az egyes alacsony Egyenleg számlák azonosítani az e-mail címekre.

A következő ábrákon az Azure portálon ebben a forgatókönyvben a kód láthatók.

![Egy időzítő indítófeltételt pénzügyi forgatókönyvek index.js fájlt](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Egy időzítő indítófeltételt pénzügyi forgatókönyvek Run.csx fájlt](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Játék-és nagybetűhasználattal - Azure Cosmos DB eseményindító, és kimeneti kötése

A játékok, ha egy új felhasználó létrehozása kereshet előfordulhat, hogy ismeri a többi felhasználó a [Azure Cosmos DB Graph API](graph-introduction.md). Az eredmények [Azure Cosmos adatbázis SQL-adatbázis] partnerek majd írni.

**Végrehajtás:** egy Azure Cosmos DB eseményindító és egy kimeneti kötése

1. Egy Azure Cosmos DB használatával [graph adatbázis](graph-introduction.md) minden felhasználó tárolására, létrehozhat egy új funkció az Azure Cosmos DB eseményindító. 
2. Amikor új felhasználó csatlakoztatva van, a függvény meghívták és majd eredménye segítségével tárolják az **kimeneti kötése**.
3. A függvény lekérdezi a diagram adatbázis, amely közvetlenül kapcsolódik az új felhasználót, és adja vissza, hogy a dataset függvény felhasználók kereséséhez.
4. Egy Azure Cosmos DB, amely majd egyszerűen lekérhetők bármely előtér-alkalmazás, amely tartalmazza az új felhasználó az csatlakoztatott ismerősei majd tárolja ezeket az adatokat.

### <a name="retail-use-case---multiple-functions"></a>Kereskedelmi használati eset - több funkciók

Kereskedelmi implementációkban, amikor egy felhasználó egy elem hozzáadása a kosárhoz most már rendelkezik létrehozása és a választható üzleti csővezeték-összetevők funkciók meghívja a rugalmasságot.

**Végrehajtás:** figyel egy gyűjteményhez több Azure Cosmos DB eseményindítók

1. Létrehozhat több Azure-funkciók Azure Cosmos DB eseményindítók-ezek mindegyike figyelésére azonos módosítása a bevásárlókocsiból adatok vásárlás adatcsatorna hozzáadásával. Vegye figyelembe, hogy amikor több funkciók figyelésére azonos adatcsatorna módosításához új bérleti gyűjtemény szükség az egyes funkciók.
2. Új elem a bevásárlókocsiból vásárlás felhasználókat ad hozzá, amikor egyes funkciók egymástól függetlenül meghívták a hírcsatorna a vásárlási bevásárlókocsiból tárolóból módosítani.
    * Egy függvény az aktuális kosár tartalmát segítségével lehetséges, hogy a felhasználó előfordulhat, hogy érdeklődik más elemeket megjelenésének módosítása.
    * Egy másik függvényen módosíthatjuk készlet.
    * Egy másik függvényen küldhet ügyfél adatait az egyes termékek a marketingrészleg felhasználói, akik elküldi őket egy promóciós levelezőprogrammal. 

    Minden részleg hozzon létre egy Azure Cosmos DB eseményindító a módosítás hírcsatorna figyel, és győződjön meg azok nem késleltetés kritikus rendelés események feldolgozását a folyamat során.

Mindegyik eseteinek, a függvény az alkalmazás rendelkezik le, mert nem kell új app-példány mentése léptetési folyamatosan. Ehelyett az Azure Functions diszkrét folyamatok befejezésére, igény szerint egyes funkciókat forog.

## <a name="tooling"></a>Tooling eszköz

Natív integráció az Azure Cosmos adatbázis és az Azure Functions között érhető el az Azure portálon.
* Az Azure Functions portálon létrehozhat egy Azure Cosmos DB eseményindító. Gyors üzembe helyezés útmutatásért lásd: [egy Azure Cosmos DB eseményindító létrehozása az Azure portálon](https://aka.ms/cosmosdbtriggerportalfunc) ![egy Azure Cosmos DB eseményindító létrehozása az Azure Functions portálon](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* Az Azure Functions portálon hozzáadhatja Azure Cosmos DB bemeneti kötései és a kimeneti kötések eseményindítók más típusú. Gyors üzembe helyezés útmutatásért lásd: [Azure Functions és Cosmos DB strukturálatlan adatok tárolásához](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Egy Azure Cosmos DB eseményindító létrehozása az Azure Functions portálon](./media/serverless-computing-database/function-portal-input-binding.png)
*   A Azure Cosmos DB-portálon egy Azure Cosmos DB eseményindító adhat hozzá egy meglévő Azure-függvény alkalmazást ugyanabban az erőforráscsoportban.
    ![Egy Azure Cosmos DB eseményindító létrehozása az Azure Functions portálon](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Miért válassza ki az Azure Functions-integráció kiszolgáló nélküli számítástechnikai?

Az Azure Functions lehetővé teszi a munkát, vagy olyan logika, amely futtathatja az igény szerinti, kiépítés és infrastruktúra kezelése nélkül tömör darab méretezhető egységek létrehozásához. Az Azure Functions használatával szeretne létrehozni egy üzemelő teljes körű példányok válaszolni a módosításokat az Azure Cosmos DB adatbázisban nincs, bizonyos feladatok kis újrafelhasználható funkciók is létrehozhat. Emellett akkor is használható Azure Cosmos DB adatok a bemenet vagy kimenet egy Azure-függvény, például HTTP esemény bekövetkeztekor kérelmek vagy időzített eseményindítót.

Azure Cosmos-adatbázis a következő okok miatt a kiszolgáló nélküli számítógépes architektúra ajánlott adatbázis:

* **Az adatok azonnali hozzáférési**: hozzáférhet a részletes tárolása, mert minden egyes értékhez Azure Cosmos DB [automatikusan elvégzi a](indexing-policies.md) alapértelmezés szerint minden adat, és ezeket az indexeket azonnal elérhetővé. Ez azt jelenti, hogy folyamatosan lekérdezésére, frissítéséhez és az adatbázis új elemek hozzáadására és azonnali hozzáférhetnek az Azure Functions segítségével lehet.

* **Séma nélküli**. Az Azure Cosmos DB séma nélküli -, így egyedi módon képes kezelni egy Azure-függvény adatok kimenetet. Ez a megközelítés "semmit kezelni" teszi magától értetődő, amely számos feladatot létrehozni Azure Cosmos DB minden kimenetet.

* **Méretezhető átviteli sebesség**. Átviteli sebesség is méretezhető felfelé és lefelé azonnal az Azure Cosmos-Adatbázisba. Ha több száz vagy ezer funkciók kérdez le, és ugyanahhoz a gyűjteményekhez írása, költenie a [RU/mp](request-units.md) a terhelés kezelésére. Összes funkciójának együttműködhet párhuzamosan használ a lefoglalt RU/mp és az adatok garantáltan [konzisztens](consistency-levels.md).

* **Globális replikációs**. Azure Cosmos DB adatok replikálhatja [a világ minden táján](distribute-data-globally.md) a késés, földrajzi helye a legközelebb hol találhatók a felhasználók adatok csökkentéséhez. Mint minden Azure Cosmos DB lekérdezések eseményvezérelt eseményindítók adatolvasás adatok a legközelebb a felhasználó Azure Cosmos-Adatbázisból.

Ha integrálása az Azure Functions adatok tárolására van szüksége, és nem kell részletes indexelő, vagy ha mellékletek és médiafájlok tárolására van szüksége a [Azure Blob Storage eseményindító](../azure-functions/functions-bindings-storage-blob.md) jobb megoldás lehet.

Az Azure Functions előnyei: 

* **Eseményvezérelt**. Az Azure Functions eseményvezérelt, és figyelheti az Azure Cosmos Adatbázisból hírcsatorna változásakor. Ez azt jelenti, hogy nem szeretné létrehozni a figyelési logikát, akkor csak nyomon követheti akkor figyelését vagyunk a módosítások. 

* **Nem határoz meg**. Függvények futtatása, és a szolgáltatás fordulat fel tetszőleges számú, szükség szerint. Állítsa a paramétereit.

* **Gyors tevékenységek helyes**. A szolgáltatás forog funkciók új példányát be, amikor az esemény akkor következik be, és bezárja azokat, amint befejeződik a függvény. Csak kell fizetnie az idő a függvények futnak.

Ha nem biztos abban, hogy folyamata, a Logic Apps, az Azure Functions vagy a webjobs-feladatok-e hoznia ajánlott, lásd: [válasszon a folyamatot, a Logic Apps, a funkciók és a webjobs-feladatok](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>További lépések

Most tegyük csatlakozás Azure Cosmos adatbázis és az Azure Functions Ha valós: 

* [Egy Azure Cosmos DB eseményindító létrehozása az Azure portálon](https://aka.ms/cosmosdbtriggerportalfunc)
* [Az Azure Functions HTTP-eseményindítóval hozzon létre egy Azure Cosmos DB bemeneti kötése](https://aka.ms/cosmosdbinputbind)
* [Az Azure Functions és Cosmos DB strukturálatlan adatok tárolásához](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Az Azure Cosmos DB kötései és eseményindítók](../azure-functions/functions-bindings-documentdb.md)


 



