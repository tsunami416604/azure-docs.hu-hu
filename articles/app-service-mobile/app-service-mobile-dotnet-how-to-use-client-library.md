---
title: A felügyelt ügyféltár használata
description: Ismerje meg, hogyan használhatja a .NET ügyfélkönyvtárat az Azure App Service Mobile Apps windowsos és Xamarin-alkalmazásokkal.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249372"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure App Service Mobile Apps for Windows és a Xamarin-alkalmazások felügyelt ügyfélkönyvtárával. Ha még nem tart at Mobile Apps, érdemes először kitölteni az [Azure Mobile Apps rövid útmutató.][1] Ebben az útmutatóban az ügyféloldali felügyelt SDK-ra összpontosítunk. A mobilalkalmazások kiszolgálóoldali SDK-iról a [.NET Server SDK][2] vagy a [Node.js Server SDK][3]dokumentációjában olvashat bővebben.

## <a name="reference-documentation"></a>Referenciadokumentáció
Az ügyfél SDK-jának referenciadokumentációja itt található: [Azure Mobile Apps .NET ügyfélhivatkozás][4].
Az [Azure-Samples GitHub-tárházban][5]több ügyfélmintát is találhat.

## <a name="supported-platforms"></a>Támogatott platformok
A .NET platform a következő platformokat támogatja:

* Xamarin Android kiadások API 19-24 (KitKat keresztül Nugát)
* Xamarin iOS kiadások az iOS 8.0-s és újabb verzióihoz
* Univerzális Windows-platform
* Windows Phone 8.1
* Windows Phone 8.0, kivéve a Silverlight alkalmazásokat

A "kiszolgáló-flow" hitelesítés webnézetet használ a bemutatott felhasználói felülethez.  Ha az eszköz nem tudja bemutatni a WebView felhasználói felületét, akkor más hitelesítési módszerekre van szükség.  Ez az SDK ezért nem alkalmas watch-típusú vagy hasonlóan korlátozott eszközökhöz.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Telepítés és előfeltételek
Feltételezzük, hogy már létrehozta és közzétette a mobilalkalmazás-háttérprojektet, amely legalább egy táblázatot tartalmaz.  A témakörben használt kódban a `TodoItem` tábla neve, és `Id`a `Text`következő `Complete`oszlopai vannak: , , és . Ez a tábla ugyanaz a tábla, amelyet az [Azure Mobile Apps rövid útmutatójának][1]befejezésekor hoz létre.

A Megfelelő beírt ügyféloldali típus a C#-ban a következő osztály:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

A [JsonPropertyAttribute attribútum][6] segítségével definiálhatja a *PropertyName* leképezést az ügyfélmező és a táblamező között.

A mobilalkalmazások háttérrendszerében lévő táblázatok létrehozásáról a [.NET Server SDK vagy][7] a [Node.js Server SDK című témakörben][8]olvashat. Ha a QuickStart használatával hozta létre a mobilalkalmazás-háttérszolgáltatást az Azure Portalon, **használhatja** az Egyszerű táblák beállítást az [Azure Portalon.]

### <a name="how-to-install-the-managed-client-sdk-package"></a>Útmutató: A felügyelt ügyfél SDK-csomag telepítése
A felügyelt ügyfél SDK-csomagjának [nuget-ből][9]történő telepítéséhez használja az alábbi módszerek egyikét:

* **Vizuális stúdió** Kattintson a jobb gombbal a projektre, `Microsoft.Azure.Mobile.Client` kattintson a **NuGet csomagok kezelése parancsra,** keresse meg a csomagot, majd kattintson a Telepítés **parancsra.**
* **Xamarin Stúdió** Kattintson a jobb **Add** > gombbal a projektre, kattintson a **NuGet csomagok hozzáadása parancsra,** keresse meg a csomagot, majd kattintson a `Microsoft.Azure.Mobile.Client` Csomag hozzáadása **parancsra.**

A fő tevékenységfájlban ne felejtse el hozzáadni a következőket a következő **utasítás használatával:**

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Vegye figyelembe, hogy az Android-projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az `Xamarin.Android.Support.CustomTabs` SDK-tól függ az Android platform, így ha a projekt újabb támogatási csomagokat használ, telepítenie kell ezt a csomagot a szükséges verzióval közvetlenül az ütközések elkerülése érdekében.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Útmutató: Hibakeresési szimbólumok kal való kapcsolat a Visual Studióban
A Microsoft.Azure.Mobile névtér szimbólumai a [SymbolSource][10]webhelyen érhetők el.  Olvassa el a [SymbolSource utasításokat][11] a SymbolSource és a Visual Studio integrálásához.

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>A mobilalkalmazások ügyfél létrehozása
A következő kód létrehozza a [MobileServiceClient][12] objektumot, amely a mobilalkalmazás-háttérrendszer eléréséhez használatos.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Az előző kódban `MOBILE_APP_URL` cserélje le a mobilalkalmazás-háttérszolgáltatás URL-címét, amely megtalálható a mobilalkalmazás-háttérkapcsolat paneljén az [Azure Portalon.] A MobileServiceClient objektumnak egy kislemeznek kell lennie.

## <a name="work-with-tables"></a>Táblázatok használata
A következő szakasz bemutatja, hogyan kereshet és kérhet rekordokat, és hogyan módosíthatja a táblában lévő adatokat.  A következő témaköröket a következő témakörök ben tárgyaltuk:

* [Táblázathivatkozás létrehozása](#instantiating)
* [Adatok lekérdezése](#querying)
* [Visszaadott adatok szűrése](#filtering)
* [A visszaadott adatok rendezése](#sorting)
* [Adatok visszaadása oldalakon](#paging)
* [Adott oszlopok kijelölése](#selecting)
* [Rekord kinézete id-nként](#lookingup)
* [Nem gépelt lekérdezések kezelése](#untypedqueries)
* [Adatok beszúrása](#inserting)
* Adatok frissítése
* [Adatok törlése](#deleting)
* [Konfliktusmegoldás és optimista egyidejűség](#optimisticconcurrency)
* [Kötés Windows felhasználói felülethez](#binding)
* [Az oldalméret módosítása](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Útmutató: Táblázathivatkozás létrehozása
A háttértáblában lévő adatokat elérő vagy módosító összes `MobileServiceTable` kód függvényt hív meg az objektumon. A [GetTable] metódus meghívásával kérjen hivatkozást a táblára az alábbiak szerint:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

A visszaadott objektum a gépelt szerializálási modellt használja. A nem gépelt szerializálási modell is támogatott. A következő példa [egy nem gépelt táblára mutató hivatkozást hoz létre:]

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

A nem gépelt lekérdezésekben meg kell adnia az alapul szolgáló OData lekérdezési karakterláncot.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>Útmutató: Adatok lekérdezése a mobilalkalmazásból
Ez a szakasz azt ismerteti, hogy miként adhat ki lekérdezéseket a mobilalkalmazás-háttérrendszerhez, amely a következő funkciókat tartalmazza:

* [Visszaadott adatok szűrése](#filtering)
* [A visszaadott adatok rendezése](#sorting)
* [Adatok visszaadása oldalakon](#paging)
* [Adott oszlopok kijelölése](#selecting)
* [Adatok felkelőjése azonosító szerint](#lookingup)

> [!NOTE]
> A kiszolgáló által vezérelt oldalméret kényszerítve van, hogy az összes sor ne kerüljön vissza.  A lapozás megakadályozza, hogy a nagy adatkészletekre vonatkozó alapértelmezett kérelmek negatívan befolyásolják a szolgáltatást.  50-nél több sor visszaadására használja a és `Skip` `Take` a módszert az oldalak ban lévő adatok visszaküldése című részben leírtak [szerint.](#paging)

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Útmutató: A visszaadott adatok szűrése
A következő kód bemutatja, hogyan `Where` szűrheti az adatokat egy záradék lekérdezésbe való belefoglalásával. Minden olyan elemet `todoTable` `Complete` visszaad, `false`amelynek tulajdonsága megegyezik a értékkel. A [Hol] függvény sorszűrési predikátumot alkalmaz a tábla lekérdezésére.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

A háttérrendszernek küldött kérelem URI-ját üzenetvizsgáló szoftverrel, például böngészőfejlesztői eszközökkel vagy [Hegedűssel]tekintheti meg. Ha megnézi a kérelem URI-ját, figyelje meg, hogy a lekérdezési karakterlánc módosult:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Ezt az OData-kérelmet a kiszolgáló SDK-ja SQL-lekérdezéssé fordítja:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A `Where` metódusnak átadott függvény tetszőleges számú feltételt kaphat.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Ezt a példát a kiszolgáló SDK-ja sql-lekérdezéssé szeretné lefordítani:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Ez a lekérdezés több záradékra is felosztható:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

A két módszer egyenértékű, és szinonimaként is használható.  Az előbbi lehetőség&mdash;több predikátumok összefűzése egy lekérdezésben&mdash;tömörebb és ajánlott.

A `Where` záradék támogatja az OData részhalmazba lefordítandó műveleteket. A műveletek a következők:

* Relációs operátorok (==, !=, <, <=, >, >=),
* Számtani operátorok (+, -, /, *, %),
* Szám pontosság (Math.Floor, Math.Ceiling),
* Karakterláncfüggvények (Hossz, Karakterlánc-rész, Csere, IndexOf, StartsWith, EndsWith),
* Dátumtulajdonságok (év, hónap, nap, óra, perc, másodperc),
* Egy objektum tulajdonságainak elérése, és
* A műveletek bármelyikét egyesítő kifejezések.

Ha azt mérlegeli, hogy a Server SDK mit támogat, vegye figyelembe az [OData v3 dokumentációt.]

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Útmutató: A visszaadott adatok rendezése
A következő kód bemutatja, hogyan rendezheti az adatokat egy OrderBy vagy [OrderByDescending] függvény t a lekérdezésben. [OrderBy] A mező `todoTable` szerint növekvő sorrendben `Text` lévő elemeket adja vissza.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>Útmutató: Adatok visszaadása oldalakon
Alapértelmezés szerint a háttérrendszer csak az első 50 sort adja vissza. A visszaadott sorok számát a [Take] metódus hívásával növelheti. A `Take` [Skip] metódussal együtt a lekérdezés által visszaadott teljes adatkészlet egy adott "lapját" kérheti. A következő lekérdezés végrehajtásakor a tábla első három elemét adja vissza.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A következő módosított lekérdezés kihagyja az első három eredményt, és a következő három eredményt adja vissza. Ez a lekérdezés az adatok második "oldalát" hozza létre, ahol az oldalméret három elemből áll.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Az [IncludeTotalCount] metódus az *összes* visszaadott rekord teljes számát kéri, figyelmen kívül hagyva a megadott lapozási/határzáradékokat:

```csharp
query = query.IncludeTotalCount();
```

Egy valós alkalmazásokban az előző példához hasonló lekérdezéseket használhat egy személyhívó vezérlővel vagy hasonló felhasználói felülettel az oldalak közötti navigáláshoz.

> [!NOTE]
> A mobilalkalmazás-háttérrendszer 50 soros korlátjának felülbírálásához az [EnableQueryAttribute attribútumot] is alkalmaznia kell a nyilvános GET metódusra, és meg kell adnia a lapozási viselkedést. A módszerre alkalmazva a következő a maximálisvisszaadott sorokat 1000-re állítja:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Útmutató: Adott oszlopok kijelölése
Megadhatja, hogy mely tulajdonságokat kell felvenni az eredményekbe, ha [egy Select] záradékot ad a lekérdezéshez. A következő kód például azt mutatja be, hogyan jelölhet ki csak egy mezőt, és hogyan választhat ki és formázhatok több mezőt:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Az eddig leírt funkciók mindegyike adalékanyag, így folyamatosan láncolhatjuk őket. Minden egyes láncolt hívás több lekérdezést érint. Még egy példa:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>Útmutató: Adatok felkéselése azonosító szerint
A [LookupAsync] funkcióval adott azonosítóval rendelkező objektumokat kereshet az adatbázisból.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>Útmutató: Nem gépelt lekérdezések végrehajtása
Ha nem gépelt táblaobjektummal hajt végre lekérdezést, explicit módon meg kell adnia az OData lekérdezési karakterláncot a [ReadAsync]hívásával, ahogy az a következő példában is:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Visszakapja a JSON értékeket, amelyeket ingatlantáskaként használhat. További információ a JToken és Newtonsoft Json.NET, lásd a [Json.NET] oldalon.

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Útmutató: Adatok beszúrása mobilalkalmazás-háttérrendszerbe
Minden ügyféltípusnak tartalmaznia kell egy **Id**nevű tagot , amely alapértelmezés szerint karakterlánc. Ez az **azonosító** a CRUD-műveletek végrehajtásához és az offline szinkronizáláshoz szükséges. A következő kód bemutatja, hogyan szúrhat be új sorokat egy táblázatba a [InsertAsync] módszerrel. A paraméter a .NET objektumként beszúrandó adatokat tartalmazza.

```csharp
await todoTable.InsertAsync(todoItem);
```

Ha a beszúrás `todoItem` során nem szerepel egyedi egyéni azonosítóérték, a kiszolgáló guid azonosítót hoz létre.
A létrehozott azonosítót az objektum keresésével kérheti le, miután a hívás visszatér.

A nem gépelt adatok beszúrásához kihasználhatja a Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Íme egy példa, amely egy e-mail címet használ egyedi karakterlánc-azonosítóként:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Azonosítóértékek használata
A Mobile Apps támogatja a tábla **azonosítóoszlopának** egyedi egyéni karakterláncértékeit. A karakterlánc-érték lehetővé teszi, hogy az alkalmazások egyéni értékeket, például e-mail címeket vagy felhasználóneveket használjanak az azonosítóhoz.  A karakterlánc-azonosítók a következő előnyöket biztosítják:

* Az azonosítók az adatbázishoz való oda-vissza út nélkül jönnek létre.
* A rekordok könnyebben egyesíthetők különböző táblákból vagy adatbázisokból.
* Az azonosítók értékei jobban integrálhatók az alkalmazás logikájával.

Ha egy karakterlánc-azonosító értéke nincs beállítva egy beszúrt rekordon, a mobilalkalmazás-háttérrendszer egyedi értéket hoz létre az azonosítóhoz. A [Guid.NewGuid] metódus segítségével saját azonosítóértékeket hozhat létre az ügyfélen vagy a háttérrendszerben.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Útmutató: Adatok módosítása mobilalkalmazás-háttérrendszerben
A következő kód bemutatja, hogyan lehet az [UpdateAsync] metódussal frissíteni egy meglévő rekordot ugyanazzal az azonosítóval új információkkal. A paraméter a .NET objektumként frissítendő adatokat tartalmazza.

```csharp
await todoTable.UpdateAsync(todoItem);
```

A nem gépelt adatok frissítéséhez a [következőképpen használhatja ki a Json.NET:]

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

A `id` frissítéssorán meg kell adni egy mezőt. A háttérrendszer `id` a mező segítségével azonosítja a frissítandó sort. A `id` mező a `InsertAsync` hívás eredményéből szerezhető be. Egy `ArgumentException` akkor jelenik meg, ha az értéket `id` nem adja meg.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Útmutató: Adatok törlése mobilalkalmazás-háttérrendszerben
A következő kód bemutatja, hogyan lehet a [DeleteAsync] metódussal törölni egy meglévő példányt. A példányt a `id` mező a. `todoItem`

```csharp
await todoTable.DeleteAsync(todoItem);
```

A nem gépelt adatok törléséhez a következőképpen használhatja ki a Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Törlési kérelem esetén meg kell adni egy azonosítót. Más tulajdonságok nem kerülnek átadásra a szolgáltatásnak, vagy figyelmen kívül hagyják a szolgáltatást. A `DeleteAsync` hívás eredménye általában `null`. A beadandó azonosító a `InsertAsync` hívás eredményéből szerezhető be. Az `MobileServiceInvalidOperationException` a akkor történik, amikor a mező `id` megadása nélkül próbál meg törölni egy elemet.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Útmutató: Optimista egyidejűség használata az ütközések megoldásához
Egyszerre két vagy több ügyfél írhat módosításokat ugyanarra az elemre. Ütközésészlelés nélkül az utolsó írás felülírná a korábbi frissítéseket. **Az optimista egyidejűség-vezérlés** feltételezi, hogy minden tranzakció véglegesíthető, ezért nem használ erőforrás-zárolást.  A tranzakció véglegesítése előtt az optimista egyidejűség-ellenőrzés ellenőrzi, hogy más tranzakció nem módosította-e az adatokat. Ha az adatok módosultak, a véglegesítési tranzakció visszalesz állítva.

A Mobile Apps támogatja az optimista egyidejűség-szabályozást azáltal, hogy nyomon követi az egyes elemek változásait a `version` mobilalkalmazás-háttérrendszer minden táblájához megadott rendszertulajdonság-oszlop használatával. Minden alkalommal, amikor egy rekordot `version` frissít, a Mobile Apps új értékre állítja be a bejegyzés tulajdonságát. Minden frissítési kérelem `version` során a kérelemhez tartozó rekord tulajdonságát a rendszer összehasonlítja a kiszolgálón lévő rekord ugyanazon tulajdonságával. Ha a kérelemmel átadott verzió nem egyezik meg `MobileServicePreconditionFailedException<T>` a háttérrendszerrel, akkor az ügyfélkódtár kivételt képez. A kivétel típusa a háttérrendszerből származó rekord, amely a rekord kiszolgálói verzióját tartalmazza. Az alkalmazás ezután ezt az információt annak eldöntésére, `version` hogy végre a frissítési kérelem újra a megfelelő értéket a háttérrendszer a módosítások véglegesítéséhez.

Adjon meg egy oszlopot `version` a rendszertulajdonság táblaosztályában az optimista egyidejűség engedélyezéséhez. Példa:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

A nem gépelt táblákat használó `Version` alkalmazások az `SystemProperties` optimista egyidejűséget teszik lehetővé, ha a jelzőt a táblázatra az alábbiak szerint állítja be.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Az optimista egyidejűség engedélyezése mellett az `MobileServicePreconditionFailedException<T>` [UpdateAsync]hívásakor is meg kell adnia a kivételt a kódban.  Oldja fel az ütközést úgy, hogy a megfelelőt `version` alkalmazza a frissített rekordra, és hívja meg az [UpdateAsync] metódust a feloldott rekorddal. A következő kód bemutatja, hogyan oldható fel egy írási ütközés az észlelés után:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

További információt az [Offline adatok szinkronizálása] az Azure Mobile Apps témakörben talál.

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Útmutató: Mobilalkalmazások adatainak kötése Windows felhasználói felülethez
Ez a szakasz azt mutatja be, hogyan jeleníthető meg a visszaadott adatobjektumok a Windows-alkalmazások felhasználói felületi elemeivel.  A következő példakód a lista forrásához kötődik, és a hiányos elemek lekérdezése. A [MobileServiceCollection] létrehoz egy Mobile Apps-aware kötési gyűjteményt.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

A felügyelt futásidejű vezérlők némelyike támogatja az [ISupportIncrementalLoading nevű felületet.] Ez a felület lehetővé teszi, hogy a vezérlők további adatokat kérjenek a felhasználó görgetésekor. Az univerzális Windows-alkalmazások felületének beépített támogatása a [MobileServiceIncrementalLoadingCollection-en]keresztül történik, amely automatikusan kezeli a vezérlők hívásait. A `MobileServiceIncrementalLoadingCollection` Windows-alkalmazásokban az alábbiak szerint használható:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Ha az új gyűjteményt Windows Phone 8 és "Silverlight" `IMobileServiceTableQuery<T>` `IMobileServiceTable<T>`alkalmazásokon szeretné használni, használja a bővítménymetódusokat a `ToCollection` és a területen. Az adatok betöltéséhez hívja meg a hívást. `LoadMoreItemsAsync()`

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Ha a hívás `ToCollectionAsync` sal létrehozott `ToCollection`gyűjteményt használja, akkor olyan gyűjteményt kap, amely a felhasználói felület vezérlőihez kötődhet.  Ez a gyűjtemény lapozás-tisztában van.  Mivel a gyűjtemény betölti az adatokat a hálózatról, a betöltés néha sikertelen. Az ilyen hibák kezeléséhez felülbírálja `MobileServiceIncrementalLoadingCollection` a metódust a `LoadMoreItemsAsync` `OnException` hívásból eredő kivételek kezeléséhez.

Fontolja meg, hogy a táblában sok mező van-e, de csak néhányat szeretne megjeleníteni a vezérlőelemben. Az előző szakasz " Adott[oszlopok kiválasztása](#selecting)" című útmutatójával kiválaszthatja a felhasználói felületen megjelenítendő oszlopokat.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Az oldalméret módosítása
Az Azure Mobile Apps alapértelmezés szerint kérésenként legfeljebb 50 elemet ad vissza.  A lapozás méretét úgy módosíthatja, hogy növeli az ügyfélés a kiszolgáló maximális oldalméretét.  A kért oldalméret növeléséhez `PullAsync()`adja meg a következőt: `PullOptions`

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Feltételezve, hogy `PageSize` a kiszolgálón belül legalább 100-at tett, a kérelem legfeljebb 100 elemet ad vissza.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Kapcsolat nélküli táblák használata
Az offline táblák egy helyi SQLite tárolót használnak az adatok offline használatra történő tárolására.  Minden táblaművelet a helyi SQLite tárolón, nem pedig a távoli kiszolgálótárolón történik.  Kapcsolat nélküli tábla létrehozásához először készítse elő a projektet:

1. A Visual Studio jobb gombjára kattintva kattintson a megoldásra > **NuGet csomagok kezelése a megoldáshoz...**, majd keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet csomagot a megoldás összes projektjéhez.
2. (Nem kötelező) A Windows-eszközök támogatásához telepítse az alábbi SQLite futásidejű csomagok egyikét:

   * **Windows 8.1 futásidejű:** Telepítse [az SQLite for Windows 8.1 rendszert.][3]
   * **Windows Phone 8.1:** Telepítse [az SQLite-ot A Windows Phone 8.1-re][4].
   * **Univerzális Windows-platform** Telepítse [az SQLite-ot az univerzális Windows rendszerhez.][5]
3. (Nem kötelező). Windows-eszközök esetén kattintson **a Hivatkozások** > **hivatkozás hozzáadása...** gombra, bontsa ki a **Windows** mappa > **bővítmények mappáját,** majd engedélyezze a megfelelő **SQLite for Windows** SDK-t a Visual **C++ 2013 Runtime for Windows SDK-val** együtt.
    Az SQLite SDK nevek némileg eltérnek az egyes Windows platformoktól.

Táblahivatkozás létrehozása előtt a helyi tárolót a következőkre kell készíteni:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Az üzlet inicializálása általában közvetlenül az ügyfél létrehozása után történik.  Az **OfflineDbPath-nak** olyan fájlnévnek kell lennie, amely minden támogatott platformon használható.  Ha az elérési út egy teljesen minősített elérési út (azaz perjellel kezdődik), akkor a lesz használva az elérési út.  Ha az elérési út nem teljesen minősített, a fájl platformspecifikus helyre kerül.

* IOS és Android rendszerű eszközök esetén az alapértelmezett elérési út a "Személyes fájlok" mappa.
* Windows-eszközök esetén az alapértelmezett elérési út az alkalmazásspecifikus "AppData" mappa.

A táblázat hivatkozása a `GetSyncTable<>` következő módszerrel szerezhető be:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Kapcsolat nélküli tábla használatához nem kell hitelesítenie magát.  Csak akkor kell hitelesítenie, ha a háttérszolgáltatással kommunikál.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Kapcsolat nélküli tábla szinkronizálása
A kapcsolat nélküli táblák alapértelmezés szerint nincsenek szinkronizálva a háttérrendszerrel.  A szinkronizálás két részre oszlik.  A módosításokat az új elemek letöltésétől elkülönítve is leteheti.  Íme egy tipikus szinkronizálási módszer:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Ha az első `PullAsync` argumentum null értékű, akkor a növekményes szinkronizálás nem lesz használva.  Minden szinkronizálási művelet beolvassa az összes rekordot.

Az SDK implicit `PushAsync()` műveletet hajt végre a rekordok lehívása előtt.

A konfliktuskezelés `PullAsync()` egy metóduson történik.  A konfliktusokat ugyanúgy kezelheti, mint az online táblázatokat.  Az ütközés akkor `PullAsync()` jön létre, ha a beszúrás, frissítés vagy törlés során nem hívják meg. Ha több ütközés történik, azok egyetlen MobileServicePushFailedException-be vannak csomagolva.  Minden hibát külön-külön kezeljen.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Egyéni API-val dolgozhat
Az egyéni API lehetővé teszi olyan egyéni végpontok definiálását, amelyek olyan kiszolgálói funkciókat tesznek elérhetővé, amelyek nem illeszkednek beszúrási, frissítési, törlési vagy olvasási művelethez. Egyéni API használatával jobban szabályozhatja az üzenetküldést, például a HTTP-üzenetfejlécek olvasását és beállítását, valamint a JSON-tól eltérő üzenettörzs-formátum meghatározását.

Az egyéni API-t úgy hívja meg, hogy meghívja az [egyik InvokeApiAsync metódust] az ügyfélen. A következő kódsor például postakérést küld a háttérben lévő **completeAll** API-nak:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ez az űrlap egy gépelt metódushívás, és meg kell adni a **MarkAllResult** visszatérési típust. Mind a gépelt, mind a nem gépelt metódusok támogatottak.

Az InvokeApiAsync() metódus "/api/" előad az API-nak, amelyet meg szeretne hívni, kivéve, ha az API egy "/"-val kezdődik.
Példa:

* `InvokeApiAsync("completeAll",...)`/api/completeAll hívás a háttérrendszeren
* `InvokeApiAsync("/.auth/me",...)`/.auth/me hívás a háttérrendszeren

Az InvokeApiAsync használatával bármely WebAPI-t meghívhat, beleértve azokat a webAPI-kat is, amelyek nincsenek definiálva az Azure Mobile Apps alkalmazásokkal.  Az InvokeApiAsync() használatakor a rendszer a kéréssel együtt elküldi a megfelelő fejléceket, beleértve a hitelesítési fejléceket is.

## <a name="authenticate-users"></a><a name="authentication"></a>Felhasználók hitelesítése
A Mobile Apps támogatja az alkalmazásfelhasználók hitelesítését és engedélyezését különböző külső identitásszolgáltatók használatával: Facebook, Google, Microsoft-fiók, Twitter és Azure Active Directory használatával. A táblákra vonatkozó engedélyeket beállíthatja úgy, hogy az egyes műveletekhez csak hitelesített felhasználókra korlátozza a hozzáférést. A hitelesített felhasználók identitását is használhatja engedélyezési szabályok kiszolgálóparancsfájlokban való megvalósításához. További információt a [hitelesítés alkalmazásokhoz történő hozzáadását] ismertető oktatóanyagban találhat.

Két hitelesítési folyamat támogatott: *az ügyfél által felügyelt* és a kiszolgáló által *felügyelt* folyamat. A kiszolgáló által felügyelt folyamat biztosítja a legegyszerűbb hitelesítési élményt, mivel a szolgáltató webes hitelesítési felületére támaszkodik. Az ügyfél által felügyelt folyamat lehetővé teszi az eszközspecifikus képességekkel való mélyebb integrációt, mivel szolgáltatóspecifikus eszközspecifikus SDK-kra támaszkodik.

> [!NOTE]
> Azt javasoljuk, hogy az éles alkalmazásokban használjon ügyféláltal felügyelt folyamatot.

A hitelesítés beállításához regisztrálnia kell az alkalmazást egy vagy több identitásszolgáltatónál.  Az identitásszolgáltató ügyfélazonosítót és ügyféltitkos kulcsot hoz létre az alkalmazáshoz.  Ezek az értékek ezután be vannak állítva a háttérrendszerben az Azure App Service hitelesítésének/engedélyezésének engedélyezéséhez.  További információkért kövesse a részletes utasításokat az oktatóanyag [hitelesítés hozzáadása az alkalmazáshoz].

Ebben a szakaszban a következő témaköröket ismerteti:

* [Ügyfél által felügyelt hitelesítés](#clientflow)
* [Kiszolgáló által felügyelt hitelesítés](#serverflow)
* [A hitelesítési jogkivonat gyorsítótárazása](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Ügyfél által felügyelt hitelesítés
Az alkalmazás önállóan kapcsolatba léphet az identitásszolgáltatóval, majd a háttérrendszerrel való bejelentkezés során adja meg a visszaadott jogkivonatot. Ez az ügyfélfolyamat lehetővé teszi, hogy egyetlen bejelentkezési élményt biztosítson a felhasználók számára, vagy további felhasználói adatokat szerezzen be az identitásszolgáltatótól. Az ügyfélfolyamat-hitelesítés tpreferisza a kiszolgálói folyamat használata, mivel az SDK-szolgáltató natívabb felhasználói felületet biztosít, és további testreszabást tesz lehetővé.

Példák a következő ügyfélfolyamat-hitelesítési mintákra szolgálnak:

* [Active Directory hitelesítési tára](#adal)
* [Facebook vagy Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Felhasználók hitelesítése az Active Directory hitelesítési tárral
Az Active Directory hitelesítési könyvtár (ADAL) segítségével kezdeményezheti a felhasználói hitelesítést az ügyféltől az Azure Active Directory-hitelesítés használatával.

1. Konfigurálja a mobilalkalmazás háttérrendszerét AAD-bejelentkezéshez az [App Service konfigurálása] az Active Directory bejelentkezési oktatóanyagához című oktatóanyag követésével. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának választható lépését végezze el.
2. A Visual Studio vagy a Xamarin Studio alkalmazásban `Microsoft.IdentityModel.Clients.ActiveDirectory` nyissa meg a projektet, és adjon hozzá hivatkozást a NuGet csomaghoz. Kereséskor adja meg a megjelenés előtti verziókat is.
3. Adja hozzá a következő kódot az alkalmazáshoz, a használt platformnak megfelelően. Mindegyikben tegye a következő cseréket:

   * Cserélje **le az INSERT-AUTHORITY-HERE-t** annak a bérlőnek a nevére, amelyben kiépítette az alkalmazást. A formátumnak https://login.microsoftonline.com/contoso.onmicrosoft.coma legyen . Ez az érték az Azure Active Directory tartománylapjáról másolható az [Azure Portalon.]
   * Cserélje **le az INSERT-RESOURCE-ID-HERE-t** a mobilalkalmazás-háttérrendszer ügyfélazonosítójára. Az ügyfélazonosítót az Azure Active **Directory beállításai** a **portálon** a Speciális lapon szerezheti be.
   * Cserélje **le az INSERT-CLIENT-ID-HERE-t** a natív ügyfélalkalmazásból másolt ügyfélazonosítóra.
   * Cserélje **le az INSERT-REDIRECT-URI-HERE-t** a webhely */.auth/login/done* végpontjára a HTTPS-séma használatával. Ennek az értéknek *https://contoso.azurewebsites.net/.auth/login/done*a hoz hasonlónak kell lennie.

     Az egyes platformokhoz szükséges kód a következő:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Egyszeri bejelentkezés a Facebook vagy a Google tokenjével
Az ügyfélfolyamatot a Facebook vagy a Google ezen kódrészletében látható módon használhatja.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Kiszolgáló által felügyelt hitelesítés
Miután regisztrálta az identitásszolgáltatót, hívja meg a [LoginAsync metódust] a [MobileServiceClient] metóduson a szolgáltató [MobileServiceAuthenticationProvider] értékével. A következő kód például a Facebook használatával kezdeményez iratot egy kiszolgálói folyamatba való bejelentkezést.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Ha nem a Facebookot használ identitásszolgáltatót, módosítsa a [MobileServiceAuthenticationProvider] értékét a szolgáltató jará.

A kiszolgálói folyamat, az Azure App Service kezeli az OAuth hitelesítési folyamat a kijelölt szolgáltató bejelentkezési lapjának megjelenítésével.  Miután az identitásszolgáltató visszatér, az Azure App Service létrehoz egy App Service-hitelesítési jogkivonatot. A [LoginAsync] metódus egy [MobileServiceUser-t]ad vissza, amely a hitelesített felhasználó [userid-jét] és a [MobileServiceAuthenticationToken]azonosítót is json webes jogkivonatként (JWT) biztosítja. Ez a token gyorsítótárazható, és újra felhasználható, amíg le nem jár. További információ: [Caching the authentication token](#caching).

### <a name="caching-the-authentication-token"></a><a name="caching"></a>A hitelesítési jogkivonat gyorsítótárazása
Bizonyos esetekben a bejelentkezési metódus hívása elkerülhető az első sikeres hitelesítés után a hitelesítési jogkivonat szolgáltatótól való tárolásával.  A Microsoft Store és az UWP-alkalmazások a [PasswordVault] segítségével gyorsítótárazhatják az aktuális hitelesítési jogkivonatot egy sikeres bejelentkezés után, az alábbiak szerint:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

A UserId érték a hitelesítő adatok felhasználóneveként, a jogkivonat pedig jelszóként tárolódik. A későbbi indítások, ellenőrizheti a **PasswordVault** a gyorsítótárazott hitelesítő adatokat. A következő példa gyorsítótárazott hitelesítő adatokat használ, amikor azok megtalálhatók, és egyébként ismét hitelesítést kísérel meg a háttérrendszerrel:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Amikor kijelentkezik egy felhasználóból, a tárolt hitelesítő adatokat is el kell távolítania az alábbiak szerint:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

A Xamarin-alkalmazások a [Xamarin.Auth] API-kat használják a hitelesítő adatok biztonságos **tárolására** egy fiókobjektumban. Ezen API-k használatával például tekintse meg a [AuthStore.cs] kódfájlját a [ContosoMoments fényképmegosztási mintában.](https://github.com/azure-appservice-samples/ContosoMoments)

Ha ügyféláltal felügyelt hitelesítést használ, gyorsítótárazhatja a szolgáltatótól , például a Facebooktól vagy a Twittertől kapott hozzáférési jogkivonatot is. Ez a jogkivonat a háttérrendszerből egy új hitelesítési jogkivonat kéréséhez adható meg, az alábbiak szerint:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Leküldéses értesítések
A következő témakörök a leküldéses értesítéseket ölelik fel:

* [Regisztráció leküldéses értesítésekre](#register-for-push)
* [Microsoft Store-csomag biztonsági azonosítójának beszerzése](#package-sid)
* [Regisztráció platformfüggetlen sablonokkal](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Hogyan: Regisztráció leküldéses értesítésekre
A Mobile Apps-ügyfél lehetővé teszi, hogy regisztráljon a leküldéses értesítések az Azure Notification Hubs. A regisztráció során a platformspecifikus leküldéses értesítési szolgáltatástól (PNS) beszerzett leírót kap. Ezt az értéket a regisztráció létrehozásakor adja meg a címkékkel együtt. A következő kód regisztrálja a Windows-alkalmazást leküldéses értesítésekhez a Windows értesítési szolgáltatással (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Ha a WNS-hez tol, akkor be kell [szereznie egy Microsoft Store csomag SID-jét.](#package-sid)  A Windows-alkalmazásokról, többek között a sablonregisztrációkregisztrálásról a [Leküldéses értesítések hozzáadása az alkalmazáshoz című]témakörben olvashat bővebben.

Címkék kérése az ügyféltől nem támogatott.  A címkekérelmeket a regisztrációból csendben eldobják.
Ha címkékkel szeretné regisztrálni az eszközt, hozzon létre egy egyéni API-t, amely az Értesítési központok API-t használja a regisztráció az Ön nevében történő végrehajtásához.  Hívja meg az egyéni `RegisterNativeAsync()` API-t a metódus helyett.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>Útmutató: Microsoft Store-csomag BIZTONSÁGI azonosítójának beszerzése
A Microsoft Store-alkalmazások leküldéses értesítéseinek engedélyezéséhez csomagSID-re van szükség.  Csomagbiztonsági rendszer fogadásához regisztrálja az alkalmazást a Microsoft Store-ban.

Ennek az értéknek az eléréséhez:

1. A Visual Studio Solution Explorer programban kattintson a jobb gombbal a Microsoft Store alkalmazásprojektjére, és kattintson az **Áruházbeli** > **alkalmazás és az Áruház társítása parancsra...**.
2. A varázslóban kattintson a **Tovább**gombra, jelentkezzen be a Microsoft-fiókjával, írja be az alkalmazás nevét az **Új alkalmazás nevének lefoglalása**mezőbe, majd kattintson a **Foglalás**gombra.
3. Az alkalmazás regisztrációjának sikeres létrehozása után jelölje ki az alkalmazás nevét, kattintson a **Tovább**gombra, majd a **Társítás gombra.**
4. Jelentkezzen be a [Windows fejlesztői központba] a Microsoft-fiókjával. A **Saját alkalmazások**csoportban kattintson a létrehozott alkalmazásregisztrációra.
5. Kattintson **az Alkalmazáskezelő** > **alkalmazásidentitás**elemre, majd görgessen le a **csomag biztonsági azonosítójának megkereséséhez.**

A csomag SID számos felhasználási célja URI-ként kezeli, ebben az esetben *az ms-app:// rendszert* kell használnia. Jegyezze fel a csomag biztonsági azonosítójának azon verzióját, amelyet úgy alakított ki, hogy ezt az értéket előtagként összefűzi.

A Xamarin-alkalmazásokhoz további kód szükséges ahhoz, hogy regisztrálhassanak egy iOS vagy Android platformon futó alkalmazást. További információt a platformtémakörben talál:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Útmutató: Push sablonok regisztrálása platformfüggetlen értesítések küldéséhez
Sablonok regisztrálásához használja `RegisterAsync()` a módszert a sablonokkal, az alábbiak szerint:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

A sablonoknak `JObject` típusoknak kell lenniük, és több sablont is tartalmazhatnak a következő JSON formátumban:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

A **RegisterAsync()** metódus másodlagos csempéket is elfogad:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

A biztonsági regisztráció során minden címkét eltávolítunk. Címkék hozzáadása a telepítésekhez vagy sablonok a telepítésen belül, lásd: [A .NET háttérkiszolgáló SDK az Azure Mobile Apps].

A regisztrált sablonokat használó értesítések küldéséhez tekintse meg az [Értesítési központok API-jait.]

## <a name="miscellaneous-topics"></a><a name="misc"></a>Egyéb témák
### <a name="how-to-handle-errors"></a><a name="errors"></a>Útmutató: Hibák kezelése
Ha hiba történik a háttérrendszerben, az ügyfél `MobileServiceInvalidOperationException`SDK-ja megemeli a .  A következő példa bemutatja, hogyan kezelhető kvittek a háttérrendszer által visszaadott kivételek:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Egy másik példa a hibafeltételek kezelésére a [Mobilalkalmazások fájljai mintában]található. A [LoggingHandler] példa egy naplózási delegált kezelőt biztosít a háttérrendszernek küldött kérelmek naplózásához.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>Útmutató: A kérelemfejlécek testreszabása
Az adott alkalmazásforgatókönyv támogatásához szükség lehet a mobilalkalmazás-háttérrendszerrel való kommunikáció testreszabására. Előfordulhat például, hogy minden kimenő kérelemhez egyéni fejlécet szeretne hozzáadni, vagy akár módosítani is kell a válaszok állapotkódjait. Egyéni [DelegálásKezelőt]is használhat, ahogy az alábbi példában is látható:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Hitelesítés hozzáadása az alkalmazáshoz]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md
[Leküldéses értesítések hozzáadása az alkalmazáshoz]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Az App Service konfigurálása az Active Directory bejelentkezéshez]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable tábla]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[nem gépelt táblára mutató hivatkozást hoz létre]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Rendelési]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByCsökkenő]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[OlvasásI aszinkron]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Vegyünk]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Kiválasztás]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Ugrál]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Userid]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Ahol]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-portál]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid között]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalloading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows fejlesztői központ]: https://dev.windows.com/overview
[Kezelő delegálása]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Értesítési központok API-jai]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobilalkalmazások fájljaiminta]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[Naplózáskezelő]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentáció]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
