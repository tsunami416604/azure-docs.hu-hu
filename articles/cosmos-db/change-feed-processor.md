---
title: A változáscsatorna feldolgozója az Azure Cosmos DB-ben
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Change feed processzort a változási hírcsatorna beolvasására, a változási hírcsatorna processzorának összetevőire
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.openlocfilehash: 4325f75ac8181e088d64e53d3f65e085a09c0224
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119409"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>A változáscsatorna feldolgozója az Azure Cosmos DB-ben

A Change feed processzor a [Azure Cosmos db SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3)részét képezi. Leegyszerűsíti a változási csatorna olvasásának folyamatát, és az események feldolgozását a több fogyasztó számára is hatékonyan terjeszti.

A Change feed Processor Library fő előnye a hibatűrő viselkedés, amely biztosítja a változási hírcsatornában lévő összes esemény "legalább egyszeri" kézbesítését.

## <a name="components-of-the-change-feed-processor"></a>A módosítási hírcsatorna processzorának összetevői

A változáscsatorna-feldolgozó implementálásának négy fő összetevője van:

1. **A monitorozott tároló:** A monitorozott tároló tartalmazza azokat az adatokat, amelyekből létrejön a változáscsatorna. A monitorozott tárolóba való beszúrások és a tároló frissítései megjelennek a tároló változáscsatornájában.

1. **A bérlettároló:** A bérlettároló állapottárolóként működik, és több feldolgozó között koordinálja a változáscsatorna feldolgozását. A bérlettároló tárolható ugyanabban a fiókban, mint a monitorozott tároló, de akár külön fiókban is.

1. **A gazdagép:** A gazdagép egy olyan alkalmazáspéldány, amely a változáscsatorna feldolgozóját használja a módosítások figyeléséhez. Az azonos bérletkonfigurációval rendelkező példányok párhuzamosan is futhatnak, de minden példánynak más **példánynévvel** kell rendelkeznie.

1. **A delegált:** A delegált az a kód, amely meghatározza, hogy Ön, mint fejlesztő mit szeretne tenni azon egyes módosításkötegekkel, amelyeket a változáscsatorna feldolgozója beolvas. 

Ha szeretné jobban megismerni, hogy a változási hírcsatorna processzorának négy eleme hogyan működik együtt, tekintsük át az alábbi ábrán látható példát. A figyelt tároló tárolja a dokumentumokat, és a "City" partíciót használja a partíciós kulcsként. Láthatjuk, hogy a partíciós kulcs értékei az elemeket tartalmazó tartományokban vannak elosztva. Két gazdagép-példány létezik, és a módosítási hírcsatorna processzora különböző tartományokat rendel az egyes példányokhoz a számítási eloszlás maximalizálása érdekében. Minden tartományt párhuzamosan kell beolvasni, és az előrehaladását a bérlet tárolójában lévő többi tartománytól elkülönítve kell karbantartani.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Példa a hírcsatorna processzorának módosítására" border="false":::

## <a name="implementing-the-change-feed-processor"></a>A változási csatorna processzorának implementálása

A belépési pont mindig a figyelt tároló, amely egy `Container` meghívott példányból áll `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Ahol az első paraméter egy különálló név, amely leírja a processzor célját, a második pedig a módosításokat kezelő delegált implementáció. 

Egy delegált példa:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Végül meghatároz egy nevet ehhez a processzor-példányhoz a `WithInstanceName` és a használatával, amely a bérlet állapotának fenntartására szolgáló tároló `WithLeaseContainer` .

A hívással megadhatja `Build` a processzor azon példányát, amelyet elindíthat a hívásával `StartAsync` .

## <a name="processing-life-cycle"></a>Életciklus feldolgozása

A gazdagéppéldány normál életciklusa:

1. Olvassa el a változási csatornát.
1. Ha nincsenek változások, Aludjon előre meghatározott ideig (testreszabható a `WithPollInterval` -ben a Builder-ben), és lépjen a #1.
1. Ha vannak változások, küldje el azokat a **delegált**számára.
1. Ha a delegálás **sikeresen**dolgozza fel a módosításokat, frissítse a címbérleti tárolót a legutóbbi feldolgozott időponttal, és lépjen #1.

## <a name="error-handling"></a>Hibakezelés

A módosítási hírcsatorna processzora a felhasználói kódok hibáira is rugalmas. Ez azt jelenti, hogy ha a delegált implementációja kezeletlen kivételt tartalmaz (#4. lépés), akkor a rendszer leállítja a szál feldolgozását, és létrehoz egy új szálat. Az új szál azt vizsgálja, hogy a címbérleti tároló milyen legkésőbbi időpontot adott a partíciós kulcs értékeinek, és onnan indítsa újra a rendszert, és így gyakorlatilag ugyanazt a köteget küldi el a delegált állapotnak. Ez a viselkedés mindaddig folytatódni fog, amíg a delegált nem dolgozza fel megfelelően a módosításokat, és ez az oka, hogy a változási hírcsatorna processzorának "legalább egyszer" garanciája van, mert ha a delegált kód kivételt jelez, akkor a köteget újra próbálkozik.

Ha meg szeretné akadályozni, hogy a Change feed processzora "ragadt" állapotba lépjen, és ne próbálkozzon ugyanazzal a módosítással, adja hozzá a meghatalmazott kódjában található logikát, hogy a rendszer kivétel esetén a kézbesítetlen levelek várólistáján jegyezze fel a dokumentumokat. Ez a kialakítás biztosítja, hogy nyomon követheti a feldolgozatlan módosításokat, miközben továbbra is képes tovább dolgozni a jövőbeli változásokat. Előfordulhat, hogy a kézbesítetlen levelek várólistája egyszerűen egy másik Cosmos-tároló. A pontos adattár nem számít, csupán azt, hogy a feldolgozatlan módosítások megmaradnak.

Emellett a váltás a hírcsatorna- [kalkulátor](how-to-use-change-feed-estimator.md) használatával című cikk segítségével figyelheti a változási csatornához tartozó példányok állapotát, ahogy beolvasták a változási csatornát. A figyelésen kívül, ha a Change feed processzora "ragadt" állapotba kerül, és folyamatosan próbálkozik ugyanazzal a módosítással, akkor azt is megtudhatja, hogy a változási csatorna processzora a rendelkezésre álló erőforrások, például a processzor, a memória és a hálózati sávszélesség miatt marad-e hátra.

## <a name="deployment-unit"></a>Üzembe helyezési egység

Egyetlen módosítási hírcsatorna-feldolgozó egység egy vagy több példányból áll, amelyek azonos `processorName` és címbérleti tároló-konfigurációval rendelkeznek. Több üzembe helyezési egység is megadható, amelyek mindegyike különböző üzleti folyamattal rendelkezik a változásokhoz és minden központi telepítési egységhez, amely egy vagy több példányból áll. 

Előfordulhat például, hogy egy olyan központi telepítési egységgel rendelkezik, amely egy külső API-t indít el bármikor, ha megváltozik a tárolóban. Egy másik telepítési egység valós időben helyezheti át az adatátvitelt, valahányszor módosul a változás. Ha a figyelt tárolóban változás történik, az összes üzembe helyezési egység értesítést kap.

## <a name="dynamic-scaling"></a>Dinamikus méretezés

Ahogy korábban említettük, egy üzembe helyezési egységen belül egy vagy több példány is megadható. A központi telepítési egységen belüli számítási eloszlás kihasználása érdekében az egyetlen kulcsfontosságú követelmény a következő:

1. Minden példánynak ugyanazzal a bérlet-tároló konfigurációval kell rendelkeznie.
1. Minden példánynak azonosnak kell lennie `processorName` .
1. Minden példánynak különböző példánynévvel kell rendelkeznie (`WithInstanceName`).

Ha ezt a három feltételt alkalmazza, akkor a módosítási hírcsatorna processzora egyenlő terjesztési algoritmussal osztja el a bérleti tároló összes bérletét az adott üzembe helyezési egység összes futó példánya és a integrálással számítási szolgáltatás között. Egy bérlet csak egy példány tulajdonosa lehet egy adott időpontban, így a példányok maximális száma megegyezik a bérletek számával.

A példányok száma növelhető és csökkenthető, és a módosítási hírcsatorna processzora a megfelelő újraterjesztéssel dinamikusan módosítja a terhelést.

Emellett a változási hírcsatorna processzora dinamikusan alkalmazkodik a tárolók méretezéséhez, az átviteli sebesség vagy a tárterület növekedése miatt. Ha a tároló növekszik, a változási hírcsatorna-feldolgozó transzparens módon kezeli ezeket a forgatókönyveket a bérletek dinamikus növelésével és az új bérletek meglévő példányok közötti elosztásával.

## <a name="change-feed-and-provisioned-throughput"></a>A hírcsatorna és a kiosztott átviteli sebesség módosítása

A felszámított RUs díja, mivel a Cosmos-tárolókban lévő és kívüli adatáthelyezés mindig RUs-t használ. A bérleti tároló által felhasznált RUs díjait kell fizetnie.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Változáscsatorna lekérési modellje](change-feed-pull-model.md)
* [Áttelepítés a Change feed Processor Library webhelyről](how-to-migrate-from-change-feed-library.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
* [Változáscsatorna-feldolgozó indításának időpontja](how-to-configure-change-feed-start-time.md)
