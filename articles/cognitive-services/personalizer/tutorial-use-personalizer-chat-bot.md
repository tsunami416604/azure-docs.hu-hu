---
title: Személyre szabás használata a csevegési robotban – személyre szabás
description: Testreszabhatja a C# .NET chat robotot egy személyre szabott hurok használatával, hogy a megfelelő tartalmat biztosítsa a felhasználóknak a műveletek (funkciók) és a környezeti funkciók alapján.
ms.topic: tutorial
ms.date: 07/17/2020
ms.openlocfilehash: 8e936b4017ad50434bc7d6b05b4217f82a9080e0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131667"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Oktatóanyag: személyre szabott használata a .NET-csevegési robotban

Használjon egy C# .NET chat-robotot egy személyre szabott hurok használatával a megfelelő tartalom biztosításához a felhasználó számára. Ez a csevegési robot egy adott kávét vagy teát javasol egy felhasználónak. A felhasználó elfogadhatja vagy elutasíthatja ezt a javaslatot. Ez a személyre szabott információkat biztosít a következő javaslat megfelelőbbvé tételéhez.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Az Azure-erőforrások beállítása
> * A bot konfigurálása és futtatása
> * A robottal való interakció a robot Emulator használatával
> * Annak megismerése, hogy a robot hogyan használja a személyre szabott


## <a name="how-does-the-chat-bot-work"></a>Hogyan működik a csevegési robot?

A csevegési robot általában egy, a felhasználóval folytatott háttérbeli beszélgetés. Ez a speciális csevegési bot személyre szabottan kiválasztja a legmegfelelőbb műveletet (kávé vagy tea) a felhasználó felkínálása érdekében. A személyre szabás a megerősítő tanulást használja a kijelölés elvégzéséhez.

A csevegési robotnak kezelnie kell a beszélgetést. A chat bot a [bot Framework](https://github.com/microsoft/botframework-sdk) használatával felügyeli a robot-architektúrát és-beszélgetést, és a kognitív szolgáltatást, [Language Understanding](../LUIS/index.yml) (Luis) használja a természetes nyelv szándékának megismerésére a felhasználótól.

A csevegési bot egy olyan webhely, amely egy adott útvonalon érhető el a kérelmek megválaszolásához `http://localhost:3978/api/messages` . A bot Emulator használatával vizuálisan kommunikálhat a futó csevegési robottal, miközben a robotokat helyileg fejleszti.

### <a name="user-interactions-with-the-bot"></a>Felhasználói interakció a robottal

Ez egy egyszerű csevegési robot, amely lehetővé teszi szöveges lekérdezések megadását.

|A felhasználó szöveget ír be|A robot válaszol a szövegre|A műveleti robot leírása a válasz szövegének meghatározásához|
|--|--|--|
|Nincs megadva szöveg – a robot megkezdi a beszélgetést.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|A robot tanítási szöveggel kezdi a beszélgetést, és ismeri a kontextust: `Tuesday` , `Snowy` .|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Határozza meg a lekérdezés szándékát a LUIS használatával, majd jelenítse meg a kávé-és teafőző menüpontot. A műveletek funkciói |
|`What do you suggest`|`How about Latte?`|Határozza meg a lekérdezés célját a LUIS használatával, majd hívja meg a **Rank API**-t, és kérdezzen rá a legjobb választásra `How about {response.RewardActionId}?` . A JSON-hívást és a választ is megjeleníti illusztrációs célokra.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Állapítsa meg a lekérdezés célját a LUIS használatával, majd hívja meg a **jutalmazási API** -t a jutalmaval `1` , és jelenítse meg a JSON-hívást és az illusztrációs válaszokat.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Állapítsa meg a lekérdezés célját a LUIS használatával, majd hívja meg a **jutalmazási API** -t a jutalmaval `0` , és jelenítse meg a JSON-hívást és az illusztrációs válaszokat.|
|`Reset`|Az utasítás szövegét adja vissza.|Határozza meg a lekérdezés szándékát a LUIS használatával, majd megjeleníti az útmutatás szövegét, és visszaállítja a környezetet.|


### <a name="personalizer-in-this-bot"></a>Személyre szabás ebben a robotban

Ez a csevegési robot személyre szabottan választhatja ki a legfontosabb műveletet (adott kávé vagy tea) a _műveletek_ listája (bizonyos típusú tartalom) és a környezeti funkciók alapján.

A robot elküldi a műveletek listáját a helyi funkciókkal együtt a személyre szabott hurokhoz. A személyre szabás a legalkalmasabb műveletet adja vissza a robot számára, amelyet a robot megjelenít.

Ebben az oktatóanyagban a **műveletek** típusai kávé és tea:

|Kávét|Tea|
|--|--|
|Cappuccino<br>Presszókávé<br>Tejeskávé<br>Mocha|GreenTea<br>Rooibos|

**Rangsor API:** Ha segítségre van kíváncsi a személyre szabott műveletekről, a robot a következőt küldi el az egyes rank API-kérésekkel:

* _Funkciókkal rendelkező_ műveletek
* Környezeti funkciók

A modell egyik **funkciója** azon művelet vagy környezet információja, amely összesíthető (csoportosított) a csevegési robot felhasználói bázisának tagjai között. Egy szolgáltatás _nem_ egyedi (például felhasználói azonosító) vagy kifejezetten specifikus (például pontos napszak).

A funkciók a műveleteknek a modell aktuális környezetéhez való igazítására szolgálnak. A modell a személyre szabott korábbi ismeretek ábrázolása a műveletekről, a kontextusról és azok funkcióiról, amelyek lehetővé teszik az informatikai részleg számára a nevelésen alapuló döntések meghozatalát.

A modell, beleértve a szolgáltatásokat, a **modell frissítési gyakoriságának** beállítása alapján frissül a Azure Portalban.

A funkciókat azonos tervezéssel és kialakítással kell kiválasztani, amelyet a technikai architektúrában bármely sémára vagy modellre alkalmazni kellene. A szolgáltatás értékeit üzleti logikával vagy külső gyártótól származó rendszerekkel lehet beállítani.

> [!CAUTION]
> Az alkalmazás funkciói a bemutatóhoz szükségesek, és előfordulhat, hogy nem feltétlenül a legjobb funkciókat használják a webalkalmazásban a használati esetekhez.

#### <a name="action-features"></a>Műveleti funkciók

Minden művelet (tartalmi elem) olyan funkciókkal rendelkezik, amelyek segítenek megkülönböztetni a kávé-vagy teafőző-elemeket.

A funkciók nincsenek konfigurálva a hurok konfigurációjának részeként a Azure Portalban. Ehelyett JSON-objektumként lesznek elküldve az egyes rank API-hívásokkal. Ez rugalmasságot biztosít a műveletek és a funkcióik számára az időbeli növekedés, a változás és a zsugorodás érdekében, ami lehetővé teszi a személy számára a trendek követését.

A kávé és tea funkciói a következők:

* A Coffee Bean (például Kenya és Brazília) származási helye
* A kávé vagy tea szerves?
* Világos vagy sötét kávé

Míg a kávé három funkcióval rendelkezik az előző listában, a tea csak eggyel rendelkezik. Csak olyan funkciókat kell átadnia a személyre, akik a művelet szempontjából ésszerűek. Ne adjon meg üres értéket a szolgáltatáshoz, ha az nem vonatkozik a műveletre.

#### <a name="context-features"></a>Környezeti funkciók

A környezeti funkciók segítenek személyre szabni a környezet környezetét, például a megjelenítési eszközt, a felhasználót, a helyet és a használati esethez kapcsolódó egyéb szolgáltatásokat.

A csevegési robot kontextusa a következőket tartalmazza:

* Időjárás típusa (havas, esős, napos)
* A hét napja

A funkciók kiválasztása ebben a csevegési robotban véletlenszerű. A valós robotokban a környezeti funkciók valós adatait használják.

### <a name="design-considerations-for-this-bot"></a>Tervezési szempontok ehhez a robothoz

A beszélgetéssel kapcsolatban néhány figyelmeztetést is figyelembe kell venni:
* **Bot-interakció**: a beszélgetés nagyon egyszerű, mert a rangsort és a jutalmat egyszerű használati esetekben mutatja be. A bot Framework SDK vagy az emulátor teljes funkcionalitását nem igazolja.
* **Személyre szabás**: a funkciók véletlenszerűen vannak kiválasztva a használat szimulálása érdekében. Az éles személyre szabott forgatókönyvben ne végezzen véletlenszerű funkciókat.
* **Language Understanding (Luis)**: a Luis-modell néhány példájának hosszúságú kimondott szöveg kizárólag ehhez a mintához. Ne használja ezt a néhány példát az üzemi LUIS-alkalmazás hosszúságú kimondott szöveg.


## <a name="install-required-software"></a>A szükséges szoftverek telepítése
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A letölthető minták tárháza utasításokat tartalmaz, ha szívesebben szeretné használni a a .NET Core parancssori felülete.
- A [Microsoft bot Framework Emulator](https://aka.ms/botframeworkemulator) egy asztali alkalmazás, amely lehetővé teszi, hogy a robot-fejlesztők tesztelik és hibakeresést végezzenek a localhost-on, vagy távolról futtassanak egy alagúton keresztül.

## <a name="download-the-sample-code-of-the-chat-bot"></a>A csevegési robot mintakód letöltése

A csevegési robot a személyre szabott minták tárházban érhető el. Klónozott vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) a tárházat, majd nyissa meg a mintát a `/samples/ChatbotExample` könyvtárban a Visual Studio 2019-ben.

A tárház klónozásához használja a következő git-parancsot egy bash shellben (Terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Személyre szabott és LUIS-erőforrások létrehozása és konfigurálása

### <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

A csevegő robot használatához Azure-erőforrásokat kell létrehoznia a személyre szabott és a Language Understanding (LUIS) számára.

* [Luis-erőforrások létrehozása](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal). A létrehozási lépésben válassza a **mindkettő** lehetőséget, mivel a szerzői és az előrejelzési erőforrások is szükségesek.
* [Hozzon létre egy személyre szabott erőforrást](how-to-create-resource.md) , majd másolja a kulcsot és a végpontot a Azure Portalból. Ezeket az értékeket a `appsettings.json` .NET-projekt fájljában kell megadnia.

### <a name="create-luis-app"></a>LUIS-alkalmazás létrehozása

Ha a LUIS új, be kell [jelentkeznie](https://www.luis.ai) , és azonnal át kell telepítenie a fiókját. Nem kell új erőforrásokat létrehoznia, hanem ki kell választania az oktatóanyag előző szakaszában létrehozott erőforrásokat.

1. Új LUIS-alkalmazás létrehozásához a [Luis portálon](https://www.luis.ai)válassza ki az előfizetését és a szerzői erőforrást.
1. Ezután továbbra is ugyanazon a lapon válassza az **+ új alkalmazás a beszélgetéshez**, majd az **Importálás JSON-ként**lehetőséget.
1. Az előugró párbeszédpanelen válassza a **fájl kiválasztása** elemet, majd válassza ki a `/samples/ChatbotExample/CognitiveModels/coffeebot.json` fájlt. Adja meg a nevet `Personalizer Coffee bot` .
1. Kattintson a **betanítás** gombra a Luis-portál jobb felső sarkában.
1. Kattintson a **Közzététel** gombra, és tegye közzé az alkalmazást az előrejelzési futtatókörnyezet **üzemi tárolóhelyén** .
1. Válassza a **kezelés**, majd a **Beállítások**lehetőséget. Másolja ki az **alkalmazás azonosítójának**értékét. Ezt az értéket a `appsettings.json` .NET-projekt fájljában kell megadnia.
1. Továbbra is a **kezelés** szakaszban válassza az **Azure-erőforrások**lehetőséget. Ez megjeleníti az alkalmazáshoz kapcsolódó erőforrásokat.
1. Válassza az **előrejelzési erőforrás hozzáadása**lehetőséget. Az előugró párbeszédpanelen válassza ki az előfizetését, és az oktatóanyag előző részében létrehozott előrejelzési erőforrást, majd kattintson a **kész**gombra.
1. Másolja az **elsődleges kulcs** és a **végpont URL-címének**értékeit. Ezeket az értékeket a `appsettings.json` .NET-projekt fájljában kell megadnia.

### <a name="configure-bot-with-appsettingsjson-file"></a>A robot konfigurálása appsettings.jsfájllal

1. Nyissa meg a csevegési robot megoldás fájlját `ChatbotSamples.sln` a Visual Studio 2019-mel.
1. Nyissa meg a `appsettings.json` projekt gyökérkönyvtárában található mappát.
1. Állítsa be az oktatóanyag előző szakaszában szereplő összes öt beállítást.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>A robot létrehozása és futtatása

A konfigurálását követően `appsettings.json` készen áll a csevegési robot létrehozására és futtatására. Ha ezt teszi, megnyílik egy böngésző a futó webhelyhez `http://localhost:3978` .

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="A csevegési robot webhelyét megjelenítő böngésző képernyőképe.":::

Tartsa meg a webhelyet, mert az oktatóanyag leírja, hogy mit csinál a robot, így a robotot használhatja.


## <a name="set-up-the-bot-emulator"></a>A robot-emulátor beállítása

1. Nyissa meg a robot Emulatort, és válassza a **robot megnyitása**lehetőséget.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Képernyőkép a robot-emulátor indítási képernyőjéről.":::


1. Konfigurálja a robotot a következő **robot URL-címmel** , majd válassza a **kapcsolat**elemet:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Képernyőkép a robot-emulátor megnyitási beállításairól.":::

    Az emulátor csatlakozik a csevegési robothoz, és megjeleníti az útmutató szövegét, valamint a naplózási és hibakeresési információkat a helyi fejlesztéshez.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Képernyőkép a bot emulatorról az első beszélgetés során.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>A robot használata a bot emulatorban

1. Kérje meg, hogy írja be a menüt a következővel: `I would like to see the menu` . A csevegési robot megjeleníti az elemeket.
1. A bot arra utalhat, hogy az emulátor beírásával `Please suggest a drink for me.` megjelenik a Range kérelem és válasz a csevegési ablakban, így megtekintheti a teljes JSON-t. A robot pedig javaslatot tesz, valami hasonló`How about Latte?`
1. Válassza ki a választ, ami azt jelenti, hogy elfogadja a személyre szabott legjobb választást, `I like it.` az emulátor az 1. jutalom és a csevegési időszakra vonatkozó jutalom kérését jeleníti meg, így láthatja a teljes JSON-t. És a bot válaszol a `That’s great! I’ll keep learning your preferences over time.` és a`Would you like to get a new suggestion or reset the simulated context to a new day?`

    Ha `no` a kiválasztott értékre válaszol, a jutalom pontszáma 0 lesz a személyre szabott.


## <a name="understand-the-net-code-using-personalizer"></a>A .NET-kód értelmezése a személyre szabás használatával

A .NET-megoldás egy egyszerű bot Framework-csevegési robot. A személyre szabáshoz kapcsolódó kód a következő mappákban található:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs`a bot és a megszemélyesítő közötti interakció fájlja
* `/samples/ChatbotExample/ReinforcementLearning`-kezeli a személyre szabott modell műveleteit és funkcióit
* `/samples/ChatbotExample/Model`– a személyre szabott műveletekhez és szolgáltatásokhoz, valamint a LUIS-szándékokhoz tartozó fájlok

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs – a személyre szabás használata

Az `PersonalizerChatbot` osztály a következőből származik: `Microsoft.Bot.Builder.ActivityHandler` . Három tulajdonságot és metódust tartalmaz a beszélgetési folyamat kezeléséhez.

> [!CAUTION]
> Ne másolja a kódot ebből az oktatóanyagból. Használja a minta kódot a [személyre szabott minták tárházban](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

A `Send` robottal és a luistal folytatott kezeléssel előre rögzített metódusok. A módszerek `ChooseRankAsync` és a `RewardAsync` személyre szabás.

#### <a name="calling-rank-api-and-display-results"></a>A Rank API és a megjelenítési eredmények meghívása

A metódus `ChooseRankAsync` létrehozza a JSON-adatok küldését a személyre szabási rangsor API-nak a funkciók és a környezeti funkciók összegyűjtésével.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>A jutalmazási API és a megjelenítési eredmények meghívása

A metódus a `RewardAsync` pontszám meghatározásával létrehozza a megszemélyesítő jutalom API-nak KÜLDENDŐ JSON-adatmennyiséget. A pontszám meghatározása a felhasználói szövegben azonosított és a metódusból eljuttatott LUIS-szándék alapján történik `OnTurnAsync` .

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>A robot kialakításával kapcsolatos szempontok

Ez a minta egy egyszerű, végpontok közötti megoldást mutat be, amely egy roboton belül személyre szabható. A használati eset összetettebb lehet.

Ha éles robotban szeretné használni a személyre szabott beállításokat, tervezze meg a következőket:
* Valós idejű hozzáférés a személyre szabott, _minden alkalommal_ , amikor rangsorolt kijelölésre van szüksége. A Rank API nem lehet batch vagy gyorsítótárazva.  A jutalmak meghívása késleltethető vagy kiszervezhető egy külön folyamatba, és ha nem ad vissza jutalmat az adott időszakban, akkor az eseményhez az alapértelmezett jutalom érték van beállítva.
* A jutalom használati eseten alapuló kiszámítása: Ez a példa két, nulla értékű és egy, a pontszám nélküli és negatív értékkel rendelkező értéket mutatott be. A rendszerek részletesebb pontozást igényelnek.
* Bot Channels: Ez a minta egyetlen csatornát használ, de ha egynél több csatornát vagy a botok egyetlen csatornán való változatát kívánja használni, akkor előfordulhat, hogy a személyre szabott modell környezeti funkcióinak részeként kell figyelembe vennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel az Oktatóanyaggal, törölje a következő erőforrásokat:

* Törölje a minta projekt könyvtárát.
* Törölje a személyre szabott és LUIS-erőforrást a Azure Portal.

## <a name="next-steps"></a>További lépések
* [A Personalizer működése](how-personalizer-works.md)
* [Funkciók](concepts-features.md): a funkciókkal és környezettel kapcsolatos fogalmak megismerése
* [Jutalmak](concept-rewards.md): tudnivalók a jutalmak kiszámításáról
