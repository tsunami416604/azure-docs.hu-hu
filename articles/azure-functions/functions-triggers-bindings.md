---
title: Eseményindítók és kötések Azure Functions
description: Megtudhatja, hogyan használhatja az eseményindítókat és a kötéseket az Azure-függvények online eseményekhez és felhőalapú szolgáltatásokhoz való összekapcsolásához.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226567"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Triggerek és kötések Azure Functions fogalmak

Ebből a cikkből megtudhatja, hogy milyen magas szintű fogalmakat indít a functions-eseményindítók és-kötések.

Az eseményindítók a függvény futtatását okozzák. Egy eseményindító határozza meg a függvény meghívásának módját, és a függvénynek pontosan egy eseményindítóval kell rendelkeznie. Az eseményindítók társított adatokkal rendelkeznek, amelyek gyakran a függvény hasznos adattartalmaként vannak megadva. 

A függvények kötése egy másik erőforrás a függvényhez való deklaratív csatlakoztatásának módja. a kötések *bemeneti Kötésként*, *kimeneti Kötésként*vagy mindkettőként is csatlakoztathatók. A kötések adatait paraméterként a függvény kapja meg.

Az igényeinek megfelelően összekeverheti és egyeztetheti a különböző kötéseket. A kötések nem kötelezőek, és egy függvénynek lehet egy vagy több bemeneti és/vagy kimeneti kötése.

Az eseményindítók és kötések lehetővé teszik a más szolgáltatásokhoz való rögzítjük való hozzáférés elkerülését. A függvény adatokat fogad (például egy üzenetsor tartalmát) a függvény paraméterei között. Az adatküldés (például üzenetsor létrehozása) a függvény visszatérési értékével végezhető el. 

Tekintse át az alábbi példákat a különböző függvények megvalósítására.

| Példaforgatókönyv | Eseményindító | Bemeneti kötés | Kimeneti kötés |
|-------------|---------|---------------|----------------|
| Egy új üzenetsor-üzenet érkezik, amely egy függvény futtatásával ír egy másik várólistára. | Üzenetsor<sup>*</sup> | *NEz egy* | Üzenetsor<sup>*</sup> |
|Az ütemezett feladatok beolvassák Blob Storage tartalmát, és létrehoz egy új Cosmos DB dokumentumot. | Időzítő | Blob Storage | Cosmos DB |
|A Event Grid egy rendszerképet olvas be a Blob Storageról, és a Cosmos DB dokumentumból küld e-mailt. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Egy webhook, amely Microsoft Grapht használ az Excel-lapok frissítéséhez. | HTTP | *NEz egy* | Microsoft Graph |

<sup>\*</sup> Különböző várólistákat jelöl

Ezek a példák nem teljes körűek, de az eseményindítók és kötések együttes használatának szemléltetésére szolgálnak.

###  <a name="trigger-and-binding-definitions"></a>Trigger-és kötési definíciók

Az eseményindítók és kötések a fejlesztési módszertől függően eltérő módon vannak meghatározva.

| Platform | Az eseményindítók és kötések konfigurálása... |
|-------------|--------------------------------------------|
| C#osztály könyvtára | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő metódusok és paraméterek C# az attribútumokkal |
| Minden más (beleértve a Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;frissítési [function. JSON](./functions-reference.md) ([séma](http://json.schemastore.org/function)) |

A portál egy felhasználói felületet biztosít ehhez a konfigurációhoz, de a fájl szerkesztéséhez közvetlenül is megnyithatja a függvény **integrálás** lapján elérhető **speciális szerkesztőt** .

A .NET-ben a paraméter típusa határozza meg a bemeneti adatok adattípusát. Például a `string` használatával kösse a várólista-trigger szövegét, egy bájtos tömböt, amely binárisként és egyéni típusként egy objektumra deszerializálható.

A dinamikusan beírt nyelvek (például a JavaScript) esetében használja a *function. JSON* fájl `dataType` tulajdonságát. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, állítsa be `dataType` `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

A `dataType` további beállításai `stream` és `string`.

## <a name="binding-direction"></a>Kötési irány

Minden eseményindító és kötés `direction` tulajdonsággal rendelkezik a [function. JSON](./functions-reference.md) fájlban:

- Az eseményindítók esetében az irány mindig `in`
- A bemeneti és kimeneti kötések `in` és `out` használnak
- Egyes kötések speciális irányt `inout`nak. Ha `inout`használ, csak a **speciális szerkesztő** érhető el a portál **integrálás** lapján.

Ha az [attribútumokat egy osztály-függvénytárban](functions-dotnet-class-library.md) konfigurálja az eseményindítók és kötések konfigurálásához, az irány egy attribútum konstruktorában van megadva, vagy a paraméter típusa alapján következtethető ki.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

További információ arról, hogy mely kötések érhetők el előzetes verzióban, vagy hogy a rendszer az éles használatra jóváhagyja a [támogatott nyelveket](supported-languages.md).

## <a name="resources"></a>Erőforrások
- [Kötési kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
- [Kötési kifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat
  - [Stratégiák a kód teszteléséhez Azure Functions](functions-test-a-function.md)
  - [Nem HTTP-triggert futtató függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák feldolgozása](./functions-bindings-errors.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Functions kötési bővítmények regisztrálása](./functions-bindings-register.md)
