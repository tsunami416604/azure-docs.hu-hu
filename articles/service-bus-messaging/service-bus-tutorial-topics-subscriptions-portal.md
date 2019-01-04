---
title: Oktatóanyag – Kereskedelmi leltárválaszték frissítése közzétételi/előfizetési csatornák és témakörszűrők használatával az Azure Portalon | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan küldhet és fogadhat témakörökhöz és előfizetésekhez tartozó üzeneteket, és hogyan adhat hozzá szűrési szabályokat a .NET használatával
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: fb3358775881f102ecea62fbd20a1e4d85dda308
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001634"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Oktatóanyag: Az Azure Portallal és az üzenettémák, előfizetések készlet frissítése

A Microsoft Azure Service Bus egy több-bérlős felhőalapú üzenetkezelési szolgáltatás, amely információkat küld alkalmazások és szolgáltatók között. Az aszinkron műveletek rugalmas, közvetítőalapú üzenettovábbítást, valamint strukturált, érkezési sorrendben történő üzenetkiküldést tesznek lehetővé. Emellett közzétételi vagy előfizetési lehetőségeket is biztosítanak. Ebből az oktatóanyagból megtudhatja, hogyan használhatók a Service Bus-témakörök és -előfizetések kereskedelmi leltárazási forgatókönyvekben az Azure Portalt és a .NET-et használó közzétételi/feliratkozási csatornákkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Erre a forgatókönyvre egy példa, amikor több kiskereskedelmi üzletben frissül a leltárválaszték. Ebben az esetben minden egyes üzlet vagy üzletcsoport megkapja a neki szánt üzeneteket a választéka frissítéséről. Ez az oktatóanyag bemutatja, hogyan valósítható meg a forgatókönyv előfizetések és szűrők használatával. Először hozzon létre egy témakört 3 előfizetéssel, adjon hozzá szabályokat és szűrőket, majd küldjön és fogadjon üzeneteket a témaköröktől és az előfizetésektől.

![témakör](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot][].

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-témakörök és -előfizetések

Minden egyes [témakörre való előfizetés](service-bus-messaging-overview.md#topics) másolatot kaphat az összes üzenetről. A témakörök teljes protokoll és szemantika szempontjából is teljesen kompatibilisek a Service Bus-üzenetsorokkal. A Service Bus-témakörök számos különféle kiválasztási szabályt támogatnak szűrőfeltételekkel, illetve az üzenet tulajdonságait beállító vagy módosító választható műveletekkel. Minden szabályegyezéskor létrejön egy üzenet. A szabályokról, szűrőkről és műveletekről ezen a [hivatkozáson](topic-filters.md) talál további információt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Először nyissa meg az [Azure Portalt][Azure portal], és Azure-előfizetésének használatával jelentkezzen be. Az első lépés egy **Üzenetkezelés** típusú Service Bus-névtér létrehozása.

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

A Service Bus üzenetküldési névtere egyedi hatókörkezelési tárolót biztosít, amelyre a [teljes tartománynevével][] lehet hivatkozni, és ebben a tárolóban egy vagy több üzenetsort, témakört és előfizetést is létrehozhat. Az alábbi példa egy Service Bus-üzenetküldési névteret hoz létre egy új vagy meglévő [erőforráscsoportban](/azure/azure-resource-manager/resource-group-portal):

1. A portál bal oldali navigációs paneljén kattintson a **+ Erőforrás létrehozása**, a **Vállalati integráció**, majd a **Service Bus** elemre.
2. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
3. Miután meggyőződött arról, hogy a névtér neve elérhető, válassza ki a tarifacsomagot (Standard vagy Prémium).
4. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
5. Az **Erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, amelynek a névtér a része lesz, vagy hozzon létre egy újat.      
6. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
7. Kattintson a **Create** (Létrehozás) gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

  ![névtér](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>A felügyeleti hitelesítő adatok beszerzése

Egy új névtér létrehozásával automatikusan létrejön egy kezdeti közös hozzáférésű jogosultságkódra (SAS) vonatkozó szabály egy elsődleges és egy másodlagos kulcsból álló kulcspárral, amelyek mindegyike teljes hozzáférést biztosít a névtér minden területéhez. A kezdeti szabály másolásához kövesse az alábbi lépéseket:

1. Kattintson az **Összes erőforrás** elemre, majd az újonnan létrehozott névtér nevére.
2. A névtér ablakában kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** képernyőn kattintson a **RootManageSharedAccessKey** elemre.
4. Az a **házirend: RootManageSharedAccessKey** ablakban kattintson a **másolási** megjelenítő gombra **elsődleges kapcsolati karakterlánc**, hogy másolja a kapcsolati karakterláncot a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.

    ![connection-string][connection-string]
5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.

## <a name="create-a-topic-and-subscriptions"></a>Témakör és előfizetések létrehozása

Adja meg a névteret, amelyben létre kívánja hozni a Service Bus-témakört. Az alábbi példa bemutatja, hogyan hozhat létre témakört a portálon:

1. A portál bal oldali navigációs panelén kattintson a **Service Bus** (ha a **Service Bus** nem látható, akkor a **Minden szolgáltatás**) elemre.
2. Kattintson a névtérre, amelyben a témakört létre kívánja hozni.
3. A névtér ablakában kattintson a **Témakörök** elemre, majd a **Témakörök** ablakban kattintson a **+ Témakörök** elemre.
4. Adjon meg egy nevet a témakör **Név** mezőjében, a többi alapértelmezett értéket pedig ne módosítsa.
5. Az ablak alján kattintson a **Létrehozás** elemre.
6. Jegyezze fel a témakör nevét.
7. Válassza ki az imént létrehozott témakört.
8. Kattintson a **+ Előfizetés** elemre, adja meg az **S1** előfizetésnevet, a többi alapértelmezett értéket pedig ne módosítsa.
9. Ismételje meg az előző lépést kétszer, és hozza létre az **S2** és **S3** nevű előfizetéseket.

## <a name="create-filter-rules-on-subscriptions"></a>Szűrési szabályok létrehozása előfizetésekhez

Miután kiépítette a névteret és a témakört/előfizetéseket, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az előfizetések szűrési szabályainak létrehozására, majd az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) vizsgálhatja meg.

### <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

A kód futtatásához tegye a következőt:

1. Egy parancssorban vagy PowerShell-parancssorban klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Lépjen a következő mintamappához: `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Szerezze be a kapcsolati sztringet, amelyet a jelen oktatóanyag [A felügyeleti hitelesítő adatok beszerzése](#obtain-the-management-credentials) című szakaszában írtaknak megfelelően a Jegyzettömbbe másolt. Szüksége lesz továbbá az előző szakaszban létrehozott témakör nevére.

4. Írja be a következő parancsot a parancssorba:

   ```shell
   dotnet build
   ```

5. Navigáljon a `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0` mappához.

6. Írja be a következő parancsot a program futtatásához. Ne felejtse el kicserélni a `myConnectionString` kifejezést az előzőleg beszerzett értékre, a `myTopicName` kifejezést pedig az imént létrehozott témakör nevére:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Kövesse az utasításokat a konzolban, és először válassza ki a szűrő létrehozását. A szűrők létrehozásához az alapértelmezett szűrők eltávolítása is hozzátartozik. Ha PowerShellt vagy parancssori felületet használ, nem kell eltávolítania az alapértelmezett szűrőket, de ha kódot használ, ez mindenképpen szükséges. Az 1. és a 3. konzolparancs segítségével kezelheti a korábban létrehozott előfizetésekhez tartozó szűrőket:

   - Végrehajtás 1: az alapértelmezett szűrők eltávolítása.
   - Végrehajtás 2: saját szűrők hozzáadása.
   - Végrehajtás 3: igény szerint a saját szűrők eltávolítása. Vegye figyelembe, hogy az alapértelmezett szűrők így nem jönnek létre újra.

    ![2 kimenetének megjelenítése](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. A szűrők létrehozása után üzeneteket küldhet. Nyomja le a 4-es billentyűt, és figyelje meg 10 üzenetet küldését a témakörnek:

    ![Kimenet küldése](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Nyomja le az 5-ös billentyűt, és figyelje meg az üzenetek fogadását. Ha nem kapott 10 db üzenetet, nyomja le az „m” billentyűt a menü megjelenítéséhez, majd nyomja újból az 5-ös billentyűt.

    ![Kimenet fogadása](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a névteret és az üzenetsort. Ehhez válassza ki az erőforrásokat a portálon, majd kattintson a **Törlés** elemre.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti.

### <a name="get-connection-string-and-topic"></a>Kapcsolati sztring és témakör lekérése

A kód először meghatározza azokat a változókat, amelyek a program további végrehajtását vezérlik.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

A kapcsolati sztring és a témakör nevét a rendszer az alább látható módon parancssori paraméterekkel továbbítja, majd beolvassa a `Main()` metódusba:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Alapértelmezett szűrők eltávolítása

Amikor előfizetést hoz létre, a Service Bus létrehozza a hozzá tartozó alapértelmezett szűrőt. Ez a szűrő lehetővé teszi a témakörbe küldött összes üzenet fogadását. Ha egyéni szűrőket kíván használni, az alábbi kódban látható módon eltávolíthatja az alapértelmezett szűrőt:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Szűrők létrehozása

Az alábbi kóddal a jelen oktatóanyagban definiált egyéni szűrőket adhatja hozzá:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Egyénileg létrehozott szűrők eltávolítása

Ha az előfizetéséhez tartozó összes szűrőt el kívánja távolítani, ezt a következő kóddal teheti meg:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Üzenetek küldése

A témakörbe történő üzenetküldés hasonló az üzenetsorba való üzenetküldéshez. Ez a példa azt mutatja be, hogyan lehet üzeneteket küldeni egy feladatlista és aszinkron feldolgozás használatával:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenetek fogadása szintén feladatlistával történik, és a kód kötegelést használ. Kötegeléssel küldhet és fogadhat is üzeneteket, de ez a példa csak a kötegelt üzenetfogadást mutatja be. A valóságban azonban a megszakítás helyett érdemes megtartani a ciklust, és megadni egy nagyobb időtartamot, például egy percet. A közvetítő fogadási meghívása ennyi ideig marad nyitva. Ha üzenet érkezik, a rendszer azonnal visszaadja, és új fogadási hívást kezdeményez. Ezt a koncepciót *hosszú lekérdezésnek* nevezik. A fogadópumpa, amely a [rövid útmutatóban](service-bus-quickstart-portal.md) és az adattár számos további mintájában is látható, jellemzőbb megoldás.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban erőforrásokat osztott ki az Azure Portal használatával, majd üzeneteket küldött és fogadott egy Service Bus-témakörtől és a hozzá tartozó előfizetésektől. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Az üzenetküldéssel és -fogadással kapcsolatos további példákért tekintse meg a [Service Bus-mintákat a GitHubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Folytassa a következő oktatóanyaggal, ha szeretne többet megtudni a Service Bus közzétételi/előfizetési funkcióiról.

> [!div class="nextstepaction"]
> [Leltár frissítése a PowerShell és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-powershell.md)

[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[teljes tartománynevével]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
