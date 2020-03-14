---
title: A felügyelt ügyféloldali kódtár használata
description: Megtudhatja, hogyan használhatja a .NET ügyféloldali kódtárat Azure App Service Mobile Apps Windows-és Xamarin-alkalmazásokkal.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249372"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a felügyelt ügyféloldali kódtár használatával a Windows-és Xamarin-alkalmazások Azure App Service Mobile Apps. Ha még nem Mobile Apps, érdemes megfontolnia az [Azure Mobile apps][1] gyors üzembe helyezési oktatóanyagának elvégzését. Ebben az útmutatóban az ügyféloldali felügyelt SDK-ra fogunk összpontosítani. A Mobile Apps kiszolgálóoldali SDK-k további megismeréséhez tekintse meg a [.NET Server SDK][2] vagy a [Node. js Server SDK][3]dokumentációját.

## <a name="reference-documentation"></a>Dokumentáció
Az ügyfél-SDK dokumentációja itt található: [Azure Mobile apps .net-ügyfél referenciája][4].
Az [Azure-Samples GitHub-tárházban][5]számos ügyfél-mintát is talál.

## <a name="supported-platforms"></a>Támogatott platformok
A .NET platform a következő platformokat támogatja:

* Xamarin Android-kiadások a 19 – 24. API-hoz (KitKat – nugát)
* Xamarin iOS-kiadások az iOS 8,0-es és újabb verzióihoz
* Univerzális Windows-platform
* Windows Phone 8.1
* Windows Phone-telefon 8,0 Silverlight-alkalmazások kivételével

A "kiszolgálói folyamat" hitelesítés webnézetet használ a bemutatott felhasználói felületen.  Ha az eszköz nem tud Webnézeti felhasználói felületet bemutatni, akkor más hitelesítési módszerekre is szükség van.  Ez az SDK ezért nem alkalmas a Watch-Type vagy a hasonló módon korlátozott eszközökre.

## <a name="setup"></a>Telepítés és előfeltételek
Feltételezzük, hogy már létrehozta és közzétette a Mobile App backend-projektet, amely legalább egy táblázatot tartalmaz.  A témakörben használt kódban a tábla neve `TodoItem`, és a következő oszlopokkal rendelkezik: `Id`, `Text`és `Complete`. Ez a tábla ugyanaz a tábla, amely az [Azure Mobile apps][1]gyors üzembe helyezésének befejezése után jön létre.

A megfelelő begépelt ügyféloldali típus C# a következő osztály:

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

A [JsonPropertyAttribute][6] a *PropertyName* leképezésének definiálására szolgál az ügyfél és a tábla mezője között.

Ha meg szeretné tudni, hogyan hozhat létre táblákat a Mobile Apps-háttérben, tekintse meg a [.NET Server SDK témakört][7] vagy a [Node. js Server SDK témakört][8]. Ha a rövid útmutató segítségével hozta létre a Azure Portal Mobile apps-hátteret, akkor a [Azure Portalra] **egyszerű táblák** beállítását is használhatja.

### <a name="how-to-install-the-managed-client-sdk-package"></a>Útmutató: a felügyelt ügyféloldali SDK-csomag telepítése
A következő módszerek egyikével telepítheti a felügyelt ügyféloldali SDK-csomagot a [NuGet][9]Mobile apps:

* **Visual Studio** Kattintson a jobb gombbal a projektre, kattintson a **NuGet-csomagok kezelése**elemre, keresse meg a `Microsoft.Azure.Mobile.Client` csomagot, majd kattintson a **telepítés**gombra.
* **Xamarin Studio** Kattintson a jobb gombbal a projektre, kattintson a **hozzáadás** > **NuGet-csomagok hozzáadása**elemre, keresse meg a `Microsoft.Azure.Mobile.Client` csomagot, majd kattintson a **csomag hozzáadása**lehetőségre.

A fő tevékenység fájljában ne felejtse el hozzáadni a következő **using** utasítást:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Vegye figyelembe, hogy az Android-projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az SDK `Xamarin.Android.Support.CustomTabs` függőséget biztosít az Android platformhoz, így ha a projekt újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül a szükséges verzióval kell telepítenie a csomagot.

### <a name="symbolsource"></a>Útmutató: hibakeresési szimbólumok használata a Visual Studióban
A Microsoft. Azure. Mobile névtér szimbólumai a [SymbolSource][10]webhelyen érhetők el.  Tekintse át a [SymbolSource utasításokat][11] a SymbolSource a Visual Studióval való integrálásához.

## <a name="create-client"></a>Az Mobile Apps-ügyfél létrehozása
A következő kód létrehozza a [MobileServiceClient][12] objektumot, amely a Mobile apps-háttér elérésére szolgál.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Az előző kódban cserélje le a `MOBILE_APP_URL` a Mobile apps-háttér URL-címére, amely a [Azure Portalra]a Mobile apps-háttér paneljén található. A MobileServiceClient objektumnak egypéldányos kell lennie.

## <a name="work-with-tables"></a>Táblázatok használata
A következő szakasz részletesen ismerteti a rekordok keresését és beolvasását, valamint a táblázaton belüli adatok módosítását.  A szakasz az alábbi témaköröket tartalmazza:

* [Táblázatos hivatkozás létrehozása](#instantiating)
* [Adatlekérdezés](#querying)
* [Visszaadott adatértékek szűrése](#filtering)
* [Visszaadott adatsorok rendezése](#sorting)
* [A lapokon tárolt adatvisszaadás](#paging)
* [Adott oszlopok kiválasztása](#selecting)
* [Rekord megkeresése azonosító alapján](#lookingup)
* [Nem típusos lekérdezések kezelése](#untypedqueries)
* [Adatbeszúrás](#inserting)
* Az Adatfrissítés
* [Az adattörlés](#deleting)
* [Ütközés feloldása és optimista párhuzamosság](#optimisticconcurrency)
* [Kötés Windows felhasználói felülettel](#binding)
* [Az Oldalméret módosítása](#pagesize)

### <a name="instantiating"></a>Útmutató: table-hivatkozás létrehozása
Az összes olyan kód, amely egy háttér-táblában lévő adatokhoz fér hozzá vagy módosít egy `MobileServiceTable` objektumban található függvényeket. Szerezzen be egy hivatkozást a táblára a [GetTable] metódus meghívásával a következőképpen:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

A visszaadott objektum a beírt szerializálási modellt használja. A nem típusos szerializálási modell is támogatott. A következő példa [egy nem típusos táblára mutató hivatkozást hoz létre]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

A nem típusos lekérdezésekben meg kell adnia a mögöttes OData lekérdezési karakterláncot.

### <a name="querying"></a>Útmutató: adatok lekérdezése a mobil alkalmazásból
Ez a szakasz azt ismerteti, hogyan lehet lekérdezéseket kibocsátani a Mobile apps-háttérbe, amely a következő funkciókat tartalmazza:

* [Visszaadott adatértékek szűrése](#filtering)
* [Visszaadott adatsorok rendezése](#sorting)
* [A lapokon tárolt adatvisszaadás](#paging)
* [Adott oszlopok kiválasztása](#selecting)
* [Az adatkeresés azonosító alapján](#lookingup)

> [!NOTE]
> A kiszolgáló által vezérelt oldal mérete kényszerítve van, hogy a rendszer ne adja vissza az összes sort.  A lapozás megtartja a nagyméretű adathalmazok alapértelmezett kéréseit, amelyek negatív hatással vannak a szolgáltatásra.  Ha több mint 50 sort szeretne visszaadni, használja a `Skip` és a `Take` metódust a következő témakörben ismertetett módon: az [adatküldés a lapokon](#paging).

### <a name="filtering"></a>Útmutató: a visszaadott adatértékek szűrése
A következő kód bemutatja, hogyan szűrheti az adatszűrést egy lekérdezés `Where` záradékával. Visszaadja a `todoTable` összes olyan elemét, amelynek `Complete` tulajdonsága egyenlő `false`. A [Ahol] függvény egy sor szűrési predikátumot alkalmaz a lekérdezésre a táblán.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Megtekintheti a háttérnek küldött kérelem URI-JÁT az üzenet-ellenőrzési szoftverekkel, például a böngésző fejlesztői eszközeivel vagy a [Fiddler]használatával. Ha megtekinti a kérelem URI-JÁT, figyelje meg, hogy a lekérdezési karakterlánc módosult:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Ezt a OData-kérelmet a Server SDK egy SQL-lekérdezésre fordítja le:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A `Where` metódusnak átadott függvény tetszőleges számú feltételt tartalmazhat.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Ezt a példát a Server SDK egy SQL-lekérdezésre fordítja le:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

A lekérdezés több kikötésre is bontható:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

A két módszer egyenértékű, és szinonimaként is használható.  A korábbi lehetőség&mdash;több predikátum összefűzése egy lekérdezésben&mdash;a tömörítés és a javasolt.

A `Where` záradék a OData részhalmazra lefordított műveleteket támogatja. A műveletek a következők:

* Viszonyítási operátorok (= =,! =, <, < =, >, > =),
* Aritmetikai operátorok (+,-,/, *,%)
* Szám pontossága (Math. floor, Math. plafon),
* Karakterlánc-függvények (hossz, alkarakterlánc, csere, IndexOf, StartsWith, EndsWith),
* Dátum tulajdonságai (év, hónap, nap, óra, perc, másodperc),
* Objektumhoz való hozzáférés tulajdonságai és
* A fenti műveletek bármelyikét egyesítő kifejezések.

A Server SDK által támogatott szempontokat figyelembe véve megtekintheti az [OData v3 – dokumentáció].

### <a name="sorting"></a>Útmutató: a visszaadott adathalmazok rendezése
Az alábbi kód azt szemlélteti, hogyan rendezheti az adatrendezést a lekérdezésben lévő [OrderBy] vagy [OrderByDescending] függvénnyel. A `todoTable` a `Text` mező alapján növekvő sorrendbe rendezi az elemeket.

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

### <a name="paging"></a>Útmutató: a lapokon tárolt adatvisszaadás
Alapértelmezés szerint a háttérrendszer csak az első 50 sort adja vissza. Megnövelheti a visszaadott sorok számát a [Eltarthat] metódus meghívásával. A [Kihagyása] metódussal együtt használja a `Take`t a lekérdezés által visszaadott teljes adatkészlet adott "oldalának kéréséhez. A következő lekérdezés a végrehajtáskor a tábla első három elemét adja vissza.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Az alábbi felülvizsgált lekérdezés kihagyja az első három találatot, és a következő három eredményt adja vissza. Ez a lekérdezés az adatok második "lapját" állítja elő, ahol az oldalméret három elem.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A [IncludeTotalCount] metódus az *összes* visszaadott rekord teljes számát kéri, figyelmen kívül hagyva a megadott lapozási/korlátozási záradékot:

```csharp
query = query.IncludeTotalCount();
```

Egy valós alkalmazásban az előző példához hasonló lekérdezések használhatók a lapok közötti váltáshoz egy személyhívó vezérlőelemmel vagy hasonló KEZELŐFELÜLETtel.

> [!NOTE]
> Egy Mobile apps-háttér 50-as korlátjának felülbírálásához a [EnableQueryAttribute] a nyilvános get metódusra is alkalmaznia kell, és meg kell adnia a lapozási viselkedést. Ha alkalmazva van a metódusra, a következő beállítja a maximálisan visszaadott sorokat 1000-re:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Útmutató: adott oszlopok kiválasztása
Megadhatja, hogy mely tulajdonságok szerepeljenek az eredmények között egy [Kiválasztás] záradék hozzáadásával a lekérdezéshez. Az alábbi kód például azt mutatja be, hogyan lehet csak egy mezőt kijelölni, és hogyan lehet kijelölni és formázni több mezőt:

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

Az eddig leírt függvények az adalékok, így továbbra is megtarthatjuk a láncot. Minden láncolt hívás több lekérdezést is érint. Még egy példa:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Útmutató: az adatkeresés azonosító alapján
A [LookupAsync] függvény használatával kereshet objektumokat az adatbázisból egy adott azonosítóval.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Útmutató: nem típusos lekérdezések végrehajtása
Ha nem típusos tábla objektummal hajt végre lekérdezést, explicit módon meg kell adnia a OData lekérdezési karakterláncot a [ReadAsync]meghívásával, ahogy az alábbi példában látható:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

A rendszer visszaküldi a JSON-értékeket, amelyeket használhat, például egy tulajdonság táska. A JToken és a Newtonsoft Json.NET kapcsolatos további információkért tekintse meg a [JSON.net] webhelyet.

### <a name="inserting"></a>Útmutató: az adatbeszúrás egy Mobile apps-háttérbe
Minden ügyfél-típusnak tartalmaznia kell egy **azonosító**nevű tagot, amely alapértelmezés szerint karakterlánc. Ez az **azonosító** a szifilisz-műveletek végrehajtásához és az offline szinkronizáláshoz szükséges. A következő kód bemutatja, hogyan szúrhat be új sorokat egy táblába a [InsertAsync] metódus használatával. A paraméter a .NET-objektumként beszúrandó adattípusokat tartalmazza.

```csharp
await todoTable.InsertAsync(todoItem);
```

Ha egy Beszúrás során a `todoItem` nem tartalmazza egyedi egyéni azonosító értékét, a kiszolgáló GUID azonosítót hoz létre.
A generált azonosító lekéréséhez vizsgálja meg az objektumot a hívás visszaküldése után.

A nem típusos adatmennyiségek beszúrásához használhatja a Json.NET előnyeit:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Az alábbi példa egy e-mail-címet használ egyedi karakterlánc-azonosítóként:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>AZONOSÍTÓ értékek használata
Mobile Apps támogatja a tábla **azonosító** oszlopának egyedi egyéni karakterlánc-értékeit. A sztringek lehetővé teszik az alkalmazások számára, hogy egyéni értékeket, például e-mail-címeket vagy felhasználóneveket használjanak az AZONOSÍTÓhoz.  A karakterlánc-azonosítók a következő előnyöket nyújtják:

* Az azonosítók létrehozása anélkül történik meg, hogy az adatbázisba oda kellene bejutni.
* A rekordok könnyebben egyesíthetők különböző táblákból vagy adatbázisokból.
* Az azonosító értékek jobban integrálhatók az alkalmazás logikájának használatával.

Ha egy karakterlánc-azonosító érték nincs beszúrt rekordra beállítva, a mobil alkalmazás háttere egyedi értéket hoz létre az AZONOSÍTÓhoz. A [GUID. NewGuid] metódus használatával a saját azonosító értékeit is létrehozhatja az ügyfélen vagy a háttérben.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Útmutató: Az adatmódosítás a Mobile apps-háttérben
A következő kód azt mutatja be, hogyan használható a [UpdateAsync] metódus egy meglévő rekordnak az új információkkal való frissítésére UGYANAZZAL az azonosítóval. A paraméter tartalmazza a .NET-objektumként frissítendő adattípusokat.

```csharp
await todoTable.UpdateAsync(todoItem);
```

A nem típusos adatmennyiségek frissítéséhez a következőképpen használhatja a [JSON.net] :

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Frissítés készítésekor meg kell adni egy `id` mezőt. A háttérrendszer a `id` mezőt használja a frissítendő sor azonosítására. A `id` mező a `InsertAsync` hívás eredménye alapján szerezhető be. Egy `ArgumentException` akkor jön létre, ha a `id` érték megadása nélkül próbál frissíteni egy adott tételt.

### <a name="deleting"></a>Útmutató: az adattörlés egy Mobile apps-háttérbeli háttérben
A következő kód bemutatja, hogyan használható a [DeleteAsync] metódus egy meglévő példány törléséhez. A példányt a `todoItem`beállított `id` mező azonosítja.

```csharp
await todoTable.DeleteAsync(todoItem);
```

A nem típusos adatmennyiségek törléséhez a Json.NET az alábbiak szerint használhatja ki:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Törlési kérelem esetén meg kell adni egy azonosítót. A szolgáltatás nem továbbítja a többi tulajdonságot, vagy figyelmen kívül hagyja a szolgáltatást. Egy `DeleteAsync` hívás eredménye általában `null`. Az átadni kívánt azonosító a `InsertAsync` hívás eredményében szerezhető be. Egy `MobileServiceInvalidOperationException` akkor kerül kiválasztásra, ha a `id` mező megadása nélkül próbál törölni egy elemeket.

### <a name="optimisticconcurrency"></a>Útmutató: optimista Egyidejűség használata az ütközés feloldásához
Két vagy több ügyfél egyszerre is írhat módosításokat ugyanarra az objektumra. Ütközések észlelése nélkül az utolsó írás felülírja a korábbi frissítéseket. Az **optimista egyidejűségi vezérlés** feltételezi, hogy minden tranzakció véglegesíthető, ezért nem használ erőforrás-zárolást.  A tranzakció véglegesítése előtt az optimista Egyidejűség-vezérlés ellenőrzi, hogy egyetlen másik tranzakció sem módosította-e az adatfeldolgozást. Ha az adatgyűjtés módosult, a véglegesítési tranzakció vissza lesz állítva.

Mobile Apps támogatja az optimista Egyidejűség-vezérlést az egyes elemek változásainak nyomon követésével a Mobile apps-háttér minden egyes táblájához megadott `version` rendszertulajdonság oszlop használatával. Minden alkalommal, amikor egy rekord frissül, Mobile Apps beállítja a rekord `version` tulajdonságát egy új értékre. Az egyes frissítési kérelmek során a kérésben szereplő rekord `version` tulajdonsága a kiszolgálón lévő rekordhoz képest ugyanahhoz a tulajdonsághoz lesz hasonlítva. Ha a kérelemmel átadott verzió nem felel meg a háttérnek, akkor az ügyféloldali kódtár `MobileServicePreconditionFailedException<T>` kivételt vált ki. A kivételben szereplő típus a rekord kiszolgáló verzióját tartalmazó háttérbeli rekord. Az alkalmazás ezt az információt felhasználva eldöntheti, hogy a frissítési kérést újra végrehajtja-e a megfelelő `version` értékkel a háttérből a módosítások elvégzéséhez.

Adjon meg egy oszlopot a Table osztályban a `version` rendszer tulajdonsághoz az optimista Egyidejűség engedélyezéséhez. Például:

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

A nem típusos táblákat használó alkalmazások esetében az optimista párhuzamosságot az alábbi módon állíthatja be a táblázat `SystemProperties` `Version` jelzőjét.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Az optimista Egyidejűség engedélyezése mellett a [UpdateAsync]meghívásakor is meg kell fognia a kódban `MobileServicePreconditionFailedException<T>` kivételt.  Oldja fel az ütközést úgy, hogy a megfelelő `version` alkalmazza a frissített rekordra, és a megoldott rekorddal hívja meg a [UpdateAsync] . A következő kód bemutatja, hogyan oldható fel az írási ütközés az észlelést követően:

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

További információ: [Offline adatszinkronizálás az Azure Mobile Appsban] témakörben.

### <a name="binding"></a>Útmutató: Mobile Apps-információ kötése Windows felhasználói felülethez
Ez a szakasz bemutatja, hogyan jelenítheti meg a visszaadott adatobjektumokat a felhasználói felületi elemek használatával egy Windows-alkalmazásban.  A következő mintakód a lista forrásához kötődik a hiányos elemek lekérdezésével. A [MobileServiceCollection] létrehoz egy Mobile apps-kompatibilis kötési gyűjteményt.

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

A felügyelt futtatókörnyezet egyes vezérlői támogatják a [ISupportIncrementalLoading]nevű felületet. Ez az interfész lehetővé teszi, hogy a vezérlőelemek további adatkérést kérjenek a felhasználó görgetésekor. Az univerzális Windows-alkalmazások számára a [MobileServiceIncrementalLoadingCollection]-on keresztül beépített támogatás érhető el ehhez az interfészhez, amely automatikusan kezeli a hívásokat a vezérlőktől. A Windows-alkalmazásokban a következőképpen használhatja a `MobileServiceIncrementalLoadingCollection`:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Ha az új gyűjteményt Windows Phone-telefon 8 és a "Silverlight" alkalmazásban szeretné használni, használja a `ToCollection` bővítmény metódusait `IMobileServiceTableQuery<T>` és `IMobileServiceTable<T>`. Az betöltéshez hívja meg a `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Ha `ToCollectionAsync` vagy `ToCollection`hívásával létrehozott gyűjteményt használ, egy olyan gyűjteményt kap, amely felhasználói felületi vezérlőkhöz köthető.  Ez a gyűjtemény lapozófájl-kompatibilis.  Mivel a gyűjtemény adatok betöltése a hálózatról történik, a betöltés néha sikertelen lesz. Az ilyen hibák kezeléséhez bírálja felül a `MobileServiceIncrementalLoadingCollection` `OnException` metódusát, hogy kezelni tudja a `LoadMoreItemsAsync`hívásainak miatti kivételeket.

Vegye figyelembe, hogy a tábla sok mezőből áll, de csak néhányat szeretne megjeleníteni a vezérlőelemben. A felhasználói felületen megjelenítendő egyes oszlopok kiválasztásához használhatja az előző, "[adott oszlopok kiválasztása](#selecting)" című szakaszban található útmutatást.

### <a name="pagesize"></a>Oldalméret módosítása
Az Azure Mobile Apps alapértelmezés szerint legfeljebb 50 elemet ad vissza kérelemként.  A lapozófájl méretét úgy módosíthatja, hogy az ügyfélen és a kiszolgálón is megnöveli az oldalméret maximális méretét.  A kért oldalméret növeléséhez a `PullAsync()`használatakor `PullOptions`t kell megadnia:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Feltételezve, hogy a-kiszolgálón a `PageSize` egyenlő vagy nagyobb, mint 100, a kérelem legfeljebb 100 elemet ad vissza.

## <a name="#offlinesync"></a>Kapcsolat nélküli táblák használata
Az offline táblák helyi SQLite-tárolót használnak az adatkapcsolat nélküli használatra.  Az összes tábla-művelet a távoli kiszolgáló tárolója helyett a helyi SQLite-tárolón történik.  Offline tábla létrehozásához először készítse elő a projektet:

1. A Visual Studióban kattintson a jobb gombbal a megoldásra > **NuGet-csomagok kezelése**a megoldáshoz..., majd keresse meg és telepítse a **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet-csomagot a megoldás összes projektje számára.
2. Választható A Windows-eszközök támogatásához telepítse a következő SQLite Runtime-csomagok egyikét:

   * **Windows 8,1 futtatókörnyezet:** Telepítse [a Windows 8,1 rendszerhez készült SQLite][3]-t.
   * **Windows Phone-telefon 8,1:** [Az SQLite telepítése Windows Phone-telefon 8,1][4]-re.
   * **Univerzális Windows-platform** [Az SQLite telepítése az univerzális Windows rendszerre][5].
3. (Nem kötelező). Windows-eszközök esetén kattintson a **hivatkozások** > **hivatkozás hozzáadása...** elemre, bontsa ki a **Windows** mappa > **bővítmények**csomópontot, majd engedélyezze a megfelelő **SQLite for Windows** SDK-t, valamint a **Visual C++ 2013 Runtime for Windows** SDK-t.
    Az SQLite SDK-nevek kis mértékben eltérőek az egyes Windows platformokon.

A tábla hivatkozásának létrehozása előtt elő kell készíteni a helyi tárolót:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

A tároló inicializálása általában közvetlenül az ügyfél létrehozása után történik.  A **OfflineDbPath** az összes támogatott platformon használható fájlnévnek kell lennie.  Ha az elérési út teljesen minősített elérési út (azaz perjeltel kezdődik), akkor a rendszer az elérési utat használja.  Ha az elérési út nem teljes mértékben minősített, a fájl egy platform-specifikus helyre kerül.

* Az iOS-és Android-eszközök esetében az alapértelmezett elérési út a "személyes fájlok" mappa.
* Windows-eszközök esetén az alapértelmezett elérési út az alkalmazásspecifikus "AppData" mappa.

A `GetSyncTable<>` metódussal beszerezhetők táblázatos referenciák:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

A hitelesítéshez nem szükséges a kapcsolat nélküli tábla használata.  Csak akkor kell hitelesítenie magát, amikor a háttér-szolgáltatással kommunikál.

### <a name="syncoffline"></a>Offline tábla szinkronizálása
Alapértelmezés szerint a rendszer nem szinkronizálja az offline táblákat a háttérrel.  A szinkronizálás két darabra oszlik.  A módosításokat külön is leküldheti az új elemek letöltésével.  Íme egy tipikus szinkronizálási módszer:

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

Ha a `PullAsync` első argumentuma null értékű, akkor a növekményes szinkronizálás nincs használatban.  Minden szinkronizálási művelet lekérdezi az összes rekordot.

Az SDK implicit `PushAsync()` hajt végre a rekordok húzása előtt.

Az ütközések kezelését `PullAsync()` metódussal történik.  Az ütközéseket ugyanúgy kezelheti, mint az online táblákat.  Az ütközés akkor jön létre, ha a `PullAsync()` hívása az INSERT, a Update vagy a DELETE helyett történik. Ha több ütközés történik, azok egyetlen MobileServicePushFailedException vannak becsomagolva.  Az egyes hibák kezelése külön történik.

## <a name="#customapi"></a>Egyéni API használata
Az egyéni API-k olyan egyéni végpontok definiálását teszik lehetővé, amelyek olyan kiszolgáló-funkciókat tesznek elérhetővé, amelyek nem képezhetők be a beszúrási, frissítési, törlési és olvasási műveletekhez. Egyéni API-k használatával nagyobb mértékben szabályozhatja az üzenetkezelést, többek között a HTTP-üzenetek fejlécének olvasását és beállítását, valamint a JSON formátumon kívüli üzenettörzs definiálását is.

Egy egyéni API-t úgy hívhat meg, hogy meghívja az egyik [InvokeApiAsync] metódust az ügyfélen. A következő kódrészlet például egy POST-kérést küld a **completeAll** API-nak a háttérbe:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ez az űrlap egy begépelt metódus hívása, amely megköveteli, hogy a **MarkAllResult** visszatérési típusa meg legyen adva. A beírt és a nem típusos metódusok egyaránt támogatottak.

A InvokeApiAsync () metódus "/API/" paraméterként megadott a hívni kívánt API-ra, kivéve, ha az API egy "/" karakterrel kezdődik.
Például:

* `InvokeApiAsync("completeAll",...)` hívások/api/completeAll a háttéren
* `InvokeApiAsync("/.auth/me",...)` hívások/.auth/Me a háttéren

A InvokeApiAsync segítségével bármilyen WebAPI hívhat meg, beleértve azokat a webapi-kat is, amelyek nincsenek definiálva az Azure Mobile Apps-ban.  A InvokeApiAsync () használatakor a rendszer elküldi a megfelelő fejléceket (beleértve a hitelesítési fejléceket is) a kérelemmel együtt.

## <a name="authentication"></a>Felhasználók hitelesítése
Mobile Apps támogatja az alkalmazások felhasználóinak hitelesítését és engedélyezését különféle külső identitás-szolgáltatók használatával: Facebook, Google, Microsoft-fiók, Twitter és Azure Active Directory. A táblákra vonatkozó engedélyeket úgy állíthatja be, hogy az adott műveletekhez való hozzáférést csak a hitelesített felhasználókra korlátozza. A hitelesített felhasználók identitását is használhatja a kiszolgálói parancsfájlok engedélyezési szabályainak megvalósításához. További információt a [Hitelesítés hozzáadása az alkalmazáshoz] ismertető oktatóanyagban találhat.

Két hitelesítési folyamat támogatott: az *ügyfél által felügyelt* és a *kiszolgáló által felügyelt* folyamat. A kiszolgáló által felügyelt folyamat biztosítja a legegyszerűbb hitelesítési felületet, mivel a szolgáltató webes hitelesítési felületén alapul. Az ügyfél által felügyelt folyamat lehetővé teszi az eszközre jellemző képességekkel való mélyebb integrációt, mivel a szolgáltatóra jellemző, eszközre jellemző SDK-kat használ.

> [!NOTE]
> Javasoljuk, hogy használjon egy ügyfél által felügyelt folyamatot az éles alkalmazásokban.

A hitelesítés beállításához regisztrálnia kell az alkalmazást egy vagy több identitás-szolgáltatóval.  Az identitás-szolgáltató létrehoz egy ügyfél-azonosítót és egy ügyfél-titkot az alkalmazáshoz.  A rendszer ezeket az értékeket a háttérbe állítja be a Azure App Service hitelesítés/engedélyezés engedélyezéséhez.  További információért kövesse az oktatóanyagban a [hitelesítés hozzáadása az alkalmazáshoz]című témakör részletes utasításait.

A szakasz a következő témaköröket tartalmazza:

* [Ügyfél által felügyelt hitelesítés](#clientflow)
* [Kiszolgáló által felügyelt hitelesítés](#serverflow)
* [A hitelesítési jogkivonat gyorsítótárazása](#caching)

### <a name="clientflow"></a>Ügyfél által felügyelt hitelesítés
Az alkalmazás önállóan kapcsolatba léphet az identitás-szolgáltatóval, majd a háttérbe való bejelentkezés során megadhatja a visszaadott tokent. Ez az ügyféloldali folyamat lehetővé teszi, hogy egyszeri bejelentkezési élményt nyújtson a felhasználóknak, vagy további felhasználói adatok kérhetők le az identitás-szolgáltatótól. Az ügyfél-átfolyásos hitelesítés előnyben részesített egy olyan kiszolgálói folyamat használatával, mint az Identity Provider SDK, amely egy natív UX-élményt biztosít, és lehetővé teszi a további testreszabást.

Példák a következő ügyféloldali hitelesítési mintákhoz:

* [Active Directory-hitelesítési tár](#adal)
* [Facebook vagy Google](#client-facebook)

#### <a name="adal"></a>Felhasználók hitelesítése a Active Directory-hitelesítési tár
A Active Directory-hitelesítési tár (ADAL) használatával kezdeményezheti a felhasználói hitelesítést az ügyféltől Azure Active Directory hitelesítéssel.

1. A HRE-bejelentkezéshez a [App Service konfigurálása Active Directory bejelentkezéshez] oktatóanyagban című témakörben található. Győződjön meg arról, hogy a natív ügyfélalkalmazás regisztrálásának nem kötelező lépéseit kell végrehajtania.
2. A Visual Studióban vagy a Xamarin Studióban nyissa meg a projektet, és adjon hozzá egy hivatkozást a `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet csomaghoz. Kereséskor adja meg a kiadás előtti verziókat.
3. Adja hozzá a következő kódot az alkalmazáshoz a használt platform alapján. Minden esetben végezze el a következő cseréket:

   * Cserélje le a **Insert-Authority-here** nevet annak a bérlőnek a nevére, amelyben az alkalmazást kiépítte. A formátumnak https://login.microsoftonline.com/contoso.onmicrosoft.comnak kell lennie. Ez az érték a [Azure Portalra]Azure Active Directory tartomány lapjáról másolható.
   * Cserélje le a **Insert-Resource-id-** t a Mobile apps-háttér ügyfél-azonosítójával. Az ügyfél-azonosítót a portál **Azure Active Directory beállítások** területén található **speciális** lapon szerezheti be.
   * Cserélje le az **Insert-Client-ID-** t a natív ügyfélalkalmazás által másolt ügyfél-azonosítóra.
   * Cserélje le a **Insert-redirect-URI-t – itt** a hely */.auth/login/Done* -végpontján a https-séma használatával. Ennek az értéknek a *https://contoso.azurewebsites.net/.auth/login/donehoz* hasonlónak kell lennie.

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

#### <a name="client-facebook"></a>Egyszeri bejelentkezés Facebook-vagy Google-token használatával
Az ügyfél folyamatát használhatja a Facebook vagy a Google jelen kódrészletében is látható módon.

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

### <a name="serverflow"></a>Kiszolgáló által felügyelt hitelesítés
Miután regisztrálta az identitás-szolgáltatót, hívja meg a [LoginAsync] metódust a [MobileServiceClient] eszközön a szolgáltató [MobileServiceAuthenticationProvider] értékével. A következő kód például a Facebook használatával kezdeményezi a kiszolgálói folyamat bejelentkezését.

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

Ha a Facebooktól eltérő identitás-szolgáltatót használ, módosítsa a [MobileServiceAuthenticationProvider] értékét a szolgáltató értékére.

A kiszolgálói folyamatokban a Azure App Service kezeli a OAuth hitelesítési folyamatát a kiválasztott szolgáltató bejelentkezési oldalának megjelenítésével.  Ha az identitás-szolgáltató visszatér, Azure App Service létrehoz egy App Service hitelesítési tokent. A [LoginAsync] metódus egy [MobileServiceUser]ad vissza, amely a hitelesített felhasználó és a [MobileServiceAuthenticationToken]felhasználói [UserID] is megadja JSON webes jogkivonatként (JWT). Ez a token gyorsítótárazható, és újra felhasználható, amíg le nem jár. További információ: [a hitelesítési jogkivonat gyorsítótárazása](#caching).

### <a name="caching"></a>A hitelesítési jogkivonat gyorsítótárazása
Bizonyos esetekben a bejelentkezési módszer meghívása elkerülhető az első sikeres hitelesítés után, ha a szolgáltatótól tárolja a hitelesítési jogkivonatot.  A Microsoft Store és a UWP alkalmazások a [PasswordVault] használatával gyorsítótárba helyezhetik az aktuális hitelesítési tokent a sikeres bejelentkezés után, a következőképpen:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

A felhasználóazonosító értékét a hitelesítő adat felhasználóneveként tárolja a rendszer, a jogkivonat pedig jelszóként tárolódik. A későbbi elindítások során megtekintheti a gyorsítótárazott hitelesítő adatok **PasswordVault** . Az alábbi példa gyorsítótárazott hitelesítő adatokat használ a megtalált példányok esetén, és egyéb módon kísérli meg a hitelesítést a háttérrel:

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

Amikor kijelentkezik egy felhasználót, el kell távolítania a tárolt hitelesítő adatokat is a következő módon:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

A Xamarin-alkalmazások a [Xamarin. auth] API-kat használják a hitelesítő adatok biztonságos tárolására egy **fiók** objektumban. Az API-k használatával kapcsolatos példát a [ContosoMoments Photo Sharing minta](https://github.com/azure-appservice-samples/ContosoMoments) [AuthStore.cs] -kódjában talál.

Ha ügyfél által felügyelt hitelesítést használ, akkor a szolgáltatótól kapott hozzáférési tokent (például Facebook vagy Twitter) is gyorsítótárazhatja. Ezt a jogkivonatot úgy lehet megadni, hogy új hitelesítési tokent igényeljen a háttérből, a következőképpen:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Leküldéses értesítések
A következő témakörök leküldéses értesítéseket foglalnak magukban:

* [Regisztráció leküldéses értesítésekhez](#register-for-push)
* [Microsoft Store csomag biztonsági azonosítójának beszerzése](#package-sid)
* [Regisztráció platformfüggetlen sablonokkal](#register-xplat)

### <a name="register-for-push"></a>Útmutató: a leküldéses értesítések regisztrálása
Az Mobile Apps ügyfél lehetővé teszi a leküldéses értesítések regisztrálását az Azure Notification Hubs használatával. A regisztráláskor a platform-specifikus leküldéses értesítési szolgáltatástól (PNS) beszerezhető leírót kell beszereznie. Ezt az értéket a regisztráció létrehozásakor minden címkével együtt meg kell adnia. A következő kód regisztrálja Windows-alkalmazását leküldéses értesítéseket a Windows Notification Service (WNS) szolgáltatással:

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Ha a WNS-ra kattint, be kell [szereznie egy Microsoft Store csomag SID](#package-sid)-t.  A Windows-alkalmazásokkal kapcsolatos további információkért, beleértve a sablonok regisztrációjának regisztrálását is, lásd: [leküldéses értesítések hozzáadása az alkalmazáshoz].

A címkék az ügyféltől való kérelmezése nem támogatott.  A címkézési kérelmeket a rendszer csendben elveti a regisztrációból.
Ha címkével szeretné regisztrálni az eszközt, hozzon létre egy egyéni API-t, amely a Notification Hubs API-t használja a regisztráció elvégzéséhez az Ön nevében.  Az `RegisterNativeAsync()` metódus helyett hívja meg az egyéni API-t.

### <a name="package-sid"></a>Útmutató: Microsoft Store csomag biztonsági azonosítójának beszerzése
A leküldéses értesítések Microsoft Store alkalmazásokban való engedélyezéséhez csomag biztonsági azonosítója szükséges.  A csomag biztonsági azonosítójának fogadásához regisztrálja az alkalmazást a Microsoft Store.

Az érték beszerzése:

1. A Visual Studio Megoldáskezelőban kattintson a jobb gombbal a Microsoft Store alkalmazás-projektre, majd kattintson az **áruházban** > **az alkalmazás hozzárendelése az áruházhoz..** . elemre.
2. A varázslóban kattintson a **tovább**gombra, jelentkezzen be a Microsoft-fiókba, írja be az alkalmazás nevét a **foglaláshoz**, és kattintson a **tartalék**elemre.
3. Az alkalmazás regisztrációjának sikeres létrehozása után válassza ki az alkalmazás nevét, kattintson a **tovább**, majd a **hozzárendelés**elemre.
4. Jelentkezzen be a [Windows fejlesztői központ] a Microsoft-fiókjával. A **saját alkalmazások**alatt kattintson a létrehozott alkalmazás-regisztrációra.
5. Kattintson az **alkalmazás-kezelés** > **alkalmazás identitása**elemre, majd görgessen le a **csomag biztonsági azonosítójának**megkereséséhez.

A csomag biztonsági azonosítójának számos használata URI-ként van kezelve, ebben az esetben az *MS-app://* értéket kell használnia a sémaként. Jegyezze fel a csomag SID-verzióját, amelyet az érték előtagként való összefűzésével hozott létre.

A Xamarin-alkalmazásokhoz további kód szükséges ahhoz, hogy regisztrálni lehessen az iOS-vagy Android-platformokon futó alkalmazásokat. További információt a platform témakörében talál:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Útmutató: leküldéses sablonok regisztrálása platformfüggetlen értesítések küldéséhez
A sablonok regisztrálásához használja a `RegisterAsync()` metódust a sablonokkal, a következőképpen:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

A sablonoknak `JObject` típusúnak kell lenniük, és több sablont is tartalmazhatnak a következő JSON formátumban:

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

A **RegisterAsync ()** metódus a másodlagos csempéket is elfogadja:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

A biztonsági regisztráció során az összes címkét el kell távolítani. Ha címkéket szeretne hozzáadni a telepítésekhez vagy sablonokhoz a telepítések között, tekintse meg a következőt: [az Azure-hoz készült .NET backend Server SDK használata Mobile Apps].

Az alábbi regisztrált sablonokat használó értesítések küldéséhez tekintse meg a [Notification Hubs API-k].

## <a name="misc"></a>Egyéb témakörök
### <a name="errors"></a>Útmutató: hibák kezelése
Ha hiba lép fel a háttérben, az ügyfél-SDK egy `MobileServiceInvalidOperationException`t hoz létre.  Az alábbi példa bemutatja, hogyan kezelheti a háttér által visszaadott kivételt:

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

A hibák feltételeit egy másik példán is megtalálhatja a [Mobile Apps fájlok minta]. A [LoggingHandler] példa egy naplózási delegált kezelőt biztosít a háttérre irányuló kérelmek naplózásához.

### <a name="headers"></a>Útmutató: a kérelmek fejlécének testreszabása
Az adott alkalmazás-forgatókönyv támogatásához előfordulhat, hogy testre kell szabnia a kommunikációt a Mobile apps-háttérrel. Előfordulhat például, hogy egyéni fejlécet szeretne hozzáadni minden kimenő kérelemhez, vagy akár a válaszok állapotkódot is módosítja. Használhat egyéni [DelegatingHandler]is, ahogy az alábbi példában is látható:

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
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Leküldéses értesítések hozzáadása az alkalmazáshoz]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[App Service konfigurálása Active Directory bejelentkezéshez]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[egy nem típusos táblára mutató hivatkozást hoz létre]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Eltarthat]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Kiválasztás]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Kihagyása]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Ahol]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portalra]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows fejlesztői központ]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API-k]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps fájlok minta]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 – dokumentáció]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
