---
title: Eseményindítók és kötések Azure Functions
description: Megtudhatja, hogyan használhatja az eseményindítókat és a kötéseket az Azure-függvények online eseményekhez és felhőalapú szolgáltatásokhoz való összekapcsolásához.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 914158ba7cfcc7530120d427c62e69036b3bb156
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085090"
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
| Egy új üzenetsor-üzenet érkezik, amely egy függvény futtatásával ír egy másik várólistára. | Queue<sup>*</sup> | *Nincsenek* | Queue<sup>*</sup> |
|Az ütemezett feladatok beolvassák Blob Storage tartalmát, és létrehoz egy új Cosmos DB dokumentumot. | Időzítő | Blobtároló | Cosmos DB |
|A Event Grid egy rendszerképet olvas be a Blob Storageról, és a Cosmos DB dokumentumból küld e-mailt. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Egy webhook, amely Microsoft Grapht használ az Excel-lapok frissítéséhez. | HTTP | *Nincsenek* | Microsoft Graph |

<sup>\*</sup>Különböző várólistákat jelöl

Ezek a példák nem teljes körűek, de az eseményindítók és kötések együttes használatának szemléltetésére szolgálnak.

###  <a name="trigger-and-binding-definitions"></a>Trigger-és kötési definíciók

Az eseményindítók és kötések a fejlesztési módszertől függően eltérő módon vannak meghatározva.

| Platform | Az eseményindítók és kötések konfigurálása... |
|-------------|--------------------------------------------|
| C#osztály könyvtára | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek az C# attribútumokkal |
| Minden más (beleértve a Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function. JSON](./functions-reference.md) frissítése ([séma](http://json.schemastore.org/function)) |

A portál egy felhasználói felületet biztosít ehhez a konfigurációhoz, de a fájl szerkesztéséhez közvetlenül is megnyithatja a függvény **integrálás** lapján elérhető **speciális szerkesztőt** .

A .NET-ben a paraméter típusa határozza meg a bemeneti adatok adattípusát. Például a paranccsal kötést hozhat `string` létre egy üzenetsor-trigger szövegéhez, egy byte-tömböt, amely binárisként és egyéni típusként egy objektumra deszerializálható.

A dinamikusan beírt nyelvek (például a JavaScript) a `dataType` *function. JSON* fájlban található tulajdonságot használják. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, állítsa `dataType` `binary`a következőre:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

A `dataType` `stream` és a`string`további beállításai.

## <a name="binding-direction"></a>Kötési irány

Minden eseményindító és kötés rendelkezik egy `direction` tulajdonsággal a [function. JSON](./functions-reference.md) fájlban:

- Az eseményindítók esetében az irány mindig`in`
- A bemeneti és kimeneti kötések `in` használata és`out`
- Egyes kötések speciális irányt `inout`támogatnak. A használata `inout`esetén csak a **speciális szerkesztő** érhető el a portál **integrálás** lapján.

Ha az [attribútumokat egy osztály](functions-dotnet-class-library.md) -függvénytárban konfigurálja az eseményindítók és kötések konfigurálásához, az irány egy attribútum konstruktorában van megadva, vagy a paraméter típusa alapján következtethető ki.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

További információ arról, hogy mely kötések érhetők el előzetes verzióban, vagy hogy a rendszer az éles használatra jóváhagyja a [támogatott nyelveket](supported-languages.md).

## <a name="resources"></a>További források
- [Kötési kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
- [Kötési kifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat
  - [Stratégiák a kód teszteléséhez Azure Functions](functions-test-a-function.md)
  - [Nem HTTP-triggert futtató függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák feldolgozása](./functions-bindings-errors.md)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure Functions kötési bővítmények regisztrálása](./functions-bindings-register.md)
