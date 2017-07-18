---
title: "Strukturálatlan adatok tárolása az Azure Functions és a Cosmos DB használatával"
description: "Strukturálatlan adatok tárolása az Azure Functions és a Cosmos DB használatával"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure functions, függvények, eseményfeldolgozás, Cosmos DB, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 492c916a493bb8d5c5415fc517506e5c1ccffc56
ms.contentlocale: hu-hu
ms.lasthandoff: 07/10/2017

---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Strukturálatlan adatok tárolása az Azure Functions és a Cosmos DB használatával

Az Azure Cosmos DB kiválóan alkalmas strukturálatlan és JSON-adatok tárolására. A Cosmos DB, az Azure Functions szolgáltatással kombinálva felgyorsítja és megkönnyíti az adattárolást, hiszen lényegesen kevesebb kódot igényel, mint amennyi egy relációs adatbázisban történő adattároláshoz szükséges.

Ez az oktatóanyag végigvezeti egy olyan Azure-függvény Azure Portal használatával történő létrehozásán, amely egy Cosmos DB-dokumentumban tárol strukturálatlan adatokat. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>Függvény létrehozása

Hozzon létre egy új, C# nyelvű általános webhookot `MyTaskList` néven.

1. Bontsa ki a meglévő saját függvények listáját, majd kattintson a + jelre az új függvény létrehozásához
1. Jelölje ki a Select GenericWebHook-CSharp elemet, és rendelje hozzá a `MyTaskList` nevet.

![Új, C# nyelvű általános webhook-függvényalkalmazás hozzáadása](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

Egy Azure-függvényhez egy trigger és tetszőleges számú bemeneti vagy kimeneti kötés tartozhat. Ebben a példában a kimeneti kötés egy HTTP-kérelem típusú trigger és a Cosmos DB-dokumentum lesz.

1. Nyissa meg a függvény *Integrálás* lapját, ahol megtekintheti vagy módosíthatja a függvény triggerét és kötéseit.
1. Kattintson az oldal jobb felső sarkában található *Új kimenet* hivatkozásra.

Megjegyzés: A HTTP-kérelem típusú trigger már konfigurálva van, de a Cosmos DB-dokumentumkötést hozzá kell adnia.

![Új kimeneti Cosmos DB-kötés hozzáadása](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Adja meg a szükséges adatokat a kötés létrehozásához. Határozza meg az értékeket az alábbi táblázat segítségével.

![A Cosmos DB kimeneti kötésének konfigurálása](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Mező | Érték  |
|---|---|
| Dokumentumparaméter neve | A Cosmos DB-objektumra utaló név a kódban |
| Adatbázis neve | Adatbázis neve a dokumentumok mentéséhez |
| Gyűjtemény neve | Név a Cosmos DB-adatbázisok csoportosításához |
| Szeretné, ha automatikusan létrejönne a Cosmos DB-adatbázis és -gyűjtemény? | Igen vagy Nem |
| Cosmos DB-fiókkapcsolat | Cosmos DB-adatbázisra mutató kapcsolati sztring |

Konfigurálnia kell a Cosmos DB-adatbázishoz történő kapcsolódást is.

1. Kattintson a *Cosmos DB document connection (Cosmos DB-dokumentumkapcsolat) címke melletti Új hivatkozásra.
1. Töltse ki a mezőket, és válassza ki a megfelelő beállításokat a Cosmos DB-dokumentum létrehozásához.

![A Cosmos DB-kapcsolat konfigurálása](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Mező | Érték  |
|---|---|
| Azonosító | A Cosmos DB adatbázis egyedi azonosítója  |
| NoSQL API | Cosmos DB vagy MongoDB  |
| Előfizetés | MSDN-előfizetés  |
| Erőforráscsoport  | Hozzon létre új csoportot, vagy válasszon ki egy már meglévőt.  |
| Hely  | WestEurope  |

1. Kattintson az *OK* gombra. Lehet, hogy néhány percet várnia kell, amíg az Azure létrehozza az erőforrásokat.
1. Kattintson a *Mentés* gombra.

## <a name="update-the-function-code"></a>A függvénykód módosítása

Cserélje le a függvény sablonkódját a következőre:

Vegye figyelembe, hogy ez a mintakód csak C#-környezetben érvényes.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

A mintakód beolvassa a HTTP-kérelem lekérdezési sztringjeit, és egy `taskDocument` objektum tagjaként jelöli ki őket. A `taskDocument` objektum automatikusan menti az adatokat a Cosmos DB adatbázisba, és még létre is hozza az adatbázist az első használatkor.

## <a name="test-the-function-and-database"></a>A függvény és az adatbázis tesztelése

1. A függvény lapon kattintson a portál jobb oldalán látható *Teszt* hivatkozásra, majd adja meg a következő HTTP-lekérdezési sztringeket:

| Lekérdezési sztring | Érték |
|---|---|
| név | Kenyeres Péter |
| feladat | Szalonnás szendvics elkészítése |
| határidő | 05/12/2017 |

1. Kattintson a *Futtatás* hivatkozásra.
1. Ellenőrizze, hogy a függvény visszaadta-e a *HTTP 200 OK* válaszkódot.

![A Cosmos DB kimeneti kötésének konfigurálása](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Ellenőrizze, hogy került-e bejegyzés a Cosmos DB adatbázisba.

1. Keresse meg az adatbázist az Azure Portalon, és jelölje ki.
1. Kattintson az *Adatkezelő* elemre.
1. Bontsa ki a csomópontokat addig, amíg el nem éri a dokumentum bejegyzéseit.
1. Ellenőrizze az adatbázis-bejegyzést. Az adatbázisban a saját adatai mellett további metaadatokat is találni fog.

![A Cosmos DB-bejegyzés ellenőrzése](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Ha az adatok megvannak a dokumentumban, akkor sikeresen hozott létre egy olyan Azure-függvényt, amely strukturálatlan adatokat tárol egy Cosmos DB-adatbázisban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

A Cosmos DB-adatbázisokhoz végzett kötésről további információt az [Azure Functions Cosmos DB-kötéseket](functions-bindings-documentdb.md) ismertető cikk tartalmaz.

