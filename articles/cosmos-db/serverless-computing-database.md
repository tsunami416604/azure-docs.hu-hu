---
title: Kiszolgáló nélküli adatbázis-használat – Azure Functions és az Azure Cosmos DB-hez
description: Ismerje meg, hogyan Azure Cosmos DB és az Azure Functions együttes eseményvezérelt kiszolgáló nélküli számítástechnika alkalmazásokat hozhat létre.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 1d013f2cdd9f33f55d579638386355e5cbaccb7e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714950"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB és az Azure Functions használatával kiszolgáló nélküli adatbázis-használat

Kiszolgáló nélküli számítástechnika az összes arról, hogy arra koncentrálhasson, megismételhető és állapot nélküli logika egyéni adatokat. Megtalálhatja megkövetelése nincs infrastruktúra-felügyelettel, és csak másodperc vagy milliszekundum, azok futtatása tartozó használnak erőforrásokat. A kiszolgáló nélküli számítástechnika áthelyezését magját vannak a Funkciók, az Azure-ökoszisztéma által rendelkezésre bocsátott [Azure Functions](https://azure.microsoft.com/services/functions). További információ a többi kiszolgáló nélküli végrehajtáshoz környezetek Azure itt talál: [az Azure kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) lapot. 

Közötti natív integrációnak [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) és az Azure Functions, létrehozhat adatbázis eseményindítók, bemeneti és kimeneti kötések közvetlenül az Azure Cosmos DB-fiókot. Az Azure Functions és az Azure Cosmos DB használatával, létrehozása, és üzembe helyezése az eseményvezérelt, kiszolgáló nélküli alkalmazásokat alacsony késéssel globális felhasználói bázis vonatkozó részletes adatokat.

## <a name="overview"></a>Áttekintés

Az Azure Cosmos DB és az Azure Functions lehetővé teszi az adatbázisok és a kiszolgáló nélküli alkalmazások integrálása az alábbi módokon:

* Hozzon létre egy eseményvezérelt **Azure Cosmos DB-eseményindító** az Azure-függvény. Ez az eseményindító támaszkodik [módosításcsatornáját](change-feed.md) Streamek módosítások az Azure Cosmos DB-tárolók monitorozásához. Amikor végzett módosítások egy tárolóba, az eseményindító, amely az Azure-függvény meghív elküldi a változáscsatorna stream.
* Azt is megteheti, kötést létrehozni egy Azure-függvény használatával egy Azure Cosmos DB tároló egy **bemeneti kötést**. Bemeneti kötések adatokat olvasni egy tárolót, ha egy függvény végrehajtását.
* Egy függvény kötést létrehozni egy Azure Cosmos DB tároló használatával egy **kimeneti kötésének**. Kimeneti kötések adatokat írni egy tárolót, egy függvény befejezéséről.

> [!NOTE]
> Jelenleg az Azure Cosmos DB eseményindító, bemeneti és kimeneti kötések támogatottak a csak az SQL API segítségével. Az összes többi Azure Cosmos DB API-k, kell az adatbázis eléréséhez fog a függvényéből az API-hoz a statikus ügyfél használatával.


A következő ábra szemlélteti ezen három Integrációk: 

![Milyen az Azure Cosmos DB és az Azure Functions integrálása](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Az Azure Cosmos DB eseményindító, a bemeneti kötési és a kimeneti kötés a következő kombinációkban használhatók:
* Egy Azure Cosmos DB-eseményindítóval is használható egy kimeneti kötés egy másik Azure Cosmos DB-tárolóba. Miután függvény végrehajt egy műveletet a módosítási hírcsatorna-elem írhat egy másik tároló (írás a, hogy ugyanazt a tárolót származik hatékonyan kell létrehoznia egy rekurzív hurok). Vagy egy Azure Cosmos DB-eseményindító segítségével hatékonyan át minden módosított elemeket egy tárolóból egy másik tárolóba, a kimeneti kötés használatát.
* Bemeneti és kimeneti kötések az Azure Cosmos DB az azonos Azure-függvény is használható. Ez működik jól abban az esetben, ha bizonyos adatok keresése a bemeneti kötéssel, módosítását, az Azure-függvény, és mentse azt ugyanazt a tárolót, vagy egy másik tárolóba, a módosítás után szeretné.
* Bemeneti kötést egy Azure Cosmos DB-tárolóhoz egy Azure Cosmos DB-eseményindító ugyanabban a függvényben használható, és vagy anélkül egy kimeneti kötés is használható. Használhatja az kombinációját naprakész pénznem exchange adatai (jelentkezzen be az exchange-tárolókat bemeneti kötést lekért) a alkalmazni a módosítási hírcsatorna új megrendelések a vásárlási a bevásárlókocsi-szolgáltatásban. A frissített vásárlási bevásárlókocsi teljes, a alkalmazni, aktuális átváltásához csak írható egy harmadik tároló kimeneti kötés használatával.

## <a name="use-cases"></a>Használati esetek

A következő használati esetek néhány módszerrel teheti a legtöbbet az Azure Cosmos DB-adatok – csatlakozás az adatokhoz az Azure Functions eseményvezérelt mutatják be.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Használati eset – Azure Cosmos DB-eseményindító IoT- és kimeneti kötése

IoT-implementációkban egy függvényt, amikor a csatlakoztatott autók műszerfalához némileg jelenik meg a jelölőnégyzet motor világos hívhat meg.

**Végrehajtás:** Egy Azure Cosmos DB-eseményindítóval és a egy kimeneti kötés

1. Egy **Azure Cosmos DB-eseményindító** aktiválhat eseményeket, kapcsolódó autó-riasztásokat, például csatlakoztatott autók műszerfalához némileg hamarosan ellenőrzési motor fény szolgál.
2. Ha a jelölőnégyzet motor világos, érzékelőadatokat zajlik az Azure Cosmos DB-hez.
3. Az Azure Cosmos DB létrehozása vagy frissítése. új érzékelő adatokat tartalmazó dokumentumok, majd ezeket a módosításokat a rendszer streamként továbbítja az Azure Cosmos DB-eseményindítóval.
4. A trigger meghívásakor minden adat-változás az érzékelő adatokat gyűjteményhez, mivel minden módosítás a változáscsatorna keresztül átvitt.
5. Küszöbérték feltétel az érzékelő adatokat küldeni a jótállási részleg függvény szerepel.
6. Ha a hőmérséklet van is egy bizonyos érték feletti, riasztást is küld a tulajdonosa.
7. A **kimeneti kötésének** a függvény frissíti az autó rekordot egy másik Azure Cosmos DB-tároló az ellenőrzési motor esemény kapcsolatos információk tárolására.

Az alábbi képen az Azure Portalon, az eseményindító írt kóddal.

![Az Azure Portalon egy Azure Cosmos DB-eseményindító létrehozása](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Pénzügyi használati eset – időzítő eseményindító és bemeneti kötést

Pénzügyi megvalósításokban egy függvényt, amikor egy adott időtartamot alá tartozik a bank fiókja egyenlege hívhat meg.

**Végrehajtás:** Azure Cosmos DB bemeneti kötéssel időzítő eseményindító

1. Használatával egy [időzítő eseményindító](../azure-functions/functions-bindings-timer.md), egy Azure Cosmos DB-tárolók használatával időközönként tárolt Bankszámla egyenleg információit kérheti egy **bemeneti kötést**.
2. Ha megfelelő módon értesüljenek alacsony egyenlegéről a felhasználó által megadott küszöbérték alá, majd kövesse művelet, az Azure-függvény.
3. A kimeneti kötés lehet egy [SendGrid integrációs](../azure-functions/functions-bindings-sendgrid.md) , amely egy e-mailt küld szolgáltatásfiók egyes alacsony egyenlegéről számlák azonosítani az e-mail-címeket.

Az alábbi képeken az kód ebben a forgatókönyvben az Azure Portalon.

![Az időzítő eseményindító pénzügyi forgatókönyv esetén az index.js fájlt](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Az időzítő eseményindító egy pénzügyi forgatókönyv Run.csx fájl](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Használati eset – Azure Cosmos DB-eseményindító játék és a kimeneti kötése

A játék-, amikor létrejön egy új felhasználót is kereshet más, előfordulhat, hogy ismeri a felhasználók a [Azure Cosmos DB Gremlin API](graph-introduction.md). Az eredményeket egy [Azure Cosmos DB SQL Database-adatbázishoz] egyszerű lekérdezés majd írhat.

**Végrehajtás:** Egy Azure Cosmos DB-eseményindítóval és a egy kimeneti kötés

1. Egy Azure Cosmos DB használatával [gráfadatbázis](graph-introduction.md) szeretné tárolni a minden felhasználó, létrehozhat egy új függvényt egy Azure Cosmos DB-eseményindítóval. 
2. Amikor a rendszer beszúr egy új felhasználót, a függvény meghívása, és ezután az eredmény tárolja használatával egy **kimeneti kötésének**.
3. A függvény lekérdezése a graph-adatbázis, amely az új felhasználó közvetlenül kapcsolódó, és visszaadja az adatkészlet a függvény minden felhasználó kereséséhez.
4. Ezeket az adatokat egy Azure Cosmos DB, amely ezután egyszerűen lekérheti bármelyik előtér-alkalmazás, amely megjeleníti az új felhasználó csatlakoztatott ismerősei majd tárolódik.

### <a name="retail-use-case---multiple-functions"></a>Kiskereskedelmi használatieset - több funkciók

A kiskereskedelmi megvalósításokhoz amikor egy felhasználó felvesz egy elemet a kosárhoz most rugalmasan hozhat létre, és nem kötelező üzleti folyamat összetevők függvények meghívása.

**Végrehajtás:** Több Azure Cosmos DB-eseményindító figyeli egy tárolóba

1. Létrehozhat több Azure-funkciók hozzáadásával az Azure Cosmos DB-eseményindítók-mindegyike figyeli a következő azonos módosításcsatornáját a vásárlás bevásárlókocsi adatokat. Vegye figyelembe, hogy amikor több funkciók figyeli a következő azonos módosításcsatornáját, egy új a bérletek gyűjteményének szükség minden egyes függvény. Bérlet gyűjteményekkel kapcsolatos további információkért lásd: [ismertetése a Változáscsatorna feldolgozói kódtárával](change-feed-processor.md).
2. Egy új elemet a bevásárlókocsihoz felhasználók hozzáadásakor, minden egyes függvény egymástól függetlenül hív a változáscsatorna a vásárlási a bevásárlókocsi-tárolóból.
    * Egy függvény az aktuális kosár tartalmát felhasználhatja más elemek, a felhasználó esetlegesen érdeklő célpontokhoz jelenjen meg.
    * Egy másik függvény előfordulhat, hogy frissítse a készlet.
    * Egy másik függvény küldhet ügyfél-információkat az egyes termékek a marketingrészleg felhasználói, akik promóciós levelezőprogrammal elküldi őket. 

    Bármelyik részleg hozzon létre egy Azure Cosmos DB-eseményindító úgy a módosítási hírcsatorna, és győződjön meg azok nem késleltetés kritikus sorrendben az események feldolgozását a folyamat során.

Ezek mindegyikét a használati esetek, a függvény rendelkezik leválasztott maga az alkalmazás, mert nem kell mindig az új alkalmazás-példányok üzembe helyezésekor. Ehelyett az Azure Functions indít el az egyes függvények diszkrét folyamatok végrehajtásához szükség szerint.

## <a name="tooling"></a>Eszköztámogatás

Az Azure Portalon, és a Visual Studio 2017 Azure Cosmos DB és az Azure Functions közötti natív integrációnak érhető el.

* Az Azure Functions portálon létrehozhat egy Azure Cosmos DB-eseményindítóval. Gyors útmutatásért lásd: [egy Azure Cosmos DB-eseményindító létrehozása az Azure Portalon](https://aka.ms/cosmosdbtriggerportalfunc).
* Az Azure Cosmos DB-portálon egy Azure Cosmos DB-eseményindító adhat hozzá egy meglévő Azure-függvényalkalmazást ugyanabban az erőforráscsoportban.
* A Visual Studio 2017 használatával is létrehozhat egy Azure Cosmos DB az eseményindító a [Azure Functions Tools for Visual Studio 2017](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Miért érdemes választani az Azure Functions-integráció a kiszolgáló nélküli számítástechnika?

Az Azure Functions lehetővé teszi a munkát, vagy tömör darab logika, amely üzembe helyezése és infrastruktúra kezelése nélkül futtatható igény szerinti, méretezhető egységek létrehozásához. Az Azure Functions szolgáltatással, nem kell reagálni az igények változásaira az Azure Cosmos DB-adatbázisban a teljes alkalmazás létrehozása, egyes feladatok kis újrafelhasználható funkciókat is létrehozhat. Emellett használhatja az Azure Cosmos DB-adatai, a bemenet vagy kimenet az Azure-függvény, például egy HTTP esemény bekövetkeztekor kérések és a egy időzített eseményindító.

Azure Cosmos DB a kiszolgáló nélküli számítástechnikai architektúra ajánlott adatbázisát a következő okok miatt:

* **Minden az adatok azonnali hozzáférést**: Minden egyes értékhez, mert a részletes hozzáférés rendelkezik Azure Cosmos DB [automatikusan indexeli az](index-policy.md) alapértelmezés szerint minden adat, és ezeket az indexeket azonnal elérhetővé. Ez azt jelenti, folyamatosan lekérdezheti, módosíthatja, és új elemeket adhat hozzá az adatbázishoz és azonnal hozzáférhet az Azure Functions használatával.

* **Séma nélküli**. Azure Cosmos DB a séma nélküli –, így képes kezelni bármilyen adatokat tartalmazó kimenetét az Azure-függvény. Ez a megközelítés "képesek kezelni" teszi magától értetődő, amely különféle funkciókat létrehozása az Azure Cosmos DB az összes kimeneti.

* **Méretezhető átviteli sebesség**. Átviteli sebesség méretezhetők felfelé és lefelé azonnal az Azure Cosmos DB-ben. Ha több száz vagy ezer funkciók ugyanazt a tárolót, írásához és lekérdezéséhez, lehetősége lesz növelni az [RU/s](request-units.md) terhelés kezelésére. A függvények használatával a kiosztott RU/s párhuzamosan működhet, és az adatok garantáltan [konzisztens](consistency-levels.md).

* **Globális replikálás**. Replikálhatja az Azure Cosmos DB-adatai [világszerte](distribute-data-globally.md) késés, földrajzi az adatok hol találhatók a felhasználók legközelebb csökkentése érdekében. Mint az Azure Cosmos DB az összes lekérdezés, eseményvezérelt eseményindítók származó adatok van adatokat olvasni az Azure Cosmos DB-a felhasználó legközelebb.

Ha szeretne adatokat tárolni az Azure Functions integrálható, és nem kell részletes indexelő, vagy ha a mellékleteket és médiafájlok tárolására van szüksége a [Azure Blob Storage-eseményindító](../azure-functions/functions-bindings-storage-blob.md) jobb megoldás lehet.

Az Azure Functions előnyei: 

* **Eseményvezérelt**. Az Azure Functions eseményvezérelt, és figyelheti a módosítása az Azure Cosmos DB-hírcsatorna. Ez azt jelenti, hogy nem kell figyelési logikát hozhat létre, akkor csak biztatok, hogy figyeli a változtatásokat. 

* **Korlátlan**. Függvények hajtsa végre a párhuzamos és a szolgáltatás terveket a jövőre fel annyi igény szerint. Állítsa a paramétereit.

* **A gyors feladatok jó**. A szolgáltatás új példányainak funkciók indít el, amikor az esemény akkor következik be, és lezárja őket, amint az függvény lefutott. Csak az idő a függvények futnak kell fizetnie.

Ha nem biztos abban, hogy Flow, Logic Apps, az Azure Functions vagy a webjobs-feladatok-e a megvalósítás esetén ajánlott használni, tekintse meg [Flow, Logic Apps, Functions és WebJobs közötti](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>További lépések

Most csatlakoztassuk ahhoz az Azure Cosmos DB és az Azure Functions valós: 

* [Az Azure Portalon egy Azure Cosmos DB-eseményindító létrehozása](https://aka.ms/cosmosdbtriggerportalfunc)
* [Az Azure Functions HTTP-trigger létrehozása Azure Cosmos DB bemeneti kötéssel](https://aka.ms/cosmosdbinputbind)
* [Az Azure Cosmos DB-kötések és triggerek](../azure-functions/functions-bindings-cosmosdb.md)


 



