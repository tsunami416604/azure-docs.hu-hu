---
title: Eseményindítók és kötések az Azure Functionsben
description: Ismerje meg, hogy az azure-függvényt online eseményekhez és felhőalapú szolgáltatásokhoz csatlakoztathatja az eseményindítók és kötések használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276503"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions elindítja és kötési fogalmak

Ebben a cikkben megismerheti a függvényeket körülvevő magas szintű fogalmakat, amelyek aktiválódnak és kötések.

Az eseményindítók okozzák a függvények futtatását. Az eseményindító határozza meg, hogyan kell egy függvénymeghívása, és egy függvény nek pontosan egy eseményindítóval kell rendelkeznie. A triggerekhez társított adatok tartoznak, amelyek gyakran a függvény hasznos adataiként vannak megadva. 

A függvényhez való kötés egy másik erőforrás deklaratív módon történő csatlakoztatásának módja a függvényhez; kötések csatlakoztathatók *bemeneti kötésként,* *kimeneti kötésként vagy mindkettőként.* A kötések adatai a függvények számára paraméterekként vannak megadva.

A különböző kötéseket igény szerint kombinálhatja. A kötések választhatóak, és egy függvény rendelkezhet egy vagy több bemeneti és/vagy kimeneti kötéssel.

Az eseményindítók és kötések segítségével elkerülheti a más szolgáltatásokhoz való hardcoding hozzáférést. A függvény függvényparaméterekben kapja meg az adatokat (például egy üzenetsor üzenetének tartalmát). Az adatokat (például egy üzenetsor üzenetének tartalmát) a függvény által visszaadott értékek használatával küldheti el. 

Vegye figyelembe az alábbi példákat arra, hogyan valósíthat meg különböző függvényeket.

| Példaforgatókönyv | Eseményindító | Bemeneti kötés | Kimenetkötés |
|-------------|---------|---------------|----------------|
| Új várólista-üzenet érkezik, amely egy függvényt futtat egy másik várólistába való íráshoz. | Várólista<sup>*</sup> | *Nincs* | Várólista<sup>*</sup> |
|Egy ütemezett feladat beolvassa a Blob Storage tartalmát, és létrehoz egy új Cosmos DB-dokumentumot. | Időzítő | Blob Storage | Cosmos DB |
|Az Event Grid segítségével olvashat egy képet a Blob Storage-ból és egy dokumentumot a Cosmos DB e-mailt küldeni. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Olyan webhook, amely a Microsoft Graph segítségével frissíti az Excel-lapokat. | HTTP | *Nincs* | Microsoft Graph |

<sup>\*</sup>Különböző várólistákat jelöl

Ezek a példák nem célja, hogy teljes körű, de rendelkezésre állnak, hogy bemutassa, hogyan használhatja az eseményindítók és kötések együtt.

###  <a name="trigger-and-binding-definitions"></a>Kiváltó és kötési definíciók

Az eseményindítók és kötések meghatározása a fejlesztési megközelítéstől függően eltérő.

| Platform | Az eseményindítókat és a kötéseket... |
|-------------|--------------------------------------------|
| C# osztálykönyvtár | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek C# attribútumokkal |
| Az összes többi (beleértve az Azure Portalt is) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.json](./functions-reference.md) ([séma](http://json.schemastore.org/function)) frissítése |

A portál felhasználói felületet biztosít ehhez a konfigurációhoz, de a fájl közvetlenül szerkeszthető a funkció **Integrálás** lapján elérhető **Speciális szerkesztő** megnyitásával.

A .NET-ben a paramétertípus határozza meg a bemeneti adatok adattípusát. Például egy `string` várólista-eseményindító szövegéhez, egy binárisként olvasandó bájttömbhöz és egy objektumhoz való szerializálást leváltó egyéni típushoz köthető.

A dinamikusan beírt nyelvek, például a `dataType` JavaScript esetében használja a *function.json fájl tulajdonságát.* Ha például egy HTTP-kérelem tartalmát bináris formátumban `binary`szeretné olvasni, állítsa a következőre: `dataType`

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Egyéb lehetőségek `dataType` `stream` vannak `string`és .

## <a name="binding-direction"></a>Kötésirány

Minden eseményindítónak és `direction` kötésnek van egy tulajdonsága a [function.json](./functions-reference.md) fájlban:

- Az eseményindítók esetében az irány mindig`in`
- A bemeneti és `in` kimeneti kötések használata és`out`
- Egyes kötések speciális `inout`irányt támogatnak. Ha a `inout`, csak a **Speciális szerkesztő** érhető el a **portál Integrálás** lapján.

Ha [egy osztálytár attribútumait](functions-dotnet-class-library.md) használja az eseményindítók és kötések konfigurálásához, az irány egy attribútumkonstruktorban vagy a paramétertípusból következtethet.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Arról, hogy mely kötések előzetes verzióban vannak megtekinthetők, vagy milyen termelési használatra vannak jóváhagyva, a Támogatott nyelvek című [témakörben](supported-languages.md)talál tájékoztatást.

## <a name="resources"></a>Források
- [Kötési kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure Függvény visszatérési értékének használata](./functions-bindings-return-value.md)
- [Kötési kifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat:
  - [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](functions-test-a-function.md)
  - [Nem HTTP által aktivált függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák kezelése](./functions-bindings-errors.md)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Functions kötésbővítményeinek regisztrálása](./functions-bindings-register.md)
