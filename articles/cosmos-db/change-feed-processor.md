---
title: A hírcsatorna-feldolgozó függvénytárának módosítása Azure Cosmos DB
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB változása a hírcsatorna-feldolgozó függvénytárat a változási hírcsatorna beolvasásához
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 3e7f5e46068844da538864fdfaa03ca7023e4372
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445566"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Adatcsatorna-processzor módosítása Azure Cosmos DB 

A Change feed processzor a [Azure Cosmos db SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3)részét képezi. Leegyszerűsíti a változási csatorna olvasásának folyamatát, és az események feldolgozását a több fogyasztó számára is hatékonyan terjeszti.

A Change feed Processor Library fő előnye a hibatűrő viselkedés, amely biztosítja a változási hírcsatornában lévő összes esemény "legalább egyszeri" kézbesítését.

## <a name="components-of-the-change-feed-processor"></a>A módosítási hírcsatorna processzorának összetevői

A módosítási hírcsatorna processzorának megvalósításának négy fő összetevője van: 

1. **A figyelt tároló:** A figyelt tárolóban szerepelnek azok az adatok, amelyekről a változási csatornát létrehozták. A figyelt tároló összes beszúrása és frissítése megjelenik a tároló változási hírcsatornájában.

1. **A bérlet tárolója:** A bérlet tároló állapot-tárolóként működik, és koordinálja a változási csatornát több feldolgozón keresztül. A bérlet tároló a figyelt tárolóval megegyező fiókban vagy egy külön fiókban is tárolható. 

1. **A gazdagép:** A gazdagép egy olyan alkalmazás-példány, amely a változási hírcsatorna-feldolgozó használatával figyeli a módosításokat. Az azonos címbérleti konfigurációval rendelkező példányok párhuzamosan futhatnak, de minden példánynak más **példánynév**is kell lennie. 

1. **A delegált:** A delegált az a kód, amely meghatározza, hogy az Ön és a fejlesztő milyen módosításokat szeretne végrehajtani a változási hírcsatorna-feldolgozó által beolvasott minden egyes kötegben. 

Ha szeretné jobban megismerni, hogy a változási hírcsatorna processzorának négy eleme hogyan működik együtt, tekintsük át az alábbi ábrán látható példát. A figyelt tároló tárolja a dokumentumokat, és a "City" partíciót használja a partíciós kulcsként. Láthatjuk, hogy a partíciós kulcs értékei az elemeket tartalmazó tartományokban vannak elosztva. Két gazdagép-példány létezik, és a módosítási hírcsatorna processzora különböző tartományokat rendel az egyes példányokhoz a számítási eloszlás maximalizálása érdekében. Minden tartományt párhuzamosan kell beolvasni, és az előrehaladását a bérlet tárolójában lévő többi tartománytól elkülönítve kell karbantartani.

![Példa a hírcsatorna processzorának módosítására](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>A változási csatorna processzorának implementálása

A belépési pont mindig a figyelt tároló, `Container` `GetChangeFeedProcessorBuilder`meghívott példányból:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Ahol az első paraméter egy különálló név, amely leírja a processzor célját, a második pedig a módosításokat kezelő delegált implementáció. 

Egy delegált példa:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Végül megadhatja a processzor-példány nevét a `WithInstanceName` és azt a tárolót, amely a bérlet állapotát `WithLeaseContainer`sal tartja karban.

A `Build` meghívásával elindíthatja a processzor példányát, amely a `StartAsync`meghívásával indítható el.

## <a name="processing-life-cycle"></a>Életciklus feldolgozása

A gazdagép-példányok normál életciklusa:

1. Olvassa el a változási csatornát.
1. Ha nincsenek változások, az alvó állapotot előre definiált ideig (testreszabható `WithPollInterval` a Builder-ben), és nyissa meg a #1.
1. Ha vannak változások, küldje el azokat a **delegált**számára.
1. Ha a delegálás **sikeresen**dolgozza fel a módosításokat, frissítse a címbérleti tárolót a legutóbbi feldolgozott időponttal, és lépjen #1.

## <a name="error-handling"></a>Hibakezelés

A módosítási hírcsatorna processzora a felhasználói kódok hibáira is rugalmas. Ez azt jelenti, hogy ha a delegált implementációja kezeletlen kivételt tartalmaz (#4. lépés), akkor a rendszer leállítja a szál feldolgozását, és létrehoz egy új szálat. Az új szál azt vizsgálja, hogy a címbérleti tároló milyen legkésőbbi időpontot adott a partíciós kulcs értékeinek, és onnan indítsa újra a rendszert, és így gyakorlatilag ugyanazt a köteget küldi el a delegált állapotnak. Ez a viselkedés mindaddig folytatódni fog, amíg a delegált nem dolgozza fel megfelelően a módosításokat, és ez az oka, hogy a változási hírcsatorna processzorának "legalább egyszer" garanciája van, mert ha a delegált kód dob, akkor újra próbálkozik a kötegtel.

## <a name="dynamic-scaling"></a>Dinamikus méretezés

Ahogy az a bevezetésben is említettük, a változási hírcsatorna processzora több példányon is eloszthatja a számítási kapacitást. Az alkalmazás több példányát is üzembe helyezheti az adatmódosítási folyamattal, és kihasználhatja, az egyetlen kulcsfontosságú követelmény a következő:

1. Minden példánynak ugyanazzal a bérlet-tároló konfigurációval kell rendelkeznie.
1. Minden példánynak ugyanazzal a munkafolyamat-névvel kell rendelkeznie.
1. Minden példánynak más példánynév (`WithInstanceName`) kell lennie.

Ha ezt a három feltételt alkalmazza, akkor a módosítási hírcsatorna processzora egyenlő terjesztési algoritmussal osztja szét a bérleti tároló összes bérletét az összes futó példányon és a integrálással számításon keresztül. Egy bérlet csak egy példány tulajdonosa lehet egy adott időpontban, így a példányok maximális száma megegyezik a bérletek számával.

A példányok száma növelhető és csökkenthető, és a módosítási hírcsatorna processzora a megfelelő újraterjesztéssel dinamikusan módosítja a terhelést.

Emellett a változási hírcsatorna processzora dinamikusan alkalmazkodik a tárolók méretezéséhez, az átviteli sebesség vagy a tárterület növekedése miatt. Ha a tároló növekszik, a változási hírcsatorna-feldolgozó transzparens módon kezeli ezeket a forgatókönyveket a bérletek dinamikus növelésével és az új bérletek meglévő példányok közötti elosztásával.

## <a name="change-feed-and-provisioned-throughput"></a>A hírcsatorna és a kiosztott átviteli sebesség módosítása

A felszámított RUs díja, mivel a Cosmos-tárolókban lévő és kívüli adatáthelyezés mindig RUs-t használ. A bérleti tároló által felhasznált RUs díjait kell fizetnie.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Áttelepítés a Change feed Processor Library webhelyről](how-to-migrate-from-change-feed-library.md)
* [A Change feed kalkulátor használata](how-to-use-change-feed-estimator.md)
* [Hírcsatorna-processzor kezdő időpontjának módosítása](how-to-configure-change-feed-start-time.md)
