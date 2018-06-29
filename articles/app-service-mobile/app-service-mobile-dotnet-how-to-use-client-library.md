---
title: Az App Service Mobile Apps felügyelt ügyféloldali kódtár használata (Windows |} Microsoft Docs
description: Útmutató a .NET-ügyfél használata az Azure App Service Mobile Apps Windows és a Xamarin-alkalmazásokat.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: f0f28d4b6573e4e6fecf0e6dd84814d4fc66cd60
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050496"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a felügyelt ügyféloldali kódtár használata Azure App Service Mobile Apps for Windows és a Xamarin-alkalmazásokba gyakori forgatókönyvek. Ha most ismerkedik a Mobile Apps, fontolja meg először befejezése a [Azure Mobile Apps gyors üzembe helyezés] [ 1] oktatóanyag. Ez az útmutató azt figyelmet az ügyféloldali felügyelt SDK-val. További információt a kiszolgálóoldali SDK-k a Mobile Apps, dokumentációjában a [.NET SDK-kiszolgáló] [ 2] vagy a [Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Segédanyagok
Az ügyfél SDK dokumentációját a következő helyen található: [Azure Mobile Apps .NET ügyfél hivatkozási][4].
Több ügyfél minták is megkeresheti a [Azure-minták GitHub-tárházban][5].

## <a name="supported-platforms"></a>A támogatott platformok
A .NET platformon a következő platformokat támogatja:

* Xamarin Android API 19 kiadását – 24 (KitKat nugát keresztül)
* Xamarin iOS kiadását iOS 8.0-s és újabb verziók
* Univerzális Windows-platform
* Windows Phone 8.1
* Windows Phone 8.0 kivételével a Silverlight alkalmazások részére

A "server-folyamat" hitelesítési bemutatott felhasználói felülete a webes nézet használja.  Az eszköz nincs jelen webes nézet felhasználói Felületet, ha más hitelesítési módszert van szükség.  Ez az SDK így nem alkalmas figyelési típusú vagy hasonló módon korlátozott eszközök.

## <a name="setup"></a>A telepítő és Előfeltételek
Feltételezzük, hogy már létrehozott és közzétett mobilalkalmazás háttérprojekt, amely legalább egy olyan táblát tartalmaz.  A kód a jelen témakörben használt, a táblázat neve `TodoItem` és van-e a következő oszlopokat: `Id`, `Text`, és `Complete`. Ez a táblázat ugyanahhoz a táblához jön létre, amikor befejezte a [Azure Mobile Apps gyors üzembe helyezés][1].

A megfelelő típusos ügyféloldali C# típus a következő osztály:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

A [JsonPropertyAttribute] [ 6] azonosítására szolgál a *PropertyName* az ügyfél és a tábla mező közötti megfeleltetés.

Táblázatok létrehozása a Mobile Apps-háttéralkalmazásának további tudnivalókért lásd: a [.NET Server SDK témakörben] [ 7] vagy a [Node.js Server SDK témakörben][8]. Ha a gyors üzembe helyezés az Azure portálon létrehozott Mobile Apps-háttéralkalmazását, használhatja a **könnyen táblák** beállítást azokban a [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Útmutató: a kezelt ügyfél SDK telepítéséhez
A felügyelt ügyfél SDK csomag telepítéséhez a Mobile Apps-alkalmazáshoz az alábbi módszerek valamelyikével [NuGet][9]:

* **A Visual Studio** kattintson jobb gombbal a projektre, kattintson a **NuGet-csomagok kezelése**, keresse meg a `Microsoft.Azure.Mobile.Client` csomagot, majd kattintson az **telepítése**.
* **Xamarin Studio** kattintson jobb gombbal a projektre, kattintson a **Hozzáadás** > **NuGet-csomagok hozzáadása**, keresse meg a `Microsoft.Azure.Mobile.Client `csomagot, majd kattintson a **csomag hozzáadása** .

A fő tevékenységnél fájlban, ne felejtse el hozzáadni a következő **használatával** utasítást:

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Útmutató: a Visual Studio hibakeresési szimbólumok használata
A szimbólumokat a Microsoft.Azure.Mobile névtér érhetők el a [SymbolSource][10].  Tekintse meg a [SymbolSource utasításokat] [ 11] SymbolSource a Visual Studio integrálását.

## <a name="create-client"></a>A Mobile Apps-ügyfél létrehozása
A következő kódot hoz létre a [MobileServiceClient] [ 12] objektum, amely használható a Mobile Apps-háttéralkalmazás eléréséhez.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Cserélje le az előzőekben látható kód `MOBILE_APP_URL` a Mobile Apps-háttéralkalmazás URL-címét, amely megtalálható a Mobile Apps-háttéralkalmazását panel a [Azure Portal]. A MobileServiceClient objektum egypéldányos kell lennie.

## <a name="work-with-tables"></a>A táblázatok használata
Az alábbi szakasz részletesen keresése és lehívása és módosíthatja az adatokat a táblán belül.  A következő témaköröket:

* [Egy tábla hivatkozás létrehozása](#instantiating)
* [Adatait kérdezi le.](#querying)
* [Visszaadott adatok szűrése](#filtering)
* [A rendezési adatokat adott vissza](#sorting)
* [A lapok visszatérési adatai](#paging)
* [Egyes oszlopok kiválasztásához](#selecting)
* [Kereshet meg egy olyan rekordot-azonosító szerint](#lookingup)
* [A típus nélküli lekérdezések kezelése](#untypedqueries)
* [Adatok beszúrása](#inserting)
* [Adatok frissítése](#updating)
* [Adatok törlése](#deleting)
* [Ütközések feloldása, és az egyidejű hozzáférések optimista](#optimisticconcurrency)
* [Egy Windows felhasználói felület kötése](#binding)
* [Az oldalméret módosítása](#pagesize)

### <a name="instantiating"></a>Hogyan: tábla hivatkozás létrehozása
A kódot, amely hozzáfér, vagy módosítja a háttérrendszer táblákban tárolt adatokat meghívja a funkciók a `MobileServiceTable` objektum. A tábla mutató hivatkozás beszerzése meghívásával a [GetTable] módszert az alábbiak szerint:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

A visszaadott objektumot a típusos szerializálási modellt használ. Az a típus nélküli szerializálás modell is támogatott. Az alábbi példa [hoz létre egy típusos táblára mutató hivatkozás]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

A típus nélküli lekérdezések meg kell adnia az alapul szolgáló OData-lekérdezési karakterlánc.

### <a name="querying"></a>Útmutató: a mobilalkalmazás adatok lekérdezése
Ez a szakasz ismerteti, hogyan lekérdezések kiadni a Mobile Apps-háttéralkalmazás, amely a következő funkciókat tartalmazza:

* [Visszaadott adatok szűrése](#filtering)
* [A rendezési adatokat adott vissza](#sorting)
* [A lapok visszatérési adatai](#paging)
* [Egyes oszlopok kiválasztásához](#selecting)
* [Adatok-azonosító szerint](#lookingup)

> [!NOTE]
> Egy kiszolgáló adatvezérelt oldalméret ad vissza az összes sort megakadályozása van kényszerítve.  Lapozófájl alapértelmezett kérelmek nagy adatkészletek tarthatja negatív hatással a szolgáltatás.  Több mint 50 sorok visszaállításához használja a `Skip` és `Take` metódus, a [vissza adatokat a lapok](#paging).

### <a name="filtering"></a>Hogyan: szűrő adatokat adott vissza.
Az alábbi kód bemutatja az adatok szűrése, beleértve a következőket a `Where` alzáradékában. Az összes elemet adja vissza `todoTable` amelynek `Complete` tulajdonság értéke `false`. A [Ha] függvény egy sort a lekérdezéshez a táblázaton predikátum szűrés vonatkozik.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Megtekintheti a küldött üzenet hálózatfelügyeleti szoftverek, például a böngésző fejlesztői eszközök segítségével a háttér-kérelem URI vagy [Fiddler]. Ha a kérelem URI-azonosítója tekinti meg, figyelje meg, hogy van-e módosítva a lekérdezési karakterlánc:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Az OData-kérelem a kiszolgáló SDK lefordítását egy SQL-lekérdezést:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A függvénynek átadott a `Where` metódus egy tetszőleges számú feltételek lehet.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Ebben a példában volna fordítható egy SQL-lekérdezést, a kiszolgáló SDK-ban:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Ez a lekérdezés több záradékot is osztható:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

A két módszer egyenértékű, és szabadon felcserélhetők.  A korábbi beállítás&mdash;hozzáfűzésével több predikátumok egy lekérdezést a&mdash;tömörebb és ajánlott.

A `Where` záradék támogatja a műveleteket, lehet, az OData-részhalmazt lefordítva. Műveletek a következők:

* Relációs operátorok (==,! =, <, < =, >, > =),
* Aritmetikai operátor (+, -, /, *, %),
* A pontosság (Math.Floor, Math.Ceiling), szám
* Karakterlánc (hossza, Substring, Replace, IndexOf, megadott módon kezdődő, megadott módon végződő),
* Tulajdonságok (év, hónap, nap, óra, perc másodperc), dátum
* Az objektum tulajdonságainak hozzáférést és
* A kifejezések kombinálásával egyik műveletet.

Annak eldöntéséhez, hogy a kiszolgáló SDK támogatja, akkor is fontolóra veheti a [OData v3 dokumentáció].

### <a name="sorting"></a>Hogyan: rendezési adatokat adott vissza.
Az alábbi kód bemutatja, hogyan lehet rendezni az adatok-ot egy [OrderBy] vagy [OrderByDescending] a lekérdezésben. Adja vissza, a cikkek `todoTable` növekvő sorrend által a `Text` mező.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Hogyan: vissza adatokat lap
Alapértelmezés szerint a háttér csak az első 50 sort adja vissza. Visszaadott sorok száma meghívásával növelheti a [érvénybe] metódust. Használjon `Take` együtt a [Skip] módszer egy adott "lap" a teljes adatkészlet a lekérdezés által visszaadott kéréséhez. A következő lekérdezés végrehajtásakor, a három legfontosabb cikkeket a táblát adja vissza.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Az alábbi javított lekérdezés kihagyja az első három eredményeket, és a következő három eredményeket ad vissza. Ez a lekérdezés második "lapján" adatok, ahol az oldalméret három elemet hoz létre.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A [IncludeTotalCount] metódus kérelmek teljes számát *összes* az azt jelzi, hogy akkor adott vissza, figyelmen kívül hagyja a lapozófájl/vonatkozó záradékban megadott:

```
query = query.IncludeTotalCount();
```

Egy valós alkalmazás használatával az előző példához hasonló lekérdezéseket a személyhívó vezérlőre vagy hasonló felhasználói felület oldalai között.

> [!NOTE]
> A Mobile Apps-háttéralkalmazás-50-sor korlát felülbírálásához is telepítenie kell a [EnableQueryAttribute] nyilvános GET metódus, és adja meg a lapozófájl viselkedését. A metódus alkalmazásakor a következő állítja a maximálisan engedélyezett 1000 sort adott vissza:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Hogyan: egyes oszlopok kiválasztásához
Megadhatja, amely tulajdonságok beállítása hozzáadásával az eredmények felvenni egy [Kiválasztás] záradékot a lekérdezéshez. Például a következő kód bemutatja, csak egy mező kiválasztása, és válassza ki, és több mező formázása:

```
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

Amennyiben leírt minden funkció additívak, így azt is tartsa láncolás őket. Minden láncolt hívás több lekérdezés van hatással. Egy további példa:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Útmutató: adatok-azonosító szerint
A [LookupAsync] függvény használható objektumokat kereshet meg egy adott. az adatbázisból

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Útmutató: a típus nélküli lekérdezések végrehajtása
A típus nélküli tábla objektum lekérdezést végrehajtásakor pontosan meg kell adni az OData-lekérdezési karakterlánc meghívásával [ReadAsync], az alábbi példa:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

JSON értékekre, amelyek például a tulajdonságcsomag vissza. JToken és Newtonsoft Json.NET további információkért lásd: a [Json.NET] hely.

### <a name="inserting"></a>Útmutató: a Mobile Apps-háttéralkalmazás adatok beszúrása
Minden ügyfél esetében tartalmaznia kell egy nevű tag **azonosító**, amely alapértelmezés szerint ki egy karakterláncot. Ez **azonosító** CRUD műveletek elvégzéséhez szükséges és a kapcsolat nélküli szinkronizálás. Az alábbi kód bemutatja, hogyan használható a [InsertAsync] módszer új sorok beillesztéséhez egy tábla. A paraméter tartalmazza az adatokat beszúrni .NET objektumként.

```
await todoTable.InsertAsync(todoItem);
```

Ha egyéni azonosító egyedi érték nem szerepel a `todoItem` során egy INSERT utasítás, a kiszolgáló által létrehozott GUID.
A generált kódot adott vissza a hívása után az objektum vizsgálatával kérheti le.

Típusos adatok beszúrásához eltarthat Json.NET előnye:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Íme egy példa egy e-mail cím használata egy egyedi karakterlánc-azonosító:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Azonosító értékek használata
Mobile Apps támogatja egyedi egyéni karakterlánc-értékek a táblázat **azonosító** oszlop. Egy karakterláncértéket lehetővé teszi, hogy az alkalmazások az egyéni értékek, például az e-mail címek vagy felhasználónevek a azonosítóját.  Karakterlánc-azonosítóknak a következő előnyöket biztosítja:

* Azonosítók akkor jönnek létre, de oda-vissza az adatbázist.
* Rekordok olyan könnyebben különböző táblákhoz vagy adatbázisok egyesíteni.
* Azonosítók értékek jobban integrálható egy alkalmazás logikáját.

Ha a karakterlánc-azonosító értéke nincs beállítva egy beszúrt rekordot, a Mobile Apps-háttéralkalmazás állít elő, egyedi értéket a azonosítóját. Használhatja a [Guid.NewGuid] metódus saját azonosító értékeket, az ügyfélen vagy a háttérben létrehozásához.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Útmutató: a Mobile Apps-háttéralkalmazás adatainak módosítása
Az alábbi kód bemutatja, hogyan használható a [UpdateAsync] metódust ugyanazzal az azonosítóval rendelkező új információ egy meglévő rekordjának frissítése érdekében. A paraméter tartalmazza az adatokat, frissítenie kell a .NET objektumként.

```
await todoTable.UpdateAsync(todoItem);
```

Típusos adatok frissítéséhez is igénybe vehet előnyeit [Json.NET] az alábbiak szerint:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Egy `id` mezőt meg kell adni, ha egy frissítés. A háttéralkalmazás használja a `id` mező frissítése sor azonosítására. A `id` mező eredménye lehet lekérni a `InsertAsync` hívható meg. Egy `ArgumentException` jelenik meg, ha egy elem frissítése megadása nélkül próbál a `id` érték.

### <a name="deleting"></a>Útmutató: a Mobile Apps-háttéralkalmazás adatok törlése
Az alábbi kód bemutatja, hogyan használható a [DeleteAsync] metódus törlése egy meglévő példányát. A példány azonosíthatók a `id` set mezője az `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Típusos adatok törlése, akkor előfordulhat, hogy előnyeit Json.NET az alábbiak szerint:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Ha elvégezte a törlési kérelmet, meg kell adni egy Azonosítót. Egyéb tulajdonságok nem továbbítódnak a szolgáltatás, vagy figyelmen kívül lesznek hagyva, a szolgáltatás. Eredménye egy `DeleteAsync` tekintendő, amely általában `null`. Adjon át az Azonosítót az eredménye lehet lekérni a `InsertAsync` hívható meg. A `MobileServiceInvalidOperationException` fordul elő, amikor egy elem törlése megadása nélkül próbál a `id` mező.

### <a name="optimisticconcurrency"></a>Hogyan: használata egyidejű hozzáférések optimista az ütközések feloldása
Két vagy több ügyfelet az egy időben ugyanazt a cikket is írni módosítások. Nélkül ütközésészlelés az utolsó írás felülírná korábbi frissítéseket. **Egyidejű hozzáférések optimista vezérlését** feltételezi, hogy az egyes tranzakciókra véglegesítheti, és ezért nem használhatja semmilyen erőforrás zárolását.  Előtt véglegesítése tranzakció, egyidejű hozzáférések optimista vezérlését ellenőrzi, hogy nincs másik tranzakció módosította-e az adatokat. Ha az adatok módosítva lett, a végrehajtása tranzakció vissza lesz állítva.

Mobile Apps egyidejű hozzáférések optimista vezérlését támogatja a változások követése minden elem használatát a `version` rendszer tulajdonság oszlop, amely minden táblában a Mobile Apps-háttéralkalmazás van definiálva. Minden alkalommal, amikor frissíti, a Mobile Apps beállítja a `version` tulajdonság értéke rekord. Minden egyes frissítés kérelem során a `version` tulajdonság a rekord tartalmazza a kéréshez a rendszer összehasonlítja a rekord a kiszolgáló ugyanahhoz a tulajdonsághoz. Ha átadni a verziót a kérelem nem egyezik meg a háttérrendszer, majd az ügyféloldali kódtár kivált egy `MobileServicePreconditionFailedException<T>` kivétel. A kivétel mellékelt típus a rekord a rekord kiszolgálók verzióját tartalmazó háttérrendszerből. Az alkalmazás ezután használhatja ezt az információt határozza meg, hogy a frissítési kérelem újra a megfelelő `version` a háttérkiszolgálón a változtatások véglegesítése a határidő közötti értéket.

Adja meg a tábla osztály az oszlopot a `version` rendszer tulajdonság egyidejű hozzáférések optimista engedélyezéséhez. Példa:

```
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

A típus nélküli táblák használata kiszolgálóalkalmazások lehetővé teszik az egyidejű hozzáférések optimista úgy, hogy a `Version` a jelzőt a `SystemProperties` a tábla az alábbiak szerint.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Egyidejű hozzáférések optimista engedélyezniük, meg kell is dolgozza fel a `MobileServicePreconditionFailedException<T>` a kódban meghívásakor kivétel [UpdateAsync].  Oldja fel az ütközést úgy, hogy alkalmazza a megfelelő `version` a frissített rekord és a hívás [UpdateAsync] a feloldott bejegyzéshez. A következő kód bemutatja, hogyan feloldani egy írás ütközés többször észlelte:

```
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

További információkért lásd: a [Offline adatszinkronizálás az Azure Mobile Appsban] témakör.

### <a name="binding"></a>Útmutató: egy Windows felhasználói felületét Bind Mobile Apps-adatok
Ez a szakasz bemutatja, hogyan visszaadott adatok objektumok felhasználói felületi elemei használják a Windows-alkalmazások megjelenítéséhez.  Az alábbi példakód az a lista forrása a hiányos elemeket a lekérdezéssel van kötve. A [MobileServiceCollection] létrehoz egy Mobile Apps-kompatibilis kötés gyűjteményt.

```
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

A felügyelt futásidejű vezérlőelemeket támogat egy felületet nevű [ISupportIncrementalLoading]. Ez az interfész lehetővé teszi, hogy a vezérlők további adatokat kér, amikor a felhasználó görget. Ez az interfész keresztül univerzális Windows-alkalmazások beépített támogatása [MobileServiceIncrementalLoadingCollection], amely automatikusan kezeli a vezérlők hívást. Használjon `MobileServiceIncrementalLoadingCollection` a Windows-alkalmazások az alábbiak szerint:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Az új gyűjtemény használatához a Windows Phone 8 és a "Silverlight" alkalmazásokban a `ToCollection` a kiterjesztésmetódusok `IMobileServiceTableQuery<T>` és `IMobileServiceTable<T>`. Adatok betöltése, hívja meg a `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

A hívó által létrehozott gyűjtemény használatakor `ToCollectionAsync` vagy `ToCollection`, kap egy gyűjteményt, amely a felhasználói felületi vezérlők köthető.  Ez a gyűjtemény a lapozófájl-kompatibilis.  A gyűjtemény adatokat tölt be a hálózaton, mivel néha betöltése sikertelen. Ilyen hibák kezeléséhez, bírálja felül a `OnException` metódusa `MobileServiceIncrementalLoadingCollection` hívások eredő kivételek kezelésének `LoadMoreItemsAsync`.

Vegye figyelembe, ha a tábla sok mezőt tartalmaz, de a vezérlőben megjelenítendő némelyike csak szeretne. Segítségével az útmutatót az előző szakaszban leírt "[egyes oszlopok kiválasztásához](#selecting)" a felhasználói felületen megjelenő egyes oszlopok kiválasztásához.

### <a name="pagesize"></a>Az oldalméret módosítása
Az Azure Mobile Apps egy legfeljebb 50 elemet kérelmenként alapértelmezés szerint adja vissza.  Az ügyfél és a kiszolgáló a maximális méretének növelésével módosíthatja a lapozófájl mérete.  A kért méretének növelése érdekében adja meg `PullOptions` használatakor `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Feltéve, hogy végzett a `PageSize` egyenlő vagy nagyobb, mint 100 belül a kiszolgáló, a kérelem legfeljebb 100 elemek adja vissza.

## <a name="#offlinesync"></a>A kapcsolat nélküli táblázatok használata
Kapcsolat nélküli táblák egy helyi SQLite tároló a tároló adatokat használ, amikor a kapcsolat nélküli használja.  A helyi elleni végzett műveletek minden tábla SQLite helyett a távoli kiszolgáló tárolójában tárolja.  Egy kapcsolat nélküli tábla létrehozásához először készítse elő a projekthez:

1. A Visual Studióban, kattintson a jobb gombbal a megoldás > **NuGet-csomagok kezelése megoldáshoz...** , majd keresse meg, és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot az összes projektet a megoldásban.
2. (Választható) Windows-eszközök támogatása érdekében telepítse a következő SQLite futtatókörnyezetek egyike:

   * **Windows 8.1 futásidejű:** telepítése [a Windows 8.1 SQLite][3].
   * **Windows Phone 8.1:** telepítése [a Windows Phone 8.1 SQLite][4].
   * **Az univerzális Windows Platform** telepítése [az univerzális Windows SQLite][5].
3. (Nem kötelező). Windows-eszközökhöz, kattintson a **hivatkozások** > **hivatkozás hozzáadása...** , bontsa ki a **Windows** mappa > **bővítmények**, engedélyeznie kell a megfelelő **SQLite for Windows** SDK, valamint a **Windows visual C++ 2013 futásidejű** SDK.
    Az SQLite SDK nevek függően eltérőek az egyes Windows-platformra.

Egy tábla hivatkozás létrehozása előtt elő kell készíteni a helyi tárolójába:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Tanúsítványtár inicializálásához általában akkor történik, az ügyfél létrehozása után azonnal.  A **OfflineDbPath** használja az Ön által támogatott összes platformon megfelelő legyen a fájl nevét.  Ha az elérési út egy teljes mértékben minősített elérési út (Ez azt jelenti, hogy kezdődik perjellel), majd, hogy az elérési utat használja.  Ha az elérési út nem teljesen minősített, a fájl egy platformspecifikus helyre kerül.

* Az iOS és Android-eszközök esetében az alapértelmezett elérési út a "Személyes fájlok" mappa.
* Windows-eszközök esetén az alapértelmezett útvonal: az alkalmazás-specifikus "AppData" mappa.

Egy táblahivatkozás használatával lehet megszerezni a `GetSyncTable<>` módszert:

```
var table = client.GetSyncTable<TodoItem>();
```

Nem kell, hogy egy kapcsolat nélküli táblázat hitelesítést.  Csak kell hitelesíteni, ha a háttérszolgáltatáshoz kommunikációra.

### <a name="syncoffline"></a>Egy kapcsolat nélküli tábla szinkronizálása
Kapcsolat nélküli táblák nincsenek szinkronizálva a háttéralkalmazással való alapértelmezés szerint.  Szinkronizálás két részre van osztva.  Töltsön le új elemek külön-külön tolható módosításokat.  Íme egy jellegzetes szinkronizálási módszere:

```
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

Ha az első argumentumának `PullAsync` null értékű, akkor nem használja a növekményes szinkronizálás.  Minden egyes szinkronizálási műveletet lekéri az összes rekordot.

Az SDK-t hajt végre egy implicit `PushAsync()` előtt az rekord.

Kezelési ütközésben történik, az egy `PullAsync()` metódust.  Ugyanúgy online táblák ütközés is foglalkozik.  Az ütközés hozzák amikor `PullAsync()` ahelyett, hogy közben az insert, update vagy delete nevezik. Több ütközés fordulhat elő, ha azok egyetlen MobileServicePushFailedException be vannak becsomagolva.  Minden egyes hiba külön kezelni.

## <a name="#customapi"></a>Egy egyéni API használata
Egy egyéni API lehetővé teszi, hogy adhatók meg egyéni végpontokat teszi közzé a kiszolgálói funkciót, amely nem egy INSERT utasítás van leképezve, frissítése, törlése, vagy olvasási művelete. Egy egyéni API használatával is befolyásolni további üzenetküldés, beleértve az olvasási és HTTP-üzenet fejlécek beállítása meghatározó JSON nem üzenet törzsének formátumban.

Egy egyéni API hívása egyik a [InvokeApiAsync] módszerek az ügyfélen. Például a következő kódsort egy POST kérést küld a **completeAll** a háttérkiszolgálón API:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Az űrlap típusos metódusát, és megköveteli, hogy a **MarkAllResult** visszatérési, van definiálva típus. Típusos és a nem típusos metódusok támogatottak.

A InvokeApiAsync() metódus lefoglalja a API hívása, kivéve, ha a API-val kezdődik-e használni kívánt "/ api /" a "/".
Példa:

* `InvokeApiAsync("completeAll",...)` meghívja a /api/completeAll a háttér
* `InvokeApiAsync("/.auth/me",...)` a háttérkiszolgálón hívások /.auth/me

InvokeApiAsync segítségével bármely WebAPI, beleértve a nem meghatározott e WebAPIs az Azure Mobile Apps hívja.  InvokeApiAsync() használata esetén a megfelelő fejlécek, beleértve a hitelesítési fejléceket, a kérelem küldése.

## <a name="authentication"></a>Felhasználók hitelesítéséhez
Mobile Apps hitelesítése és engedélyezése a felhasználók alkalmazás különböző külső Identitásszolgáltatók támogatja: Facebook, a Google, a Microsoft Account, a Twitter és az Azure Active Directory. A engedélyeket korlátozhatja a hozzáférést a megadott művelet csak a hitelesített felhasználók táblákon. Hitelesített felhasználók identitásának használhatja, ha az engedélyezési szabályok megvalósítását a kiszolgáló parancsfájlokat. További információt a [Hitelesítés hozzáadása az alkalmazáshoz] ismertető oktatóanyagban találhat.

Két hitelesítési forgalom támogatottak: *ügyfél által felügyelt* és *kiszolgáló által kezelt* folyamata. A kiszolgáló által felügyelt folyamat nyújt a legegyszerűbb felhasználói hitelesítés, a szolgáltató webes hitelesítés felület támaszkodnak. Az ügyfél által felügyelt folyamat lehetővé teszi a eszközspecifikus képességekkel szorosabb integrációt, a szolgáltatói eszközspecifikus SDK-k támaszkodnak.

> [!NOTE]
> Az éles alkalmazásokban lévő ügyfél által felügyelt folyamat használatát javasoljuk.

Hitelesítés beállítása az alkalmazás regisztrálnia kell egy vagy több identitás-szolgáltatóktól.  Az identitásszolgáltató egy ügyfél-Azonosítót és az alkalmazás egy ügyfélkulcsot hoz létre.  Ezeket az értékeket az a kiszolgáló akkor értéke a engedélyezése az Azure App Service hitelesítés/engedélyezés.  További információkért kövesse a részletes utasításokat az oktatóanyag [Hitelesítés hozzáadása az alkalmazáshoz].

Ez a szakasz a következő témákat tárgyalja:

* [Ügyfél által felügyelt hitelesítés](#clientflow)
* [A hitelesítési kiszolgáló által felügyelt](#serverflow)
* [A hitelesítési jogkivonat gyorsítótárazása](#caching)

### <a name="clientflow"></a>Ügyfél által felügyelt hitelesítés
Az alkalmazás egymástól függetlenül lépjen kapcsolatba az identitásszolgáltató, és adja meg a visszaküldött jogkivonat bejelentkezés során a fájlok. Az ügyféltanúsítvány-folyamat lehetővé teszi, hogy egy egyszeri bejelentkezéses élményt nyújtanak a felhasználóknak, vagy további felhasználói adatok beolvasása az identitásszolgáltató által. Folyamat ügyfélhitelesítés használata ajánlott a használatával egy kiszolgáló folyamata, mint az identitásszolgáltató SDK több natív UX abba biztosít, és lehetővé teszi, hogy további testreszabási.

Példák a következő ügyfél-folyamat hitelesítési minták áll rendelkezésre:

* [Az Active Directory hitelesítési könyvtár](#adal)
* [Facebook-on vagy a Google](#client-facebook)
* [Élő SDK](#client-livesdk)

#### <a name="adal"></a>Az Active Directory Authentication Library a felhasználók hitelesítéséhez
Az Active Directory Authentication Library (ADAL) segítségével kezdeményezhet felhasználói hitelesítést, az Azure Active Directory-hitelesítéssel ügyfél.

1. A mobil-háttéralkalmazás az aad-ben bejelentkezés konfigurálása a következő a [App Service konfigurálása az Active Directory bejelentkezéshez] oktatóanyag. Ügyeljen arra, hogy a natív ügyfélalkalmazás regisztrációján választható lépés elvégzése után.
2. A Visual Studio és Xamarin Studio, nyissa meg a projektet, és adjon hozzá egy hivatkozást, a `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet-csomagot. Ha keres, vegye fel az előzetes verzióját.
3. Adja hozzá a következő kódot az alkalmazásba, használja a platformtól függően. Az egyes ellenőrizze az alábbi új:

   * Cserélje le **INSERT-SZOLGÁLTATÓ-Itt** nevű, a bérlő, amelyben az alkalmazás üzembe. A következő formátumban kell megadni https://login.microsoftonline.com/contoso.onmicrosoft.com. Ez az érték lehet másolni az Azure Active Directory tartományi lapján a [Azure Portal].
   * Cserélje le **INSERT-erőforrás-azonosító-Itt** az ügyfél-azonosító a mobil-háttéralkalmazás számára. Ezt úgy szerezheti be az ügyfél-azonosító a **speciális** lap **Azure Active Directory beállításai** a portálon.
   * Cserélje le **INSERT-ügyfél-azonosító-Itt** , az ügyfél-Azonosítót a natív ügyfélalkalmazás másolta.
   * Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel való */.auth/login/done* végpont, a HTTPS protokollt használ. Ez az érték a következőképpen kell kinéznie *https://contoso.azurewebsites.net/.auth/login/done*.

     Az egyes platformokon szükséges kódot a következőképpen:

     **Windows:**

    ```
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

    ```
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

    ```
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

#### <a name="client-facebook"></a>Egyszeri bejelentkezés Facebook-on vagy a Google származó jogkivonat használatával
Az ügyféltanúsítvány-folyamat is használhat, ahogy az a kódrészletet a Facebook-on vagy a Google.

```
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

#### <a name="client-livesdk"></a>Egyszeri bejelentkezés Microsoft Account használatával az élő SDK-val
Hitelesíti a felhasználókat, regisztrálnia kell az alkalmazást a Microsoft-fiók fejlesztői központban. A regisztrációs adatokat konfigurálja a mobilalkalmazás háttérrendszerének. Hozzon létre egy Microsoft-fiók regisztrálása, és csatlakoztassa a mobil-háttéralkalmazást, hajtsa végre a lépéseket a [Regisztrálja az alkalmazást egy Microsoft-fiók bejelentkezési használata]. Ha az alkalmazás Microsoft Store és a Windows Phone 8/Silverlight verzióit, először Regisztráljon a Microsoft Store verziót.

Az alábbi kód Live SDK használatával, és jelentkezzen be a mobilalkalmazás háttérrendszerének a visszaküldött jogkivonat alapján.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Microsoft Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

További információkért lásd: a [A Windows Live SDK] dokumentációját.

### <a name="serverflow"></a>A hitelesítési kiszolgáló által felügyelt
Miután regisztrálta az identitásszolgáltató, hívja az [LoginAsync] [MobileServiceClient] rendelkező metódust a [MobileServiceAuthenticationProvider] érték a szolgáltató. Az alábbi kód például a kiszolgáló folyamata bejelentkezés kezdeményezi, Facebook-fiókkal.

```
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

Ha eltérő Facebook identitásszolgáltató használ, módosítsa [MobileServiceAuthenticationProvider] a szolgáltató értékre.

Egy kiszolgáló folyamatában Azure App Service szolgáltatás az OAuth hitelesítési folyamatot megjelenítése a bejelentkezési lapon a kiválasztott szolgáltató.  Miután az identity provider értéket ad eredményül, Azure App Service létrehoz egy App Service hitelesítés jogkivonatot. A [LoginAsync] metódus értéket ad vissza egy [MobileServiceUser], amely biztosítja, hogy mind a [Felhasználói azonosítóját] a hitelesített felhasználó és a [MobileServiceAuthenticationToken], mint egy JSON webes jogkivonatot (JWT). Ez a token gyorsítótárazható, és újra felhasználható, amíg le nem jár. További információkért lásd: [a hitelesítési jogkivonat gyorsítótárazás](#caching).

### <a name="caching"></a>A hitelesítési jogkivonat gyorsítótárazása
Néhány esetben a bejelentkezési metódus hívása elkerülhető az első sikeres hitelesítést követően a hitelesítési jogkivonat-szolgáltatójáról való elhelyezésével.  Microsoft Store és UWP-alkalmazások használhatják [PasswordVault] a következőképpen gyorsítótárában, miután egy sikeres bejelentkezés, a jelenlegi hitelesítési jogkivonat:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

A UserId értéket a UserName hitelesítő adat tárolja, és a lexikális elem a tárolt jelszóként. A következő kezdő vállalkozások számára, ellenőrizheti a **PasswordVault** a gyorsítótárazott hitelesítő adatokat. Az alábbi példában gyorsítótárazott hitelesítő adatokat, ha találhatók, valamint egyéb próbál meg újra a háttéralkalmazással való:

```
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

Amikor a felhasználó kijelentkezik, el kell távolítani a tárolt hitelesítő adatok, az alábbiak szerint:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin alkalmazások használatát a [Xamarin.Auth] API-k biztonságosan tárolni a felhasználó hitelesítő adatait egy **fiók** objektum. Ezen API-k használatának példája, tekintse meg a [AuthStore.cs] a forráskód fájlja a [minta megosztása ContosoMoments fénykép](https://github.com/azure-appservice-samples/ContosoMoments).

Ügyfél által felügyelt hitelesítés használatakor a Facebook- vagy Twitter például szolgáltatótól kapott hozzáférési jogkivonat is képes gyorsítótárazni. Ez a token is kell adni egy új hitelesítési jogkivonatot kérhet a háttér, az alábbiak szerint:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Leküldéses értesítések
A következő témakörök a leküldéses értesítések terjed ki:

* [A leküldéses értesítések regisztrálása](#register-for-push)
* [Szerezzen be egy Microsoft Store csomag biztonsági azonosítója](#package-sid)
* [Platformok közötti sablonok regisztrálása](#register-xplat)

### <a name="register-for-push"></a>Útmutató: a leküldéses értesítések regisztrálása
A Mobile Apps-ügyfél lehetővé teszi az Azure Notification Hubs leküldéses értesítések regisztrálása. Amikor regisztrál, be kell szereznie egy leíró beszerezni az a platform-specifikus leküldéses értesítési szolgáltatás (PNS). Ezt az értéket a címkékkel együtt, majd adja meg, a regisztráció létrehozásakor. Az alábbi kód regisztrálja a Windows-alkalmazást a leküldéses értesítések és a Windows értesítési szolgáltatásának (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Ha a WNS leküldendő, akkor meg kell [Microsoft Store csomagot SID](#package-sid).  További információ a Windows-alkalmazásokra, hogyan kell regisztrálni a sablon regisztrációhoz, beleértve: [Leküldéses értesítések hozzáadása az alkalmazáshoz].

Címkék kér az ügyfél nem támogatott.  A regisztrációs csendes eldobott címke kérelmek.
Ha az eszköz regisztrálása címkék, hozzon létre egy egyéni API-t, amely a regisztráció elvégzéséhez a nevünkben a Notification Hubs API segítségével.  [Az egyéni API](#customapi) ahelyett, hogy a `RegisterNativeAsync()` metódust.

### <a name="package-sid"></a>Hogyan: szerezze be a Microsoft Store csomag biztonsági azonosítója
A csomag biztonsági azonosítója leküldéses értesítések a Microsoft Store apps engedélyezése szükséges.  Ha szeretne kapni a csomag biztonsági AZONOSÍTÓJÁT, a Microsoft Store regisztrálhatja alkalmazását.

Ez az érték beszerzése:

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a Microsoft Store alkalmazás projektjére, kattintson a **tároló** > **az áruház alkalmazás társítása...** .
2. A varázslóban kattintson **következő**, jelentkezzen be Microsoft-fiókjával, adjon meg egy nevet az alkalmazáshoz a **lefoglalni egy új alkalmazás neve**, majd kattintson a **tartalék**.
3. Az alkalmazás-regisztráció sikeres létrehozása után válassza ki az alkalmazás nevére, kattintson a **következő**, és kattintson a **társítása**.
4. Jelentkezzen be a [A Windows fejlesztői központ] a Microsoft Account. A **alkalmazásaimat**, kattintson a létrehozott alkalmazás regisztrációját.
5. Kattintson a **Alkalmazáskezelés** > **identitását**, majd görgessen le a Keresés és a **CSOMAGAZONOSÍTÓT**.

A csomag biztonsági AZONOSÍTÓJÁT számos felhasználási kezelni egy URI-azonosítóként, ebben az esetben kell használnia *ms-app: / /* sémát. Jegyezze meg a csomag biztonsági azonosítója, ez az érték előtagjaként hozzáfűzésével megfelelő verzióját.

Xamarin-alkalmazásokat kell regisztrálnia az alkalmazást az iOS vagy Android rendszereken futó további kódot igényel. További információkért lásd: a témakör a platformra:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Útmutató: regisztráció leküldéses sablonok platformfüggetlen értesítések küldéséhez
Sablonok regisztrálásához használja a `RegisterAsync()` metódust használ a sablonokat, az alábbiak szerint:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

A sablonok kell `JObject` meg kell adnia, és tartalmazhat több sablonok a következő JSON formátummal:

```
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

A metódus **RegisterAsync()** másodlagos Csempéket is fogad:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Található összes kódcímkének számítógépnél tisztító vannak a biztonsági regisztrálása során. Címkék hozzáadása a telepítésekkel és sablonok telepítések belül, lásd: [használata a .NET-háttérrendszer server SDK az Azure Mobile Apps].

A regisztrált sablonok használatával értesítést küldeni, tekintse meg a [Notification Hubs API-k].

## <a name="misc"></a>Vegyes kapcsolatos témakörök
### <a name="errors"></a>Hogyan: hibák kezelésének
Ha hiba lép fel a háttér, az ügyfél SDK kivált egy `MobileServiceInvalidOperationException`.  A következő példa bemutatja, hogyan legyen kezelve az háttéralkalmazása által visszaadott kivétel:

```
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

Egy másik példa való hibaállapotok itt található: a [Mobilalkalmazások fájlok minta]. A [LoggingHandler] példa biztosít a naplózás delegált kezelő naplózni a háttérrendszer történik.

### <a name="headers"></a>Hogyan: testreszabása kérelem fejlécei
Az adott alkalmazást forgatókönyv támogatása érdekében szükség lehet a Mobile Apps-háttéralkalmazás kommunikáció testreszabása. Érdemes lehet például egy egyéni fejléc hozzáadását a kimenő kérelmek vagy is módosíthatja a válaszok állapotkódok. Használhat egyéni [DelegatingHandler], az alábbi példa szerint:

```
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
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Hitelesítés hozzáadása az alkalmazáshoz]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Leküldéses értesítések hozzáadása az alkalmazáshoz]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Regisztrálja az alkalmazást egy Microsoft-fiók bejelentkezési használata]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[App Service konfigurálása az Active Directory bejelentkezéshez]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[hoz létre egy típusos táblára mutató hivatkozás]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[érvénybe]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Kiválasztás]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Felhasználói azonosítóját]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Ha]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[A Windows fejlesztői központ]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[A Windows Live SDK]: https://msdn.microsoft.com/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API-k]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobilalkalmazások fájlok minta]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentáció]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
