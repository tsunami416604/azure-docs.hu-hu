---
title: Webalkalmazás használata – személyre szabás
description: A C# .NET-webalkalmazások személyre szabási hurokkal való testreszabásával a megfelelő tartalom biztosítható a felhasználók számára a műveletek (szolgáltatások) és a környezeti funkciók alapján.
ms.topic: tutorial
ms.date: 06/10/2020
ms.openlocfilehash: b8b5c005f7003f9b0d9ac228c5a5961c80c85599
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133129"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Oktatóanyag: személyre szabás hozzáadása egy .NET-webalkalmazáshoz

A C# .NET-webalkalmazások személyre szabási hurokkal való testreszabásával a megfelelő tartalom biztosítható a felhasználók számára a műveletek (szolgáltatások) és a környezeti funkciók alapján.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Személyre szabott kulcs és végpont beállítása
> * Funkciók gyűjtése
> * A Rank és a jutalmazási API-k meghívása
> * A _rewardActionId_ kijelölt legfelső szintű művelet megjelenítése



## <a name="select-the-best-content-for-a-web-app"></a>A legjobb tartalom kiválasztása egy webalkalmazáshoz

Egy webalkalmazásnak személyre szabottnak kell lennie, ha a weblapon olyan _műveletek_ (valamilyen típusú tartalom) vannak felsorolva, amelyeknek a megjelenítéséhez személyre kell rewardActionId. Ilyenek például a hírek, a gombok elhelyezési helyei és a termékek nevének kiválasztása.

A műveletek listáját a helyi funkciókkal együtt a személyre szabott hurokhoz is elküldheti. A személyre szabás lehetőség kijelöli az egyetlen legmegfelelőbb műveletet, majd a webalkalmazás megjeleníti ezt a műveletet.

Ebben az oktatóanyagban a műveletek élelmiszeripari típusok:

* tészta
* fagylalt
* mintaadatkészletét
* saláta
* Popcorn
* kávét
* leves

Ha segítségre van kíváncsi a személyre szabott műveletek megismeréséhez, küldje el a_funkciók_ és a _környezeti funkciók_ együttesét is.

A modell egyik **funkciója** azon művelet vagy környezet információja, amely összesíthető (csoportosított) a webalkalmazás felhasználói bázisának tagjai között. Egy szolgáltatás _nem_ egyedi (például felhasználói azonosító) vagy kifejezetten specifikus (például pontos napszak).

### <a name="actions-with-features"></a>Funkciókkal rendelkező műveletek

Minden művelet (tartalmi elem) olyan funkciókkal rendelkezik, amelyek megkönnyítik az élelmiszer-elemek megkülönböztetését.

A funkciók nincsenek konfigurálva a hurok konfigurációjának részeként a Azure Portalban. Ehelyett JSON-objektumként lesznek elküldve az egyes rank API-hívásokkal. Ez rugalmasságot biztosít a műveletek és a funkcióik számára az időbeli növekedés, a változás és a zsugorodás érdekében, ami lehetővé teszi a személy számára a trendek követését.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Környezeti funkciók

A környezeti funkciók segítenek a személyeknek megérteni a műveletek kontextusát. A minta alkalmazás kontextusa a következőket foglalja magában:

* nap-reggel, délután, este, éjszaka
* a felhasználó ízlése szerint sós, édes, keserű, savanyú vagy sós
* böngésző kontextus-felhasználói ügynöke, földrajzi hely, hivatkozó

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Hogyan használja a webalkalmazás személyre szabott szolgáltatást?

A webalkalmazás személyre szabott lehetőséget használ a legjobb megoldás kiválasztására az élelmiszerek listájában. Ehhez az alábbi adatokat kell elküldeni az egyes rank API-hívásokkal:
* olyan funkciókkal **, mint** a `taste` és a`spiceLevel`
* a **környezeti** funkciók, például `time` a nap, `taste` a felhasználó és a böngésző felhasználói ügynökének adatai, valamint a környezeti funkciók
* **kizárni kívánt műveletek** (például Juice)
* **Napszállta**, amely eltér a Rank API-k minden egyes hívásakor.

## <a name="personalizer-model-features-in-a-web-app"></a>Személyre szabott modell funkciói egy webalkalmazásban

A személyre szabott funkciók a műveletek (tartalom) és az aktuális környezet (felhasználó és környezet) funkcióit igénylik. A funkciók a műveleteknek a modell aktuális környezetéhez való igazítására szolgálnak. A modell a személyre szabott korábbi ismeretek ábrázolása a műveletekről, a kontextusról és azok funkcióiról, amelyek lehetővé teszik az informatikai részleg számára a nevelésen alapuló döntések meghozatalát.

A modell, beleértve a szolgáltatásokat, a **modell frissítési gyakoriságának** beállítása alapján frissül a Azure Portalban.

> [!CAUTION]
> Az alkalmazás funkciói a funkciók és a szolgáltatások értékeit mutatják be, de nem feltétlenül a webalkalmazásokban használható legjobb funkciókat.

### <a name="plan-for-features-and-their-values"></a>Szolgáltatások és azok értékeinek tervezése

A funkciókat azonos tervezéssel és kialakítással kell kiválasztani, amelyet a technikai architektúrában bármely sémára vagy modellre alkalmazni kellene. A szolgáltatás értékeit üzleti logikával vagy külső gyártótól származó rendszerekkel lehet beállítani. A szolgáltatási értékek nem lehetnek olyan nagyon specifikusak, amelyek nem vonatkoznak a csoportokra vagy a funkciók csoportjára.

### <a name="generalize-feature-values"></a>Általánosítási funkciók értékei

#### <a name="generalize-into-categories"></a>Általánosítás kategóriákba

Ez az alkalmazás `time` szolgáltatásként működik, de csoportokba sorolja az időt, például,, `morning` `afternoon` `evening` és `night` . Ez egy példa arra, hogy milyen időpontot kell használni, de nem kifejezetten meghatározott módon, például: `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Általánosítás részekre

Ez az alkalmazás a HTTP-kérések funkcióit használja a böngészőből. Ez egy nagyon specifikus sztringtel kezdődik az összes adattal, például:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

A **HttpRequestFeatures** osztály könyvtára általánosítja ezt a karakterláncot egy egyedi értékekkel rendelkező **userAgentInfo** -objektumba. A túl konkrét értékek üres karakterláncra vannak beállítva. A kérelem környezeti funkcióinak elküldésekor a következő JSON-formátummal rendelkezik:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Minta-webalkalmazás használata

A minta böngészőalapú webalkalmazás (az összes kód meg van határozva) a következő alkalmazásokat kell telepíteni az alkalmazás futtatásához.

Telepítse a következő szoftvereket:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) – a minta háttér-kiszolgáló a .net Core-t használja
* [Node.js](https://nodejs.org/) – az ügyfél/előtér-végpont az alkalmazástól függ
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)vagy [a .net Core parancssori felülete](https://docs.microsoft.com/dotnet/core/tools/) – használja a Visual Studio 2019 fejlesztői környezetét, vagy a a .net Core parancssori felülete az alkalmazás létrehozásához és futtatásához

### <a name="set-up-the-sample"></a>A minta beállítása
1. Az Azure személyre szabott minták tárházának klónozása.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. A megoldás megnyitásához nyissa meg a _Samples/HttpRequestFeatures_ `HttpRequestFeaturesExample.sln` .

    Ha szükséges, engedélyezze a Visual Studio számára a személyre szabott .NET-csomag frissítését.

### <a name="set-up-azure-personalizer-service"></a>Az Azure személyre szabott szolgáltatás beállítása

1. [Hozzon létre egy személyre szabott erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) a Azure Portalban.

1. A Azure Portal a `Endpoint` kulcsok és a végpontok lapon keresse meg a és a vagy a (vagy a `Key1` `Key2` működni fog). **Keys and Endpoints** Ezek a saját `PersonalizerServiceEndpoint` és a `PersonalizerApiKey` .
1. Töltse ki a `PersonalizerServiceEndpoint` **appsettings.js**.
1. A `PersonalizerApiKey` következő módszerek egyikével konfigurálja az [alkalmazásként](https://docs.microsoft.com/aspnet/core/security/app-secrets) megjelenő titkot:

    * Ha a a .NET Core parancssori felülete használja, használhatja az `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` parancsot.
    * Ha a Visual studiót használja, kattintson a jobb gombbal a projektre, és válassza a **felhasználói titkok kezelése** menüpontot a személyre szabott kulcsok konfigurálásához. Ezzel a Visual Studio megnyit egy fájlt, `secrets.json` ahol a kulcsokat a következőképpen adhatja hozzá:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Minta futtatása

Hozzon létre és futtasson HttpRequestFeaturesExample az alábbi módszerek egyikével:

* Visual Studio 2019: nyomja meg az **F5** billentyűt
* A .NET Core parancssori felülete: `dotnet build``dotnet run`

Egy webböngészőn keresztül egy rangsoroló kérést és egy jutalmazási kérést küldhet, és megtekintheti a válaszokat, valamint a környezetből kinyert HTTP-kérések funkcióit.

> [!div class="mx-imgBorder"]
> ![Hozza létre és futtassa a HTTPRequestFeaturesExample projektet. Megnyílik egy böngészőablak, amely megjeleníti az egyoldalas alkalmazást.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>A személyre szabott hurok bemutatása

1. Válassza az **új Rank-kérés** létrehozása gombot egy új JSON-objektum létrehozásához a Rank API-híváshoz. Ez létrehozza a műveleteket (a szolgáltatásokkal) és a környezeti funkciókat, és megjeleníti az értékeket, így láthatja, hogy a JSON milyen módon néz ki.

    A saját jövőbeli alkalmazásához a műveletek és szolgáltatások létrehozása történhet az ügyfélen, a kiszolgálón, a kettő kombinációján vagy más szolgáltatások meghívásával.

1. Válassza a **Range kérelem küldése** lehetőséget a JSON-objektum kiszolgálónak való elküldéséhez. A kiszolgáló meghívja a személyre szabási rangsor API-ját. A kiszolgáló megkapja a választ, és visszaadja a legfontosabb rangsorolt műveletet az ügyfélnek a megjelenítéshez.

1. Állítsa be a jutalom értékét, majd kattintson a **jutalom kérésének küldése** gombra. Ha nem módosítja a jutalom értékét, az ügyfélalkalmazás mindig a `1` személyre szabott értéket küldi el.

    > [!div class="mx-imgBorder"]
    > ![Hozza létre és futtassa a HTTPRequestFeaturesExample projektet. Megnyílik egy böngészőablak, amely megjeleníti az egyoldalas alkalmazást.](./media/tutorial-web-app/reward-score-api-call.png)

    A saját jövőbeli alkalmazása esetében előfordulhat, hogy a jutalom pontszámának generációja a felhasználó viselkedésének az ügyfélen való összegyűjtése után, valamint az üzleti logikával együtt történik a kiszolgálón.

## <a name="understand-the-sample-web-app"></a>A minta webalkalmazás megismerése

A mintául szolgáló webalkalmazás **C# .net** -kiszolgálóval rendelkezik, amely felügyeli a szolgáltatások gyűjteményét, és http-hívásokat küld és fogad a személyre szabott végpontnak.

A minta webalkalmazás egy **Knockout előtér-ügyfélalkalmazás** használatával rögzíti a szolgáltatásokat, és feldolgozza a felhasználói felület műveleteit, például a gombokra kattintva és adatokat küld a .net-kiszolgálónak.

A következő részek ismertetik a kiszolgáló és az ügyfél azon részeit, amelyeknek a fejlesztőnek meg kell ismernie a személyre szabást.

## <a name="rank-api-client-application-sends-context-to-server"></a>Range API: az ügyfélalkalmazás kontextust küld a kiszolgálónak

Az ügyfélalkalmazás gyűjti a felhasználó böngésző _felhasználói ügynökét_.

> [!div class="mx-imgBorder"]
> ![Hozza létre és futtassa a HTTPRequestFeaturesExample projektet. Megnyílik egy böngészőablak, amely megjeleníti az egyoldalas alkalmazást.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rangsor API: kiszolgálóalkalmazás-hívások személyre szabása

Ez egy tipikus .NET-webalkalmazás, amely egy ügyfélalkalmazás, és a kazán-kód nagy részét is megadja Önnek. A rendszer eltávolítja a személyre szabott kódokat a következő kódrészletekről, így a személyre szabott kódra is koncentrálhat.

### <a name="create-personalizer-client"></a>Személyre szabott ügyfél létrehozása

A kiszolgáló **Startup.cs**a személyre szabott végpontot és kulcsot használja a személyre szabott ügyfél létrehozásához. Az ügyfélalkalmazás nem kell kommunikálni a személyre szabással ebben az alkalmazásban ahelyett, hogy az SDK-hívásokat erre a kiszolgálóra támaszkodik.

A webkiszolgáló .NET indítási kódja a következő:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>A legjobb művelet kiválasztása

A kiszolgáló **PersonalizerController.cs**a **GENERATERANK** -kiszolgáló API ÖSSZEGZI a Range API meghívásának előkészítését

* Új létrehozása `eventId` a rangsor hívásához
* A műveletek listájának beolvasása
* A felhasználók listájának lekérése és a környezeti funkciók létrehozása
* Igény szerint a kizárt műveletek is megadhatók
* A Rank API hívása, eredmények visszaküldése az ügyfélnek

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

A személyre eljuttatott JSON, amely mindkét műveletet (funkciókat) és az aktuális környezeti funkciókat is tartalmazza, a következőképpen néz ki:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Személyre szabott rewardActionId visszaadása az ügyfélnek

A Rank API a kiválasztott legjobb műveleti **rewardActionId** adja vissza a kiszolgálónak.

Jelenítse meg a **rewardActionId**-ben visszaadott műveletet.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Az ügyfél megjeleníti a rewardActionId műveletet.

Ebben az oktatóanyagban az `rewardActionId` érték jelenik meg.

A saját jövőbeli alkalmazásában előfordulhat, hogy a weblap egy része, egy gomb vagy egy szakasz jelenik meg. A lista a pontszámok utólagos elemzését adja vissza, nem pedig a tartalom rendezését. Csak a `rewardActionId` tartalomnak kell megjelennie.

## <a name="reward-api-collect-information-for-reward"></a>Jutalom API: gyűjtsön információkat a jutalomhoz

A [jutalom pontszámát](concept-rewards.md) körültekintően kell megtervezni, ugyanúgy, mint a szolgáltatások tervezése. A jutalom pontszámnak jellemzően 0 és 1 közötti értéknek kell lennie. Az értéket _az_ ügyfélalkalmazás részben, felhasználói viselkedés alapján, részben pedig a kiszolgálón, az üzleti logika és a célok alapján lehet kiszámítani.

Ha a kiszolgáló nem hívja meg a jutalmazási API-t a személyre szabott erőforráshoz Azure Portal konfigurált **jutalmazási várakozási időn** belül, akkor az adott eseményhez az **alapértelmezett jutalom** (a Azure Portal is konfigurálva van) van használatban.

Ebben a példában egy érték kiválasztásával megtekintheti, hogy a jutalom milyen hatással van a kiválasztásokra.

## <a name="additional-ways-to-learn-from-this-sample"></a>További módszerek a mintából való tanuláshoz

A minta több időalapú eseményt használ a Azure Portal a személyre szabott erőforráshoz. Ha ezekkel az értékekkel játssza le ezeket az értékeket, térjen vissza ehhez a minta-webalkalmazáshoz, és nézze meg, hogyan befolyásolják a változások a rangsort

* Jutalom várakozási ideje
* Modell frissítési gyakorisága

Többek között a következő további beállítások játszhatók le:
* Alapértelmezett jutalom
* Kutatási százalék


## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha elkészült ezzel az Oktatóanyaggal, törölje a következő erőforrásokat:

* Törölje a minta projekt könyvtárát.
* A személyre szabott erőforrás törlése – gondolja át, hogy egy személyre szabott erőforrás a műveletekhez és a kontextushoz legyen felhasználva


## <a name="next-steps"></a>További lépések
* [A Personalizer működése](how-personalizer-works.md)
* [Funkciók](concepts-features.md): a funkciókkal és környezettel kapcsolatos fogalmak megismerése
* [Jutalmak](concept-rewards.md): tudnivalók a jutalmak kiszámításáról
