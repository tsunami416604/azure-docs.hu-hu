---
title: Hírcsatorna-processzorkönyvtár módosítása az Azure Cosmos DB-ben
description: Ismerje meg, hogyan használhatja az Azure Cosmos DB változáscsatorna-processzor könyvtárát a módosítási hírcsatorna, a változáscsatorna-processzor összetevőinek olvasásához
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273309"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>A változáscsatorna feldolgozója az Azure Cosmos DB-ben 

A változáscsatorna-processzor az [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)része. Leegyszerűsíti a változáscsatorna olvasásának folyamatát, és hatékonyan osztja el az eseményfeldolgozást több fogyasztó között.

A változáscsatorna-processzorkönyvtár fő előnye a hibatűrő viselkedése, amely biztosítja a változáscsatorna összes eseményének "legalább egyszer" kézbesítését.

## <a name="components-of-the-change-feed-processor"></a>A változásadagolási processzor összetevői

A változás-előtolási processzor megvalósításának négy fő összetevője van: 

1. **A megfigyelt tároló:** A figyelt tároló rendelkezik az adatokat, amelyekből a változáscsatorna jön létre. A figyelt tároló minden lapka és frissítés a tároló módosítási hírcsatornájában tükröződik.

1. **A lízingkonténer:** A bérlettároló állapottárolóként működik, és a változáscsatorna feldolgozása több dolgozó között feldolgozó. A címbérlet-tároló a figyelt tárolóval azonos fiókban vagy egy külön fiókban tárolható. 

1. **A házigazda:** Az állomás olyan alkalmazáspéldány, amely a változáscsatorna-processzort használja a változások figyelésére. Az azonos címbérlet-konfigurációval rendelkező példányok párhuzamosan futhatnak, de minden példánynak más **példánynévvel kell rendelkeznie.** 

1. **A meghatalmazott:** A meghatalmazott az a kód, amely meghatározza, hogy Ön, a fejlesztő mit szeretne tenni a változáscsatorna-feldolgozó által leolvasható módosítások minden egyes kötegével. 

Ha jobban meg szeretné tudni, hogyan működik együtt a változáscsatorna-processzor e négy eleme, tekintsen meg egy példát az alábbi ábrán. A figyelt tároló tárolja a dokumentumokat, és a "Város" a partíciókulcs. Azt látjuk, hogy a partíciókulcs-értékek elemeket tartalmazó tartományokban vannak elosztva. Két állomáspéldány létezik, és a változáscsatorna-feldolgozó különböző partíciókulcs-értékeket rendel minden példányhoz a számítási eloszlás maximalizálása érdekében. Minden tartomány párhuzamosan olvasható, és a folyamat a lízingtároló más tartományaitól elkülönítve marad.

![Példa a hírcsatorna-feldolgozó módosítása](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>A módosítási hírcsatorna-processzor megvalósítása

A belépési pont mindig a figyelt `Container` tároló, `GetChangeFeedProcessorBuilder`egy ön által hívott példányból:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Ahol az első paraméter egy külön név, amely leírja a processzor célját, a második név pedig a delegált implementációja, amely kezeli a módosításokat. 

Egy meghatalmazott például a következő:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Végül meg kell adnia ennek `WithInstanceName` a processzorpéldánynak a nevét, `WithLeaseContainer`és amely a címbérlet állapotát a tárolója.

A `Build` hívás sal a `StartAsync`.

## <a name="processing-life-cycle"></a>Feldolgozási életciklus

A gazdapéldány normál életciklusa a következő:

1. Olvassa el a változás hírcsatornát.
1. Ha nincsenek módosítások, aludjon egy előre meghatározott ideig (testreszabható a Szerkesztőben), `WithPollInterval` és lépjen #1.
1. Ha vannak módosítások, küldje el őket a **meghatalmazottnak.**
1. Amikor a meghatalmazott **sikeresen**befejezi a módosítások feldolgozását, frissítse a címbérlet-tárolót a legújabb feldolgozott időpontmal, és lépjen #1.

## <a name="error-handling"></a>Hibakezelés

A változáscsatorna-processzor rugalmas a felhasználói kódhibákkal szemben. Ez azt jelenti, hogy ha a delegált megvalósítása nem kezelt kivétellel (#4 lépéssel) rendelkezik, akkor az adott módosítási köteget feldolgozó szál feldolgozása le áll, és új szál jön létre. Az új szál ellenőrzi, hogy melyik volt a legutóbbi időpont, amellyel a bérlettároló rendelkezik a partíciókulcs-értékek tartományához, és onnan újraindul, és hatékonyan elküldi ugyanazt a módosítási köteget a delegáltnak. Ez a viselkedés addig folytatódik, amíg a meghatalmazott helyesen nem dolgozza fel a módosításokat, és ez az oka annak, hogy a változáscsatorna-feldolgozó "legalább egyszer" garanciával rendelkezik, mert ha a delegált kód eldob, újra megpróbálja újra az adott köteget.

## <a name="dynamic-scaling"></a>Dinamikus méretezés

Ahogy a bevezetés során említettük, a változáscsatorna-feldolgozó automatikusan el tudja osztani a számítási adatokat több példány között. Az alkalmazás több példányát is telepítheti a változáscsatorna-feldolgozó használatával, és kihasználhatja azt, az egyetlen legfontosabb követelmény a következő:

1. Minden példánynak azonos címbérleti tárolókonfigurációval kell rendelkeznie.
1. Minden példánynak azonos munkafolyamatnévvel kell rendelkeznie.
1. Minden példánynak más példánynévvel`WithInstanceName`( kell rendelkeznie .

Ha ez a három feltétel érvényes, majd a változáscsatorna-feldolgozó egyenlő terjesztési algoritmus használatával elosztja a címbérletek a lízingtárolóban az összes futó példányok között, és párhuzamosítani számít. Egy bérlet csak egy példány tulajdonában lehet egy adott időpontban, így a példányok maximális száma megegyezik a bérletek számával.

A példányok száma növekedhet és csökkenhet, és a változás-hírcsatorna-processzor dinamikusan módosítja a terhelést a megfelelő újraelosztással.

Ezenkívül a változáscsatorna-feldolgozó dinamikusan alkalmazkodhat a tárolók méretezéséhez az átviteli vagy tárolási növekedés miatt. A tároló növekedésével a változáscsatorna-feldolgozó transzparens módon kezeli ezeket a forgatókönyveket a bérletek dinamikus növelésével és az új bérletek meglévő példányok közötti elosztásával.

## <a name="change-feed-and-provisioned-throughput"></a>Csatorna és a kiépített átviteli felvétel módosítása

A felhasznált RUs-ok díja díja van, mivel a Cosmos-tárolókba való adatáthelyezés mindig a RUs-okat használja fel. A lízingtároló által felhasznált RUs-ok díja felszámítása.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a hírcsatorna-feldolgozó módosításáról:

* [A módosítási hírcsatorna áttekintése](change-feed.md)
* [Az áttelepítés a módosítási hírcsatorna-processzorkönyvtárból](how-to-migrate-from-change-feed-library.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
* [Változáscsatorna-feldolgozó indításának időpontja](how-to-configure-change-feed-start-time.md)
