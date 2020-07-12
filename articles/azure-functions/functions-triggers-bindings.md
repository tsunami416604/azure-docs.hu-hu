---
title: Eseményindítók és kötések Azure Functions
description: Megtudhatja, hogyan használhatja az eseményindítókat és a kötéseket az Azure-függvények online eseményekhez és felhőalapú szolgáltatásokhoz való összekapcsolásához.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: ddcf6758c8c648678c69070fa5b65ae6c4947018
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252691"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-eseményindítók és -kötések – fogalmak

Ebből a cikkből megtudhatja, hogy milyen magas szintű fogalmakat indít a functions-eseményindítók és-kötések.

Az eseményindítók a függvény futtatását okozzák. Egy eseményindító határozza meg a függvény meghívásának módját, és a függvénynek pontosan egy eseményindítóval kell rendelkeznie. A triggerekhez társított adatok tartoznak, amelyek gyakran a függvény hasznos adataiként vannak megadva. 

A függvények kötése egy másik erőforrás a függvényhez való deklaratív csatlakoztatásának módja. a kötések *bemeneti Kötésként*, *kimeneti Kötésként*vagy mindkettőként is csatlakoztathatók. A kötések adatai a függvények számára paraméterekként vannak megadva.

A különböző kötéseket igény szerint kombinálhatja. A kötések opcionálisak, és egy függvény egy vagy több bemeneti, illetve kimeneti kötéssel is rendelkezhet.

Az eseményindítók és kötések lehetővé teszik a más szolgáltatásokhoz való rögzítjük való hozzáférés elkerülését. A függvény függvényparaméterekben kapja meg az adatokat (például egy üzenetsor üzenetének tartalmát). Az adatokat (például egy üzenetsor üzenetének tartalmát) a függvény által visszaadott értékek használatával küldheti el. 

Tekintse át az alábbi példákat a különböző függvények megvalósítására.

| Példaforgatókönyv | Eseményindító | Bemeneti kötés | Kimeneti kötés |
|-------------|---------|---------------|----------------|
| Egy új üzenetsor-üzenet érkezik, amely egy függvény futtatásával ír egy másik várólistára. | Várólista<sup>*</sup> | *Nincs* | Várólista<sup>*</sup> |
|Az ütemezett feladatok beolvassák Blob Storage tartalmát, és létrehoz egy új Cosmos DB dokumentumot. | Időzítő | Blob Storage | Cosmos DB |
|A Event Grid egy rendszerképet olvas be a Blob Storageról, és a Cosmos DB dokumentumból küld e-mailt. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Egy webhook, amely Microsoft Grapht használ az Excel-lapok frissítéséhez. | HTTP | *Nincs* | Microsoft Graph |

<sup>\*</sup>Különböző várólistákat jelöl

Ezek a példák nem teljes körűek, de az eseményindítók és kötések együttes használatának szemléltetésére szolgálnak.

###  <a name="trigger-and-binding-definitions"></a>Trigger-és kötési definíciók

Az eseményindítók és kötések a fejlesztési módszertől függően eltérő módon vannak meghatározva.

| Platform | Az eseményindítók és kötések konfigurálása... |
|-------------|--------------------------------------------|
| C# osztály könyvtára | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek C#-attribútumokkal |
| Minden más (beleértve a Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.js](./functions-reference.md) frissítése ([séma](http://json.schemastore.org/function)) |

A portál egy felhasználói felületet biztosít ehhez a konfigurációhoz, de a fájl szerkesztéséhez közvetlenül is megnyithatja a függvény **integrálás** lapján elérhető **speciális szerkesztőt** .

A .NET-ben a paraméter típusa határozza meg a bemeneti adatok adattípusát. Például a paranccsal `string` kötést hozhat létre egy üzenetsor-trigger szövegéhez, egy byte-tömböt, amely binárisként és egyéni típusként egy objektumra deszerializálható.

A dinamikusan beírt nyelvek (például JavaScript) esetében használja a `dataType` *function.js* fájljában található tulajdonságot. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, állítsa a következőre `dataType` `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

A és a további beállításai `dataType` `stream` `string` .

## <a name="binding-direction"></a>Kötési irány

Minden eseményindító és kötés rendelkezik egy `direction` tulajdonsággal a (z) [function.js](./functions-reference.md) fájlban:

- Az eseményindítók esetében az irány mindig`in`
- A bemeneti és kimeneti kötések használata `in` és`out`
- Egyes kötések speciális irányt támogatnak `inout` . A használata esetén `inout` csak a **speciális szerkesztő** érhető el a portál **integrálás** lapján.

Ha az [attribútumokat egy osztály-függvénytárban](functions-dotnet-class-library.md) konfigurálja az eseményindítók és kötések konfigurálásához, az irány egy attribútum konstruktorában van megadva, vagy a paraméter típusa alapján következtethető ki.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

További információ arról, hogy mely kötések érhetők el előzetes verzióban, vagy hogy a rendszer az éles használatra jóváhagyja a [támogatott nyelveket](supported-languages.md).

## <a name="resources"></a>További források
- [Kötési kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
- [Kötési kifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat
  - [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](functions-test-a-function.md)
  - [Nem HTTP által aktivált függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák feldolgozása](./functions-bindings-errors.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Functions kötési bővítmények regisztrálása](./functions-bindings-register.md)
