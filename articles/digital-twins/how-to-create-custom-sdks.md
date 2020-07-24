---
title: Egyéni SDK-k létrehozása az Azure Digital Twins-hoz az autorest szolgáltatással
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhatja ki az egyéni SDK-kat, hogy az Azure Digital Twins használatát a C#-tól eltérő nyelvekkel használja.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e2a7f09ac6ff3be119a07ed0a2162525801ceef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061852"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Egyéni SDK-k létrehozása az Azure Digital Twins-hoz az autorest használatával

Jelenleg csak az Azure Digital Twins API-kkal való interakcióhoz közzétett adatközpont-SDK van a .NET (C#) rendszerhez. A .NET SDK-val és az API-kkal kapcsolatban általában a [*útmutató: az Azure digitális Twins API-k és SDK*](how-to-use-apis-sdks.md)-k használata című témakörben olvashat. Ha más nyelven dolgozik, ez a cikk bemutatja, hogyan hozhatja ki saját SDK-t az Ön által választott nyelven az autorest használatával.

## <a name="set-up-your-machine"></a>A gép beállítása

Az SDK létrehozásához a következőkre lesz szüksége:
* Az [autorest](https://github.com/Azure/autorest), a Version 2.0.4413 (3-as verzió jelenleg nem támogatott)
* [Node.js](https://nodejs.org) az autorest előfeltétele
* Az [Azure Digital Twins hencegő (OpenAPI) fájl](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) , amely a *digitaltwins.js*, valamint a hozzá tartozó, a példákhoz kapcsolódó mappájában található. Töltse le a hencegő fájlt és a hozzá tartozó mappát a helyi gépre.

Ha a gép a fenti listából mindent megtesz, készen áll az autorest használatára az SDK létrehozásához.

## <a name="create-the-sdk-with-autorest"></a>Az SDK létrehozása az autorest használatával 

Ha Node.js van telepítve, futtathatja ezt a parancsot, hogy ellenőrizze, van-e telepítve az autorest megfelelő verziója:
```cmd/sh
npm install -g autorest@2.0.4413
```

Az Azure digitális Twins hencegő fájlján az autorest futtatásához kövesse az alábbi lépéseket:
1. Másolja az Azure digitális Twins hencegő fájlját és a hozzá tartozó kapcsolódó mappát egy munkakönyvtárba.
2. Egy parancssori ablak használatával váltson át erre a munkakönyvtárra.
3. Futtassa az autorest parancsot a következő paranccsal. Cserélje le a `<language>` helyőrzőt az Ön által választott nyelvre: `--python` ,, `--java` `--go` stb. (A beállítások teljes listáját megtalálja az [Autorest README fájlban](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Ennek eredményeképpen egy új, *ADTApi* nevű mappa jelenik meg a munkakönyvtárban. A generált SDK-fájlok a névtér *ADTApi*lesznek. Ezt a névteret továbbra is használhatja a jelen cikkben található használati példákban.

Az autorest számos nyelvi kód generátort támogat.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Az SDK hozzáadása egy Visual Studio-projekthez

Az autorest által létrehozott fájlokat közvetlenül egy .NET-megoldásba is felveheti. Azonban valószínű, hogy az Azure digitális Twins SDK-t több különálló projektben (az ügyfélalkalmazások, Azure Functions alkalmazások stb.) szeretné felvenni. Emiatt hasznos lehet egy különálló projekt (.NET-osztály könyvtára) létrehozása a generált fájlokból. Ezt követően belefoglalhatja ezt az osztály-függvénytár-projektet több megoldásba projekt-hivatkozásként.

Ebből a szakaszból megtudhatja, hogyan hozhatja létre az SDK-t egy olyan osztály-függvénytárként, amely a saját projektje, és más projektekben is szerepelhet. Ezek a lépések a **Visual studióra** támaszkodnak ( [innen](https://visualstudio.microsoft.com/downloads/)telepítheti a legújabb verziót).

A lépések a következők:

1. Új Visual Studio-megoldás létrehozása az osztály könyvtára számára
2. A *ADTApi* használata projekt neveként
3. A Solutions Explorerben kattintson a jobb gombbal a generált megoldás *ADTApi* projektre, és válassza a *> meglévő elem hozzáadása..* . lehetőséget.
4. Keresse meg azt a mappát, ahová létrehozta az SDK-t, és válassza ki a legfelső szintű fájlokat.
5. Nyomja meg az "OK" gombot
6. Adjon hozzá egy mappát a projekthez (kattintson a jobb gombbal a projektre Megoldáskezelő, majd válassza a *> új mappa hozzáadása*) lehetőséget.
7. A mappa *modelljeinek* neve
8. Kattintson a jobb gombbal a *modellek* mappára a Solutions Explorerben, és válassza a *> meglévő elem hozzáadása..* . lehetőséget.
9. Válassza ki a generált SDK *models (modellek* ) mappájában található fájlokat, és nyomja meg az "OK" gombot.

Az SDK sikeres létrehozásához a projektnek ezekre a hivatkozásokra lesz szüksége:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Ezek hozzáadásához nyissa meg az *eszközök > NuGet Package Manager > NuGet-csomagok kezelése a megoldáshoz..*. lehetőséget.

1. A panelen ellenőrizze, hogy a *Tallózás* lap van-e kiválasztva.
2. Keressen rá a *Microsoft. Rest* kifejezésre
3. Válassza ki a `ClientRuntime` és a `ClientRuntime.Azure` csomagokat, és vegye fel őket a megoldásba

Most már felépítheti a projektet, és felhasználhatja azt projekt-referenciáként bármely olyan Azure digitális Twins-alkalmazásban, amelyet írsz.

## <a name="general-guidelines-for-generated-sdks"></a>A generált SDK-k általános iránymutatásai

Ez a szakasz a generált SDK használatával kapcsolatos általános információkat és útmutatásokat tartalmazza.

### <a name="synchronous-and-asynchronous-calls"></a>Szinkron és aszinkron hívások

Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

### <a name="typed-and-untyped-data"></a>Beírt és nem típusos adattípusok

REST API a hívások általában erősen beírt objektumokat adnak vissza. Mivel azonban az Azure Digital Twins lehetővé teszi, hogy a felhasználók definiálják a saját egyéni típusait az ikrek számára, nincs mód előre definiálni a statikus visszaküldési adatmennyiségeket számos Azure digitális Twins-híváshoz. Ehelyett az API-k határozottan gépelt burkoló típusokat adnak vissza, ahol alkalmazhatóak, és az egyéni típusú Twin-adat a Json.NET objektumokban van (amelyet a rendszer akkor használ, amikor az "Object" adattípus szerepel az API-aláírásokban). Ezeket az objektumokat a kódban megfelelően elvégezheti.

### <a name="error-handling"></a>Hibakezelés

Ha hiba lép fel az SDK-ban (beleértve a HTTP-hibákat (például 404), akkor az SDK kivételt jelez. Ezért fontos, hogy az összes API-hívást beágyazza a try/catch blokkon belül.

Íme egy kódrészlet, amely egy IKeret próbál felvenni, és a folyamat során hibákat észlel:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Lapozás

Az autorest két típusú lapozási mintát hoz létre az SDK számára:
* Egyet az összes API-hoz a lekérdezési API kivételével
* Egy a lekérdezési API-hoz

A nem lekérdezési lapozási mintában az egyes hívások két verziója létezik:
* Egy verzió, amely a kezdeti hívást végzi (például `DigitalTwins.ListEdges()` )
* A következő lapok beolvasására szolgáló verzió. Ezek a hívások a "Next" utótaggal rendelkeznek (például `DigitalTwins.ListEdgesNext()` )

Az alábbi kódrészlet bemutatja, hogyan kérhető le az Azure Digital Twins kimenő kapcsolatainak lapozható listája:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

A második minta csak a lekérdezési API-hoz lett létrehozva. `continuationToken`Explicit módon használja.

Íme egy példa a következő mintával:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>További lépések

Végigvezeti egy ügyfélalkalmazás létrehozásának lépésein, ahol az SDK-t használhatja:
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)