---
title: Az App Service Mobile Apps felügyelt ügyfélkódtárának használata |} A Microsoft Docs
description: Ismerje meg, hogy a .NET ügyféloldali kódtár használata az Azure App Service Mobile Apps Windows és a Xamarin-alkalmazások.
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
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 8f014f1cb40e1a629d1989f00805fc91015a3ae9
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886012"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan lehet a felügyelt ügyféloldali kódtár használatával az Azure App Service Mobile Apps a Windows és a Xamarin-alkalmazások általános forgatókönyveinek végrehajtásával. Ha most ismerkedik a Mobile Apps, érdemes lehet először befejezése a [Azure Mobile Apps – első lépések] [ 1] oktatóanyag. Ebben az útmutatóban koncentrálunk a felügyelt ügyféloldali SDK. További információ a kiszolgálóoldali SDK-k a Mobile Apps, lásd a dokumentációban a [.NET Server SDK] [ 2] vagy a [Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Segédanyagok
A referencia dokumentációja az ügyfél SDK is található itt: [Az Azure Mobile Apps .NET client hivatkozás][4].
A több ügyfél mintát is megkeresheti a [Azure-minták GitHub-adattár][5].

## <a name="supported-platforms"></a>A támogatott platformok
A .NET-platformról a következő platformokat támogatja:

* Xamarin Android API 19 kiadását – 24 (KitKat Nougat keresztül)
* Xamarin iOS-kiadások IOS 8.0 és újabb verziók
* Univerzális Windows-platform
* Windows Phone 8.1
* Windows Phone 8.0-s kivételével a Silverlight alkalmazások részére

A "server-folyamat" hitelesítési olyan WebView-t használja az itt bemutatott felhasználói felületén.  Ha az eszköz nem tudja mutatni a WebView felhasználói Felületet, más hitelesítési módszert van szükség.  Ez az SDK így nem alkalmas Watch-típusú vagy hasonló módon korlátozott eszközöket.

## <a name="setup"></a>A telepítő és Előfeltételek
Feltételezzük, hogy már létrehozott és közzétett mobilalkalmazás háttérprojekt, amely legalább egy olyan táblát tartalmaz.  Ebben a témakörben használt kódot, a tábla neve `TodoItem` és rendelkezik a következő oszlopokat: `Id`, `Text`, és `Complete`. Ez a tábla ugyanazon a táblán során a [Azure Mobile Apps – első lépések][1].

A megfelelő típusos ügyféloldali írja be a C#-ban a következő osztályok:

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

A [JsonPropertyAttribute] [ 6] meghatározására szolgál a *PropertyName* az ügyfél és a tábla mező közötti megfeleltetés.

Megtudhatja, hogyan hozhat létre táblák a Mobile Apps-háttéralkalmazást, tekintse meg a [.NET Server SDK témakörben] [ 7] vagy a [Node.js Server SDK témakörben][8]. Mobile Apps-háttéralkalmazását az Azure Portalon a rövid útmutató segítségével létrehozott, ha akkor is használhatja a **könnyen kezelhető táblák** beállítását a [az Azure portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Útmutató: Telepítse a felügyelt ügyfél SDK-csomagot
A felügyelt ügyfél SDK-csomag telepítéséhez a Mobile Apps-alkalmazáshoz az alábbi módszerek valamelyikével [NuGet][9]:

* **A Visual Studio** kattintson jobb gombbal a projektre, kattintson a **NuGet-csomagok kezelése**, keresse meg a `Microsoft.Azure.Mobile.Client` csomagot, majd kattintson a **telepítése**.
* **A Xamarin Studio** kattintson jobb gombbal a projektre, kattintson a **Hozzáadás** > **NuGet-csomagok hozzáadása**, keresse meg a `Microsoft.Azure.Mobile.Client` csomagot, és kattintson a **csomag hozzáadása** .

A fő tevékenységi fájlhoz a ne felejtse el hozzáadni a következő **használatával** utasítást:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Vegye figyelembe, hogy az Android-projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az SDK-val rendelkezik `Xamarin.Android.Support.CustomTabs` függőséget az Android platformhoz, így ha a projekt újabb használja a támogatási csomagok, a csomag telepítéséhez szükséges verziójú ütközések elkerülése érdekében közvetlenül kell.

### <a name="symbolsource"></a>kézikönyv: A Visual Studio hibakeresési szimbólumok használata
A szimbólumok Microsoft.Azure.Mobile névtérhez érhetők el a [SymbolSource][10].  Tekintse meg a [SymbolSource utasításokat] [ 11] SymbolSource integrálhatja a Visual Studio használatával.

## <a name="create-client"></a>A Mobile Apps-ügyfél létrehozása
Az alábbi kód létrehoz a [MobileServiceClient] [ 12] objektum, amely használható a Mobile Apps-háttéralkalmazás eléréséhez.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Cserélje le a fenti kóddal, `MOBILE_APP_URL` a Mobile Apps-háttéralkalmazás URL-címmel, amely megtalálható a Mobile Apps-háttéralkalmazását paneljén a [az Azure portal]. A MobileServiceClient egypéldányos kell lennie.

## <a name="work-with-tables"></a>Táblázatok használata
Az alábbi szakasz részletesen keresés és a rekordok és módosíthatja az adatokat a táblán belül.  A következő témákat tárgyalja:

* [Hozzon létre egy táblahivatkozás](#instantiating)
* [Adatok lekérdezése](#querying)
* [Visszaadott adatok szűrése](#filtering)
* [Visszaadott adatok rendezése](#sorting)
* [Az oldalak adatokat adja vissza](#paging)
* [Egyes oszlopok kiválasztásához](#selecting)
* [Kereshet meg egy rekord azonosítója](#lookingup)
* [A típus nélküli lekérdezések kezelése](#untypedqueries)
* [Adatok beszúrása](#inserting)
* Adatok frissítése
* [Adatok törlése](#deleting)
* [Ütközésfeloldás és az optimista egyidejűség](#optimisticconcurrency)
* [Egy Windows felhasználói felület kötést](#binding)
* [Az oldalméret módosítása](#pagesize)

### <a name="instantiating"></a>kézikönyv: Hozzon létre egy táblahivatkozás
A kódot, amely hozzáfér, vagy módosítja a háttér-táblákban tárolt adatokat a meghívja a függvényt a `MobileServiceTable` objektum. Szerezzen be egy hivatkozást a tábla meghívásával a [GetTable] módszert az alábbiak szerint:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

A visszaadott objektum típusos szerializálási modellt használja. Egy nem típusos szerializálási modell használata is támogatott. Az alábbi példa [létrehoz egy hivatkozást a típus nélküli táblához]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

A típus nélküli lekérdezésekben meg kell adnia a mögöttes OData-lekérdezési karakterláncot.

### <a name="querying"></a>kézikönyv: A Mobile App adatok lekérdezése
Ez a szakasz ismerteti, hogyan küldhet lekérdezéseket a Mobile Apps-háttéralkalmazás, amely a következő funkciókat tartalmazza:

* [Visszaadott adatok szűrése](#filtering)
* [Visszaadott adatok rendezése](#sorting)
* [Az oldalak adatokat adja vissza](#paging)
* [Egyes oszlopok kiválasztásához](#selecting)
* [Azonosító alapján adatokat kereshet](#lookingup)

> [!NOTE]
> Megakadályozza, hogy az összes sor visszaadott kiszolgálóvezérelt oldalméret lép érvénybe.  Lapozófájl biztosítja, hogy a nagy adatkészleteknél alapértelmezett kérelmek negatív hatással a szolgáltatás.  Több mint 50 sorból használja a `Skip` és `Take` metódus, leírtak szerint [adatokat adja vissza az oldalak](#paging).

### <a name="filtering"></a>kézikönyv: Visszaadott adatok szűrése
Az alábbi kód bemutatja, hogyan szűrhet a adatok többek között egy `Where` záradék szerepel a lekérdezésben. Az összes elemet visszaadja `todoTable` amelynek `Complete` tulajdonság értéke `false`. A [ahol] függvény szűrése a lekérdezés a táblázaton predikátum sor vonatkozik.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Megtekintheti a háttérrendszer üzenet ellenőrzési szoftverek, például a böngésző fejlesztői eszközök használatával küldött kérelem URI azonosítója vagy [Fiddler]. Ha megtekinti a kérés URI azonosítója, figyelje meg, hogy a lekérdezési karakterláncban van-e módosítva:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Az OData-kérés a kiszolgáló SDK fordíthatók be egy SQL-lekérdezést:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A függvényt, amely az átadott a `Where` metódus feltételek tetszőleges számú is rendelkezhet.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Ebben a példában lenne fordítható egy SQL-lekérdezést, a kiszolgáló SDK-ban:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Ez a lekérdezés több záradékot is osztható:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

A két módszer egyenértékűek, és azonos értelemben használják.  A korábbi beállítás&mdash;, egy lekérdezés több predikátumok összetűzésének&mdash;tömörebb és ajánlott.

A `Where` záradékban kell műveleteket támogatja az OData részét fordítják. Műveletek a következők:

* Relációs operátorokat (==,! =, <, < =, >, > =),
* Aritmetikai operátor (+, -, /, *, %),
* A pontosság (Math.Floor, Math.Ceiling) száma
* Karakterlánc-függvények (hossza, Karakterláncrészletet, csere, IndexOf, StartsWith, EndsWith),
* Tulajdonságok (év, hónap, nap, óra, perc, másodperc), dátum
* Az objektum tulajdonságai érhetők el, és
* Ezek a műveletek bármelyike kombinálásával kifejezéseket.

Amikor kiválasztja a Server SDK támogatja, érdemes lehet a [OData v3 dokumentáció].

### <a name="sorting"></a>kézikönyv: Visszaadott adatok rendezése
Az alábbi kód bemutatja, hogyan rendezze az adatokat fel egy [OrderBy] vagy [OrderByDescending] függvényt a lekérdezésben. Szereplő elemeket adja vissza `todoTable` szerint növekvő sorrendbe rendezve a `Text` mező.

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

### <a name="paging"></a>kézikönyv: Az oldalak adatokat adja vissza
Alapértelmezés szerint a háttérrendszer csak az első 50 sort adja vissza. A visszaadott sorok száma meghívásával növelheti a [igénybe] metódus. Használja `Take` együtt a [kihagyása] módszer egy adott "lap" az összes adatkészlet a lekérdezés által visszaadott kéréséhez. A következő lekérdezés végrehajtásakor, az első három elemeket a táblát adja vissza.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Az alábbi javított lekérdezés kihagyja az első három eredményeket, és a következő három eredményeket ad vissza. Ez a lekérdezés a második "lap" az adatokat, ahol az oldal mérete három elemet hoz létre.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A [IncludeTotalCount] metódus kérelmek teljes számát *összes* a rekordokat szeretne által visszaadott, figyelmen kívül hagyása minden stránkování/limit záradék van megadva:

```csharp
query = query.IncludeTotalCount();
```

Egy valós alkalmazásban az előző példához hasonló lekérdezések használhatja stránkování vezérlőelem vagy hasonló felhasználói Felületet oldalai között.

> [!NOTE]
> A Mobile Apps-háttéralkalmazás-50 sorból korlát felülbírálása, is telepítenie kell a [EnableQueryAttribute] nyilvános GET metódus, és adja meg a lapozófájl viselkedését. Ha a módszert alkalmazza, a következő beállítja visszaadott sorok 1000 a legnagyobb:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>kézikönyv: Egyes oszlopok kiválasztásához
Megadhatja, amely tulajdonságainak beállítása való hozzáadásával a keresési eredmények között szerepeljen egy [kiválasztása] záradék a lekérdezés. Ha például a következő kód bemutatja, hogyan válassza ki egy mezőt, és válassza ki, és több mező formázása:

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

Az eddig leírt összes függvények additívak, így azt is folyamatosan láncolási őket. Egyes láncolt hívások hatással van a lekérdezés több. Egy további példa:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>kézikönyv: Azonosító alapján adatokat kereshet
A [LookupAsync] függvény használható objektumokat kereshet meg egy adott azonosítóval az adatbázisból

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>kézikönyv: A típus nélküli lekérdezések végrehajtása
A lekérdezés nem típusos tábla objektum használatával végrehajtásakor meg kell adnia az OData-lekérdezési karakterlánc meghívásával [ReadAsync], ahogy az alábbi példában:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Szerezheti vissza, amelyet használhat, például a tulajdonságcsomag JSON-értékeit. JToken és Newtonsoft Json.NET további információkért lásd: a [Json.NET] hely.

### <a name="inserting"></a>kézikönyv: Adatok beszúrása a Mobile Apps-háttéralkalmazás
Minden ügyfél típus tartalmaznia kell egy nevű tag **azonosító**, amely alapértelmezés szerint ki egy karakterláncot. Ez **azonosító** CRUD-műveletek végrehajtásához és a kapcsolat nélküli szinkronizálás. Az alábbi kód bemutatja, hogyan használható a [InsertAsync] módszer új sor beszúrásához a táblába. A paraméter tartalmazza a .NET-objektumként beszúrt adatok.

```csharp
await todoTable.InsertAsync(todoItem);
```

Ha egyéni egyedi azonosító érték nem szerepel a `todoItem` során végrehajtott beszúrási, egy GUID Azonosítót a kiszolgáló által generált.
Az objektum vizsgálatával, miután a hívás visszaadja a létrehozott azonosító kérheti le.

Típusos adatok beszúrása, előfordulhat, hogy előnyeit Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Íme egy példa az e-mail-cím, egy egyedi karakterlánc-azonosító:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Azonosító érték használata
A Mobile Apps egyedi egyéni karakterlánc-értékeket támogatja a tábla **azonosító** oszlop. Egy karakterláncértéket lehetővé teszi, hogy az alkalmazások általi használatát az egyéni értékek, például az e-mail-címeket vagy felhasználói neveket a azonosítóját.  Karakterlánc-azonosítók a következő előnyöket nyújtanak:

* Azonosítók jönnek létre, anélkül, hogy adatváltási az adatbázishoz.
* Rekordok használata egyszerűbb, egyesítheti a különböző táblák vagy adatbázisok.
* Azonosítók értékek jobban integrálható az alkalmazás logikáját.

Amikor egy karakterláncértéket azonosítója nem egy beszúrva rekord van beállítva, a Mobile Apps-háttéralkalmazás állít elő, egyedi értéket a azonosítóját. Használhatja a [Guid.NewGuid] metódus létrehozni a saját azonosító értékeit, az ügyfélen vagy a háttérben.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>kézikönyv: A Mobile Apps-háttéralkalmazás adatok módosítása
Az alábbi kód bemutatja, hogyan használható a [UpdateAsync] metódus egy létező rekord frissítése az új adatokkal ugyanazzal az azonosítóval. A paraméter tartalmazza a .NET-objektumként frissíteni kell az adatokat.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Típusos adatok frissítésére, előfordulhat, hogy előnyeit [Json.NET] módon:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Egy `id` mezőt meg kell adni, ha a frissítés. A háttéralkalmazás használja a `id` mező segítségével azonosítja, mely a frissítendő sor. A `id` mező eredménye lehet megszerezni a `InsertAsync` hívja. Egy `ArgumentException` jelenik meg, ha egy elem frissítése megadása nélkül próbál a `id` értéket.

### <a name="deleting"></a>kézikönyv: A Mobile Apps-háttéralkalmazás adatok törlése
Az alábbi kód bemutatja, hogyan használható a [DeleteAsync] metódus használatával törölheti a meglévő példányt. A példány által azonosított a `id` set mezője a `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Típusos adatok törléséhez előfordulhat, hogy előnyeit Json.NET módon:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

A törlési kérést, ha egy Azonosítóját meg kell adni. Egyéb tulajdonságok nem ad át a szolgáltatásnak, vagy a szolgáltatás figyelmen kívül hagyja. Eredménye egy `DeleteAsync` hívás van általában `null`. Adja át azonosító szerezhető eredményét a `InsertAsync` hívja. A `MobileServiceInvalidOperationException` fordul elő, ha egy elem törléséhez megadása nélkül próbál a `id` mező.

### <a name="optimisticconcurrency"></a>kézikönyv: Használjon optimista egyidejűséget, az ütközések feloldása
Két vagy több ügyfél is menti a módosításokat ugyanahhoz a cikkhez egy időben. Ütközésészlelési, nem az utolsó írás felülírja a korábbi frissítéseket. **Az optimista egyidejűség-vezérlés** feltételezi, hogy minden tranzakció kötelezettséget tudnak vállalni, és ezért nem használ minden olyan erőforrás zárolását.  A tranzakció véglegesítése, mielőtt az optimista egyidejűség-vezérlés ellenőrzi, hogy nincs másik tranzakció módosította-e az adatokat. Ha az adatok módosítva lett, a végrehajtása tranzakció vissza lesz állítva.

Mobile Apps támogatja az optimista egyidejűség-vezérlés által minden elem használatával változásainak követése a `version` rendszer tulajdonság oszlop, amely a Mobile Apps minden táblában van definiálva. Minden alkalommal, amikor egy rekord frissül, Mobile Apps beállítja a `version` tulajdonság a rekord az új értéket. Minden frissítési kérelem során a `version` tulajdonság a rekord tartalmazza a kérés a rendszer összehasonlítja a rekord a kiszolgáló ugyanahhoz a tulajdonsághoz. Az átadott verzió a kérelem nem felel meg a háttérkiszolgáló, akkor az ügyféloldali kódtár kiváltja a `MobileServicePreconditionFailedException<T>` kivétel. A mellékelt a kivétel típusa, a rekord a háttérbeli, amely tartalmazza a rekord a kiszolgálók verzióját. Az alkalmazás ezután használhatja ezt az információt eldönteni, hogy hajtsa végre a frissítési kérelmet újra a helyes-e `version` érték a háttérbeli véglegesíteni a módosításokat.

Adja meg egy olyan oszlop a tábla osztály a `version` rendszertulajdonság engedélyezéséhez az optimista egyidejűséget. Példa:

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

A típus nélküli táblák használata alkalmazások lehetővé teszik a optimista egyidejűséget, beállításával a `Version` jelölővel a `SystemProperties` a táblázat az alábbiak szerint.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Optimista párhuzamosság egyrészt meg kell is a tényleges a `MobileServicePreconditionFailedException<T>` kivétel a kódban hívásakor [UpdateAsync].  Oldja fel az ütközést úgy, hogy alkalmazza a megfelelő `version` a frissített rekord és a hívás [UpdateAsync] a feloldott bejegyzéshez. A következő kód bemutatja, hogyan egyszer észlelt írási ütközés feloldása:

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

További információkért lásd: a [Offline adatszinkronizálás az Azure Mobile Apps] témakör.

### <a name="binding"></a>kézikönyv: A Mobile Apps-adatok kötése egy Windows felhasználói felület
Ebben a szakaszban egy Windows-alkalmazás felhasználói felületi elemek használatával visszaadott adatok objektumokat mutatja be.  Az alábbi példakód egy lekérdezést a hiányos elemeket a lista forrása a van kötve. A [MobileServiceCollection] egy Mobile Apps-kompatibilis kötelező gyűjteményt hoz létre.

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

A felügyelt modul az egyes vezérlők támogat egy felületet, nevű [ISupportIncrementalLoading]. Ez az interfész lehetővé teszi, hogy a vezérlők további adatokat kér, amikor a felhasználó jobbra görget. Ez az interfész a univerzális Windows-alkalmazások beépített támogatott [MobileServiceIncrementalLoadingCollection], amely automatikusan kezeli a vezérlők hívásait. Használat `MobileServiceIncrementalLoadingCollection` a Windows-alkalmazások az alábbiak szerint:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Az új gyűjtemény a Windows Phone 8 és a "Silverlight" alkalmazások használatához a `ToCollection` a bővítő metódusokat `IMobileServiceTableQuery<T>` és `IMobileServiceTable<T>`. Az adatok betöltéséhez hívja `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

A hívó által létrehozott gyűjtemény használatakor `ToCollectionAsync` vagy `ToCollection`, kap egy gyűjteményt, amely a felhasználói felületi vezérlők köthető.  A lapozófájl-kompatibilis gyűjteménye.  Mivel a gyűjtemény adatok betöltése a hálózatról, néha betöltése sikertelen. Az ilyen hibáinak a kezelése, felülbírálja a `OnException` metódust `MobileServiceIncrementalLoadingCollection` hívásainak keletkezett kivételek kezeléséhez `LoadMoreItemsAsync`.

Fontolja meg, ha a táblának sok mező, de csak megjeleníteni kívánt némelyike Önön múlik. Az előző szakaszban útmutatást használhatja "[egyes oszlopok kiválasztásához](#selecting)" megjelenítése a felhasználói felület egyes oszlopok kiválasztásához.

### <a name="pagesize"></a>Az oldalméret módosítása
Az Azure Mobile Apps egy legfeljebb 50 elemet kérelmenként alapértelmezés szerint adja vissza.  A lapozófájl méretét az ügyfél és a kiszolgálón a maximális méretének növelésével módosíthatja.  Adja meg a kért méretének növeléséhez `PullOptions` használatakor `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Feltéve, hogy végzett a `PageSize` egyenlő vagy nagyobb, mint 100 belül a kiszolgáló, a kérelem legfeljebb 100 elemeket adja vissza.

## <a name="#offlinesync"></a>A kapcsolat nélküli táblázatok használata
Kapcsolat nélküli táblák egy helyi SQLite tárolására adatok használata a kapcsolat nélküli használatra.  Az összes tábla műveleteket kell elvégezni, szemben a helyi SQLite helyett a távoli kiszolgáló tárolójában tárolja.  Egy kapcsolat nélküli tábla létrehozásához, készítse elő a projekthez:

1. A Visual Studióban kattintson a jobb gombbal a megoldás > **NuGet-csomagok kezelése megoldáshoz...** , majd keresse meg, és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** a megoldásban az összes projekt NuGet-csomagot.
2. (Nem kötelező) Támogatja a Windows-eszközöket, telepítse az SQLite következő futtatókörnyezetek közül:

   * **Windows 8.1 Runtime:** Telepítés [for Windows 8.1 SQLite][3].
   * **Windows Phone 8.1:** Telepítés [a Windows Phone 8.1-es SQLite][4].
   * **Univerzális Windows Platform** telepítése [számára az univerzális Windows SQLite][5].
3. (Nem kötelező). Kattintson a Windows-eszközök esetén **hivatkozások** > **hivatkozás hozzáadása...** , bontsa ki a **Windows** mappa > **bővítmények**, majd engedélyezze a megfelelő **SQLite for Windows** SDK-val és a **Visual C++ 2013 Runtime Windows** SDK-t.
    Az SQLite SDK nevei kissé eltérő lehet az egyes Windows-platform.

A táblahivatkozást létrehozása előtt elő kell készíteni a helyi tárolóban:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Store inicializálási általában akkor történik meg, az ügyfél létrehozása után azonnal.  A **OfflineDbPath** a FileName paramétert használja, minden platformon, amely támogatja a megfelelő legyen.  Ha az elérési út teljes elérési útja (azaz kezdődik perjellel), majd, hogy az elérési utat használja.  Ha az elérési út nem teljes mértékben minősíti, van egy platformspecifikus helyen helyezi el a fájlt.

* Az iOS és Android-eszközök esetében az alapértelmezett elérési út a "Személyes Files" mappa.
* Windows-eszközök esetén az alapértelmezett elérési út az alkalmazás-specifikus "AppData" mappában.

A táblahivatkozást használatával is beszerezhetők a `GetSyncTable<>` módszer:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Nem kell egy offline tábla használja hitelesítésre.  Csak van szükség, amikor a háttérszolgáltatással folytatott történik a kommunikáció hitelesítéséhez.

### <a name="syncoffline"></a>Egy kapcsolat nélküli táblázat szinkronizálása
A kapcsolat nélküli táblák nem szinkronizálódnak a háttérrendszerrel alapértelmezés szerint.  Szinkronizálási kétféle információra van felosztva.  Gyakorisággal továbbíthat módosításokat külön-külön töltsön le új elemeket.  Íme egy tipikus szinkronizálási módszere:

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

Ha az első argumentum `PullAsync` null értékű, akkor nem használja a növekményes szinkronizálás.  Minden egyes szinkronizálási művelete lekéri az összes rekordot.

Az SDK-t hajt végre egy implicit `PushAsync()` rekordok beolvasás előtt.

Ütközés kezelése történik egy `PullAsync()` metódust.  Online táblákként ugyanúgy kezelhető, ütközéseket.  Az ütközés előállítása során `PullAsync()` neve helyett az insert, update vagy delete során. Ha több ütközések egybe vannak csomagolva, egy egyetlen MobileServicePushFailedException.  Minden egyes hiba külön-külön kezelni.

## <a name="#customapi"></a>Egyéni API-k használata
Egyéni API-k lehetővé teszi, hogy meghatározza az egyedi végpontok, amelyek teszik közzé a kiszolgálói funkciót, amely nem egy insert leképezése, frissítése, törlése vagy olvasási művelete. Egyéni API-k használatával rendelkezhet több felügyeleti üzenetkezelés, beleértve olvasása és HTTP-üzenet fejlécek beállítása és eltérő JSON üzenet törzse formátum meghatározása.

Egyéni API-k meghívásával egyik hívja a [InvokeApiAsync] módszerek az ügyfélen. Például a következő kódsort a POST-kérést küld a **completeAll** háttérbeli API:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Ezt az űrlapot a beírt metódus hívás, és ehhez meg kell adni a **MarkAllResult** vissza típus van meghatározva. Típusos és a nem típusos metódusok támogatottak.

A InvokeApiAsync() metódus lefoglalja az API-nak hívja, kivéve, ha az API-t kezdődik kívánt "/ api /" a "/"karaktert.
Példa:

* `InvokeApiAsync("completeAll",...)` a háttérkiszolgálón /api/completeAll hívások
* `InvokeApiAsync("/.auth/me",...)` a háttérkiszolgálón hívások /.auth/me

InvokeApiAsync segítségével bármely WebAPI, beleértve a nem meghatározott ezeket WebAPIs az Azure Mobile Apps hívja.  InvokeApiAsync() használata esetén a megfelelő fejlécek, beleértve a hitelesítési fejléceket, a kérelem lesznek elküldve.

## <a name="authentication"></a>Felhasználók hitelesítése
A Mobile Apps hitelesítése és engedélyezése az alkalmazás felhasználóinak különböző külső identitásszolgáltató használatával támogatja: Facebook, Google, Microsoft-fiókkal, Twitter, és az Azure Active Directoryban. Beállíthatja, hogy a engedélyeit azokon a táblákon, az adott műveletek csak a hitelesített felhasználók a hozzáférés korlátozásához. Az engedélyezési szabályok megvalósításához a kiszolgálóoldali parancsprogramok is használhatja a hitelesített felhasználók identitását. További információt a [Hitelesítés hozzáadása az alkalmazáshoz] ismertető oktatóanyagban találhat.

Két hitelesítési folyamatok támogatottak: *ügyfél által felügyelt* és *kiszolgáló által kezelt* folyamat. A kiszolgáló által felügyelt folyamatot a legegyszerűbb felhasználói hitelesítés támaszkodik a szolgáltató webes hitelesítés felületet nyújt. Az ügyfél által felügyelt folyamatot lehetővé teszi eszközspecifikus funkciók mélyebb integrációjuk, a szolgáltatóhoz tartozó eszközspecifikus SDK-k támaszkodik.

> [!NOTE]
> Azt javasoljuk, hogy egy ügyfél által felügyelt folyamat segítségével az éles alkalmazásokban.

Állítsa be a hitelesítést, regisztrálnia kell az alkalmazás egy vagy több identitásszolgáltatókkal.  Az identitásszolgáltató állít elő, ügyfél-Azonosítót és egy ügyfél az alkalmazás titkos kulcsát.  Ezeket az értékeket a háttérszolgáltatásban majd állítsa a engedélyezése Azure App Service hitelesítés/engedélyezés.  További információkért kövesse a részletes utasításokat az oktatóanyag [Hitelesítés hozzáadása az alkalmazáshoz].

Ebben a szakaszban a következő témákat tárgyalja:

* [Hitelesítési ügyfél által felügyelt](#clientflow)
* [Hitelesítési kiszolgáló által felügyelt](#serverflow)
* [A hitelesítési jogkivonat gyorsítótárazás](#caching)

### <a name="clientflow"></a>Hitelesítési ügyfél által felügyelt
Az alkalmazás is egymástól függetlenül kapcsolatba léphet az identitásszolgáltatóval, és ezután átadhatja a visszakapott tokent bejelentkezés során a háttérszolgáltatás használatára. Ez a client flow lehetővé teszi, hogy adja meg az egyszeri bejelentkezés használatát a felhasználók számára, vagy további felhasználói adatokat lekérni az identitásszolgáltató. Ügyfél folyamat hitelesítés az előnyben részesített kiszolgáló folyamat használata az identitásszolgáltató SDK biztosít egy több natív UX működésével, és lehetővé teszi, hogy további testreszabási.

Példák a következő folyamat-ügyfél hitelesítési minták áll rendelkezésre:

* [Az Active Directory hitelesítési Kódtárával](#adal)
* [Facebook- vagy Google](#client-facebook)

#### <a name="adal"></a>Felhasználók hitelesítése az Active Directory Authentication Library az
Kezdeményezési felhasználói hitelesítést az Active Directory Authentication Library (ADAL) segítségével az ügyfél az Azure Active Directory-hitelesítés használatával.

1. A mobil-háttéralkalmazás az AAD bejelentkezés konfigurálása a következő a [konfigurálása App Service-ben az Active Directory-bejelentkezés] oktatóanyag. Ellenőrizze, hogy a natív ügyfélalkalmazás regisztrációja nem kötelező lépése.
2. A Visual Studióban vagy a Xamarin Studióban nyissa meg a projektet, és vegyen fel egy hivatkozást a `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet-csomagot. Ha keres, kiadás előtti verziót tartalmaz.
3. Adja hozzá a következő kódot az alkalmazását, használja a platformtól függően. Az egyes győződjön meg a következő cserét:

   * Cserélje le **INSERT-SZOLGÁLTATÓ – Itt** , amelyben az alkalmazás kiosztása a bérlő nevével. A formátum https://login.microsoftonline.com/contoso.onmicrosoft.com. Ez az érték lehet másolni az Azure Active Directory tartományi lapról a [az Azure portal].
   * Cserélje le **INSERT-erőforrás-azonosító – Itt** az ügyfél-Azonosítóját a mobile Apps-háttéralkalmazást. Az ügyfél-Azonosítót a szerezheti be a **speciális** lapjára **Azure Active Directory-beállítások** a portálon.
   * Cserélje le **INSERT-ügyfél-azonosító – Itt** és az ügyfél-Azonosítót a natív ügyfélalkalmazás fájlból kimásolt.
   * Cserélje le **INSERT-REDIRECT-URI-Itt** a hellyel */.auth/login/done* végpontról, a HTTPS-sémát. Ez az érték legyen hasonló *https://contoso.azurewebsites.net/.auth/login/done*.

     Az egyes platformokra vonatkozó szükséges kódot a következőképpen:

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

#### <a name="client-facebook"></a>Egyszeri bejelentkezés Facebook- vagy Google származó jogkivonat használatával
A client flow is használhat, a Facebook vagy a Google ebben a kódrészletben látható módon.

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

### <a name="serverflow"></a>Hitelesítési kiszolgáló által felügyelt
Ha már regisztrálta az identitásszolgáltatót, hívja a [LoginAsync] [MobileServiceClient] a metódust a [MobileServiceAuthenticationProvider] érték-szolgáltató. A következő kód például a server flow bejelentkezési kezdeményezi, Facebook-fiókkal.

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

Eltérő Facebook identitásszolgáltatót használja, ha értékének módosítása [MobileServiceAuthenticationProvider] értékét az a szolgáltatótól.

A server flow-ban Azure App Service felügyeli az OAuth hitelesítési folyamatot megjeleníti a kiválasztott szolgáltató bejelentkezési oldal.  Ha az identitás szolgáltató értéket ad vissza, az Azure App Service-ben létrehoz egy App Service hitelesítési tokent. A [LoginAsync] metódus adja vissza egy [MobileServiceUser], is segíti a [UserId] hitelesített felhasználó és a [ MobileServiceAuthenticationToken], mint egy JSON webes jogkivonat (JWT). Ez a token gyorsítótárazható, és újra felhasználható, amíg le nem jár. További információkért lásd: [gyorsítótárazás a hitelesítési jogkivonat](#caching).

### <a name="caching"></a>A hitelesítési jogkivonat gyorsítótárazás
Bizonyos esetekben a bejelentkezési metódus hívása elkerülhető az első sikeres hitelesítés után a hitelesítési jogkivonat-szolgáltatójáról való tárolásával.  Microsoft Store-és UWP-alkalmazások használhassák- [PasswordVault] a jelenlegi hitelesítési jogkivonat gyorsítótárazni a sikeres bejelentkezést követően, a következő:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

A felhasználói azonosító értékét tárolja felhasználóneve a hitelesítő adatainak és a jogkivonat a tárolt jelszót. Az ezt követő induló vállalkozásoknak, ellenőrizheti a **PasswordVault** a gyorsítótárazott hitelesítő adatokat. Az alábbi példában gyorsítótárazott hitelesítő adatok találhatók, és egyéb próbál meg újra a háttérszolgáltatás használatára:

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

Amikor egy felhasználó kijelentkezik, el kell távolítania is a tárolt hitelesítő adatokat, a következő:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-alkalmazások használatát a [Xamarin.Auth] API-kat biztonságosan a hitelesítő adatokat tárolni egy **fiók** objektum. Ezen API-k használatának példájáért lásd a [AuthStore.cs] a kódfájl a [ContosoMoments fényképmegosztó minta](https://github.com/azure-appservice-samples/ContosoMoments).

Ha ügyfél által felügyelt hitelesítés használata esetén is képes gyorsítótárazni például Facebook vagy a Twitter szolgáltatótól kapott hozzáférési jogkivonat. Ezt a jogkivonatot lehet adni egy új hitelesítési jogkivonatot kérhet a háttérrendszer a következő:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Leküldéses értesítések küldése
A következő témakörök a leküldéses értesítések terjed ki:

* [A leküldéses értesítések regisztrálása](#register-for-push)
* [Szerezze be a Microsoft Store csomag biztonsági azonosítója](#package-sid)
* [Regisztrálás a platformok közötti átjárhatóságról sablonok](#register-xplat)

### <a name="register-for-push"></a>kézikönyv: A leküldéses értesítések regisztrálása
A Mobile Apps-ügyfél lehetővé teszi az Azure Notification Hubs leküldéses értesítések regisztrálása. Amikor regisztrál, be kell szereznie egy leírót, hogy az a platform-specifikus leküldéses értesítési szolgáltatás (PNS). Ez az érték minden címkékkel együtt, majd adja meg, a regisztráció létrehozásakor. Az alábbi kód regisztrálja a Windows-alkalmazást a leküldéses értesítésekre a Windows értesítési szolgáltatásának (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Wns küld, akkor a következőket kell TENNIE [szerezze be a Microsoft Store csomag biztonsági azonosítója](#package-sid).  További információ a Windows-alkalmazásokra, hogyan kell regisztrálni a sablon regisztrációhoz, beleértve: [Leküldéses értesítések hozzáadása az alkalmazáshoz].

A címkék kér az ügyfél nem támogatott.  Címke kérelmek csendes regisztrációs a rendszer eldobja.
Ha az eszköz regisztrálása a címkéket, hozzon létre egy egyéni API-t, amely a Notification Hubs API-t használja a regisztráció végrehajtásához az Ön nevében.  Az egyéni API-t helyett a `RegisterNativeAsync()` metódust.

### <a name="package-sid"></a>kézikönyv: Szerezze be a Microsoft Store csomag biztonsági azonosítója
A Microsoft Store-alkalmazások leküldéses értesítések engedélyezése szükség van a csomag biztonsági azonosítója.  Ha szeretne kapni a csomag biztonsági azonosítója, regisztrálhatja alkalmazását a Microsoft Store.

Ez az érték beszerzése:

1. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a Microsoft Store-projektet, kattintson a **Store** > **a Store alkalmazás hozzárendelése...** .
2. A varázslóban kattintson **tovább**, jelentkezzen be Microsoft-fiókjával, adjon meg egy nevet az alkalmazáshoz tartozó **foglaljon le egy új nevet az alkalmazáshoz**, majd kattintson a **tartalék**.
3. Az alkalmazásregisztráció sikeres létrehozása után válassza ki az alkalmazás nevét, kattintson a **tovább**, és kattintson a **társítása**.
4. Jelentkezzen be a [Windows Dev Center] Microsoft Account. A **saját alkalmazások**, kattintson a létrehozott alkalmazás regisztrációját.
5. Kattintson a **felügyeleti** > **alkalmazás identitását**, majd görgessen lefelé, keresse meg a **csomag biztonsági azonosítója**.

A csomag biztonsági azonosítója számos felhasználási tekinti, egy URI-t, ebben az esetben kell használnia *ms-app: / /* sémát. Jegyezze meg a csomag biztonsági azonosítója, elkülönített változó összefűzésével előállítjuk ezt az értéket előtagjaként megfelelő verzióját.

Xamarin-alkalmazások tudni regisztrálni egy alkalmazást az iOS vagy Android-platformokon futó további kódot igényel. További információkért lásd a témakör a platformhoz:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>kézikönyv: Regisztráció leküldéses sablonok platformfüggetlen értesítések küldése
Sablonok regisztrálásához használja a `RegisterAsync()` metódust a sablonokat, az alábbiak szerint:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

A sablonok kell `JObject` típusait, és tartalmazhat több sablon a következő JSON formátumban:

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

A metódus **RegisterAsync()** másodlagos Csempék is fogad:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Az összes címke program azonnal eltávolítja a biztonság a regisztráció során. Címkék hozzáadása telepítések vagy sablonok telepítések belül, lásd: [a használata a .NET háttérkiszolgáló-SDK az Azure Mobile Apps].

A regisztrált sablonok használatával értesítéseket küldeni, tekintse meg a [Notification Hubs API-k].

## <a name="misc"></a>Egyéb témakörök
### <a name="errors"></a>kézikönyv: Hibakezelés
Ha hiba történik a háttérben, az ügyfél SDK-t kiváltja a `MobileServiceInvalidOperationException`.  Az alábbi példa bemutatja, hogyan kezelje a háttérrendszer által visszaadott kivétel:

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

Egy másik példa a hibaállapotok többé vesződnie a sérült megtalálható a [Mobile Apps fájlok minta]. A [LoggingHandler] példa biztosítja a naplózás delegált kezelő naplózni a háttérben történik.

### <a name="headers"></a>kézikönyv: Kérelemfejlécek testreszabása
Az adott forgatókönyv támogatásához, szüksége lehet testre szabhatja a Mobile Apps-háttéralkalmazás folytatott kommunikáció. Például érdemes hozzáadni egy egyéni fejlécet minden egyes kimenő kérelmekre, vagy is módosíthatja a válaszok állapotkódok. Használhat egyéni [DelegatingHandler], ahogy az alábbi példában:

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
[App Service-ben az Active Directory-bejelentkezés konfigurálása]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[létrehoz egy hivatkozást a típus nélküli táblához]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[hajtsa végre a megfelelő]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Válassza ezt:]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Kihagyás]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Felhasználói azonosító]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Ahol]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API-k]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[A Mobile Apps fájlok minta]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentációja]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
