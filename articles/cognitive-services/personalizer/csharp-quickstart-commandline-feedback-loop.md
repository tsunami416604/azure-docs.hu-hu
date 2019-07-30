---
title: 'Gyors útmutató: Visszajelzési hurok létrehozása – személyre szabás'
titleSuffix: Azure Cognitive Services
description: A rövid útmutató tartalmainak C# személyre szabása a személyre szabott szolgáltatással.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662799"
---
# <a name="quickstart-personalize-content-using-c"></a>Gyors útmutató: Tartalom személyre szabása a használatávalC# 

Személyre szabott tartalom megjelenítése C# ebben a rövid útmutatóban a személyre szabási szolgáltatással.

Ez a minta azt mutatja be, hogyan használható a személyre szabott C# ügyféloldali kódtár a következő műveletek elvégzéséhez: 

 * A személyre szabási műveletek listájának rangsorolása.
 * A megadott esemény felhasználó általi kiválasztása alapján a legjobb rangsorolt művelethez való kiosztásra vonatkozó jelentés.

A személyre szabás első lépései a következő lépésekből állnak:

1. Az SDK-ra való hivatkozás 
1. Kód írása a felhasználók számára megjeleníteni kívánt műveletek rangsorolása érdekében
1. Kód írása a jutalmak küldéséhez a hurok betanításához.

## <a name="prerequisites"></a>Előfeltételek

* Az előfizetési kulcs és a végponti szolgáltatás URL-címének beszerzéséhez [személyre szabott szolgáltatásra](how-to-settings.md) van szükség. 
* [Visual Studio 2015 vagy 2017](https://visualstudio.microsoft.com/downloads/).
* A [Microsoft. Azure. CognitiveServices. személyre szabott](https://go.microsoft.com/fwlink/?linkid=2092272) SDK NuGet csomagja. A telepítési utasításokat az alábbiakban találja.

## <a name="change-the-model-update-frequency"></a>A modell frissítési gyakoriságának módosítása

A Azure Portal személyre szabott erőforrásában módosítsa a **modell frissítési gyakoriságát** 10 másodpercre. Ez gyorsan betanítja a szolgáltatást, így láthatja, hogy az egyes iterációk legfelső szintű művelete hogyan változik.

Ha a rendszer először létrehoz egy személyre szabott hurkot, nem áll rendelkezésre modell, mert nem áll rendelkezésre jutalom API-hívás a betanításhoz. A rangsorban megjelenő hívások az egyes elemek esetében azonos valószínűségeket adnak vissza. Az alkalmazásnak mindig a RewardActionId kimenetével kell rangsorolnia a tartalmat.

![Modell frissítési gyakoriságának módosítása](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Új Console-alkalmazás létrehozása és a személyre szabott SDK-ra való hivatkozás 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Telepítse a személyre szabott ügyféloldali függvénytár NuGet-csomagot. A menüben válassza az **eszközök**, majd a **Nuget csomagkezelő**, majd a **megoldás Nuget-csomagok kezelése**lehetőséget.
1. Keresse meg az **előzetes kiadást**.
1. Válassza a **Tallózás** lapot, majd a **keresőmezőbe** írja be `Microsoft.Azure.CognitiveServices.Personalizer`a kifejezést.
1. A megjelenítéskor válassza a **Microsoft. Azure. CognitiveServices. personalization** elemet.
1. Jelölje be a projekt neve melletti jelölőnégyzetet, majd válassza a **telepítés**lehetőséget.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Adja hozzá a kódot, és tegye a személyre és az Azure-kulcsokra

1. A Program.cs fájl tartalmát cserélje le a következő kódra. 
1. Cserélje `serviceKey` le az értéket az érvényes személyre szabott előfizetési kulcsra.
1. Cserélje `serviceEndpoint` le a szolgáltatást a szolgáltatás-végpontra. Például: `https://westus2.api.cognitive.microsoft.com/`.
1. Futtassa a programot.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Kód hozzáadása a felhasználók számára megjeleníteni kívánt műveletek rangsorolása érdekében

A következő C# kód egy teljes lista, amellyel továbbíthatja a felhasználói adatokat, a _features, valamint a tartalommal, a _műveletekkel_és az SDK-val való személyre szabással kapcsolatos információkat. A személyre szabás a legjobban rangsorolt műveletet adja vissza a felhasználó megjelenítéséhez.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>A program futtatása

Hozza létre és futtassa a programot. A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések

[A megszemélyesítő működése](how-personalizer-works.md)


