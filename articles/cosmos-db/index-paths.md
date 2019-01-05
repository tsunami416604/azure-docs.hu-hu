---
title: Az Azure Cosmos DB index görbék használata
description: Az Azure Cosmos DB indexet-útvonalak áttekintése
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: c22d8d69284c546a4fccc86302672d81ce65b9e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032771"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Az Azure Cosmos DB indexet-útvonalak

Index elérési utakat az Azure Cosmos DB használatával, vagy kizárja a megadott elérési útra indexelésének kiválaszthatja. Egyedi elérési utak kiválasztása biztosít továbbfejlesztett írási teljesítmény- és alsó index, amelyben a lekérdezési mintáit tudja forgatókönyvekhez. A gyökér elérési utak kezdés index (`/`) helyettesítő operátor, és általában a `?` helyettesítő operátor. Ez a minta azt jelzi, hogy nincsenek-e több lehetséges értékek a előtag. Ha például a lekérdezések kiszolgálása érdekében `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, egy index elérési útját meg kell adni `/familyName/?` a tároló index házirendben.

Index elérési utakat is használhatja a `*` helyettesítő operátor számára az elérési út rekurzív módon az előtag alapján viselkedésének megadása. Ha például `/payload/*` kizárása a hasznos adat tulajdonság található minden elemet az indexelő is használható.

## <a name="common-patterns-for-index-paths"></a>Az index elérési utak gyakori minták

Az alábbiakban a közös minta indexet-útvonalak megadása:

| **Elérési út** | **Leírás és használati eset** |
| ---------- | ------- |
| /   | A gyűjtemény alapértelmezett elérési utat. A rekurzív, és a teljes dokumentum fa vonatkozik.|
| / prop /?  | Index elérési út az alábbihoz hasonló-lekérdezések kiszolgálása érdekében szükséges (kivonatoló vagy tartomány típusú, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop  |
| / prop / *  | A megadott címke alatti összes elérési utat index elérési útja. A következő lekérdezéseket működik<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop.nextprop = "érték"<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop |
| / Kellékek / [] /?  | Index kell felelni az iteráció és a JOIN lekérdezéseket az Pole binárních hodnot fejlécekké például ["a", "b", "c"] elérési útja:<br><br>A címke a collection.props címkézése VÁLASSZA WHERE címke = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props VÁLASSZA címke > 5 ahol címkézése  |
| [] /subprop/ /props/? | Iteráció kiszolgálásához szükséges index elérési út és a JOIN lekérdezéseket az objektumok, tömbök, például [{subprop: "a"}, {subprop: "b"}]:<br><br>A címke a collection.props címkézése VÁLASSZA WHERE tag.subprop = "érték"<br><br>Válassza ki címkét a gyűjtemény c ILLESZTÉSI címke IN c.props WHERE tag.subprop = "érték" |
| / prop/subprop /? | -Lekérdezések kiszolgálása érdekében szükség index elérési (kivonatoló vagy tartomány típusú, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5  |

Amikor egyéni index elérési utak, módosítania kell a adja meg az alapértelmezett indexelési szabály a teljes elem, a speciális elérési kimaradásával `/*`.

## <a name="next-steps"></a>További lépések

További információ az indexelő az Azure Cosmos DB az alábbi cikkeket:

- [Az indexelő áttekintése](index-overview.md)
- [Az Azure Cosmos DB indexelési szabályzatok](indexing-policies.md)
- [Az Azure Cosmos DB index típusa](index-types.md)
