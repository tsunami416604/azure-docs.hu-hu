---
title: Eseményindítók és kötések az Azure Functions szolgáltatásban
description: Ismerje meg, eseményindítók és kötések használata az Azure-függvény online események és a felhőalapú szolgáltatásokhoz való csatlakozáshoz.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58889838"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Az Azure Functions eseményindítók és kötések fogalmak

Ez a cikk bemutatja a functions eseményindítók és kötések körülvevő fogalmait.

Eseményindítók olyan mi függvény futtatása okozhat. Eseményindító határozza meg, hogyan függvény meghívása és a következő függvénynek rendelkeznie kell a pontosan egy eseményindító. Eseményindítók társított adatokat, amely gyakran a függvény a hasznos. 

Egy függvény kötése egy módja egy másik erőforrás deklaratív kapcsolódni a függvény; kötések csatlakoztathatók *bemeneti kötések*, *kimeneti kötések*, vagy mindkettőt. A függvény kötések származó adatok megadott paraméterekként.

Szabadon kombinálhatók különböző kötések az igényeinek megfelelő. Kötések nem kötelező, és egy függvény előfordulhat, hogy rendelkezik egy vagy több bemeneti, illetve kimeneti kötés.

Eseményindítók és kötések lehetővé teszik, más szolgáltatások elérését hardcoding elkerülése érdekében. A függvény adatok (például üzenetsori üzenet tartalma) függvény paraméterei fogadása. A visszaadott érték a függvény akkor küldjön adatokat (például üzenetsori üzenetek létrehozásához). 

Vegye figyelembe a következő példák a különböző funkciók hogyan implementálhatja.

| Példaforgatókönyv | Eseményindító | Bemeneti kötést | Kimeneti kötés |
|-------------|---------|---------------|----------------|
| Üzenetsor új üzenet érkezik, amely fut egy függvényt, amely egy másik üzenetsornak írni. | Queue<sup>*</sup> | *Nincsenek* | Queue<sup>*</sup> |
|Ütemezett feladat beolvassa a Blob Storage tartalmát, és létrehoz egy új Cosmos DB-dokumentumban. | Időzítő | Blob Storage | Cosmos DB |
|Olvassa el a képet a Blob Storage és a Cosmos DB-ből egy dokumentumot egy e-mail küldése az Event Grid szolgál. | Event Grid | A BLOB Storage és a Cosmos DB használatával | SendGrid |
| Egy webhookot, amely a Microsoft Graph segítségével frissítése egy Excel-munkalapon. | HTTP | *Nincsenek* | Microsoft Graph |

<sup>\*</sup> Külön üzenetsorokra jelöli

Ezek a példák nem jelentenek a lenni, de bemutatják, hogyan használhatja eseményindítók és kötések együtt vannak megadva.

###  <a name="trigger-and-binding-definitions"></a>Eseményindítók és kötési

Eseményindítók és kötések másképp definiált függően a fejlesztési módszere.

| Platform | Eseményindítók és kötések által konfigurált... |
|-------------|--------------------------------------------|
| C#osztálytár | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;módszerek és a paraméterek dekorálása C# attribútumok |
| Minden más (beleértve az Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;frissítési [function.json](./functions-reference.md) ([séma](http://json.schemastore.org/function)) |

A portálon Ez a konfiguráció egy felhasználói Felületet biztosít, de a fájl megnyitásával közvetlenül szerkesztheti a **speciális szerkesztő** keresztül elérhető a **integráció** a függvény lapon.

A .NET a paraméter típusa határozza meg a bemeneti adatok adattípusát. Használja például a `string` kötést létrehozni egy üzenetsor eseményindító, egy bájttömböt a bináris és a egy egyéni típusú objektumhoz deszerializálni olvasás szövegét.

Dinamikusan típusú például a JavaScript nyelven, használja a `dataType` tulajdonságot a *function.json* fájlt. Olvassa el a tartalom HTTP-kérés bináris formátumú, például állítsa be a `dataType` való `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Egyéb lehetőségek `dataType` vannak `stream` és `string`.

## <a name="binding-direction"></a>Kötés iránya

Az összes eseményindítók és kötések rendelkezik egy `direction` tulajdonság a [function.json](./functions-reference.md) fájlt:

- Az eseményindítók az irány mindig van kapcsolva `in`
- Bemeneti és kimeneti kötések használata `in` és `out`
- Néhány speciális iránya kötéseket `inout`. Ha `inout`, csak a **speciális szerkesztő** keresztül érhető el a **integráció** lapot a portálon.

Ha használ [egy osztálytár attribútumok](functions-dotnet-class-library.md) eseményindítók és kötések konfigurálásához irányát az attribútumok konstruktorában megadott vagy a paraméter típusa fióktól vette a beállításait.

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Információ arról, hogy mely kötések előzetes verzióként érhetők el, vagy éles környezetben való használatra jóváhagyott: [támogatott nyelvek](supported-languages.md).

## <a name="resources"></a>További források
- [Kötelező kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure-függvény visszaadott értékének használata](./functions-bindings-return-value.md)
- [Hogyan kell regisztrálni egy kötés kifejezés](./functions-bindings-register.md)
- Tesztelés:
  - [A kódot tesztelés az Azure Functions stratégiák](functions-test-a-function.md)
  - [Egy nem HTTP által aktivált függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák kezelése](./functions-bindings-errors.md)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure Functions kötési bővítményeket regisztrálása](./functions-bindings-register.md)
