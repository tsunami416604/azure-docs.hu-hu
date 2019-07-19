---
title: A Azure Cosmos DB hírcsatorna-feldolgozó függvénytárának módosítása
description: A Azure Cosmos DB változás a hírcsatorna-feldolgozó kódtár használatával.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 42b7cd8a60e70ab75afc30910c46eb49f1f6d62a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000954"
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

A belépési pont mindig a figyelt tároló, amely egy `Container` meghívott `GetChangeFeedProcessorBuilder`példányból áll:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Ahol az első paraméter egy különálló név, amely leírja a processzor célját, a második pedig a módosításokat kezelő delegált implementáció. 

Egy delegált példa:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Végül meghatároz egy nevet ehhez a processzor `WithInstanceName` -példányhoz a és a használatával, amely a bérlet `WithLeaseContainer`állapotának fenntartására szolgáló tároló.

A `Build` hívással megadhatja a processzor azon példányát, amelyet `StartAsync`elindíthat a hívásával.

## <a name="processing-life-cycle"></a>Életciklus feldolgozása

A gazdagép-példányok normál életciklusa:

1. Olvassa el a változási csatornát.
1. Ha nincsenek változások, Aludjon előre meghatározott ideig (testreszabható `WithPollInterval` a-ben a Builder-ben), és lépjen a #1.
1. Ha vannak változások, küldje el azokat a **delegált**számára.
1. Ha a delegálás **sikeresen**dolgozza fel a módosításokat, frissítse a címbérleti tárolót a legutóbbi feldolgozott időponttal, és lépjen #1.

## <a name="error-handling"></a>Hibakezelés

A módosítási hírcsatorna processzora a felhasználói kódok hibáira is rugalmas. Ez azt jelenti, hogy ha a delegált implementációja kezeletlen kivételt tartalmaz (#4. lépés), akkor a rendszer leállítja a szál feldolgozását, és létrehoz egy új szálat. Az új szál azt vizsgálja, hogy a címbérleti tároló milyen legkésőbbi időpontot adott a partíciós kulcs értékeinek, és onnan indítsa újra a rendszert, és így gyakorlatilag ugyanazt a köteget küldi el a delegált állapotnak. Ez a viselkedés mindaddig folytatódni fog, amíg a delegált nem dolgozza fel megfelelően a módosításokat, és ez az oka, hogy a változási hírcsatorna processzorának "legalább egyszer" garanciája van, mert ha a delegált kód dob, akkor újra próbálkozik a kötegtel.

## <a name="dynamic-scaling"></a>Dinamikus méretezés

Ahogy az a bevezetésben is említettük, a változási hírcsatorna processzora több példányon is eloszthatja a számítási kapacitást. Az alkalmazás több példányát is üzembe helyezheti az adatmódosítási folyamattal, és kihasználhatja, az egyetlen kulcsfontosságú követelmény a következő:

1. Minden példánynak ugyanazzal a bérlet-tároló konfigurációval kell rendelkeznie.
1. Minden példánynak ugyanazzal a munkafolyamat-névvel kell rendelkeznie.
1. Minden példánynak más példánynév (`WithInstanceName`) névvel kell rendelkeznie.

Ha ezt a három feltételt alkalmazza, akkor a módosítási hírcsatorna processzora egyenlő terjesztési algoritmussal osztja szét a bérleti tároló összes bérletét az összes futó példányon és a integrálással számításon keresztül. Egy bérlet csak egy példány tulajdonosa lehet egy adott időpontban, így a példányok maximális száma megegyezik a bérletek számával.

A példányok növekednek és csökkennek, és a változási hírcsatorna processzora a megfelelő újraelosztással dinamikusan módosítja a terhelést.

## <a name="change-feed-and-provisioned-throughput"></a>A hírcsatorna és a kiosztott átviteli sebesség módosítása

A felszámított RUs díja, mivel a Cosmos-tárolókban lévő és kívüli adatáthelyezés mindig RUs-t használ. A bérleti tároló által felhasznált RUs díjait kell fizetnie.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A módosítási csatorna olvasási módjai](read-change-feed.md)
* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
