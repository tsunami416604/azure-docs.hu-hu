---
title: Visszajelzés hurok - Personalizer
titleSuffix: Azure Cognitive Services
description: Ez a tartalom személyre szabásához C# rövid útmutató a Personalizer szolgáltatással.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: f0aca3e387d675064cf798b4efdeb66cfe906520
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153553"
---
# <a name="quickstart-personalize-content-using-c"></a>Gyors útmutató: Személyre szabhatja a tartalom használatávalC# 

Ez a személyre szabott tartalom megjelenítése C# rövid útmutató a Personalizer szolgáltatással.

Ez a minta bemutatja, hogyan használhatja a személyre szabás készült ügyféloldali kódtára C# a következő műveletek végrehajtásához: 

 * Rangsorolja a testreszabási műveletek listáját.
 * Érdemes lefoglalni a művelet a felhasználó kiválasztott a megadott esemény alapján rangsorolt felső ellenszolgáltatás jelentést.

Ismerkedés a személyre szabása az alábbi lépésekből áll:

1. Hivatkozás az SDK-ra 
1. A felhasználók számára megjelenítendő műveletek rangsorolását kód írása
1. Kód írása a hurok betanításához jutalmakat küldése.

## <a name="prerequisites"></a>Előfeltételek

* Kell egy [Personalizer szolgáltatás](how-to-settings.md) az előfizetést a kulcs és a jogkivonatok kiállításának szolgáltatás URL-címe. 
* [A Visual Studio 2015 vagy 2017](https://visualstudio.microsoft.com/downloads/).
* A Microsoft.Azure.CognitiveServices.Personalization SDK NuGet-csomagot. A telepítési utasításokat az alábbiakban találja.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Egy új Konzolalkalmazás létrehozása és a hivatkozás a Personalizer SDK-ra 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Személyre szabás kódtár NuGet csomag telepítését. Válassza a menü **eszközök**válassza **Nuget package Manager**, majd **NuGet-csomagok kezelése megoldáshoz**.
1. Válassza ki a **Tallózás** fülre, majd a a **keresési** mezőbe írja be `Microsoft.Azure.CognitiveServices.Personalization`.
1. Válassza ki **Microsoft.Azure.CognitiveServices.Personalization** mikor jeleníti meg.
1. Jelölje be a projekt neve melletti jelölőnégyzetet, és válassza ki **telepítése**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Adja hozzá a kódot, és helyezze a Personalizer és az Azure-kulcsok

1. A Program.cs fájl tartalmát cserélje le a következő kódra. 
1. Cserélje le `serviceKey` értéke az érvényes Personalizer előfizetési kulccsal végzett.
1. Cserélje le `serviceEndpoint` a szolgáltatás-végponthoz. Például: `https://westus2.api.cognitive.microsoft.com/`.
1. Futtassa a programot.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Adja hozzá a felhasználók számára megjelenítendő műveletek rangsorolását kódot

A következő C# kódja át a felhasználói adatok _features és a tartalommal kapcsolatos információk teljes körét _műveletek_, az SDK-val Personalizer. Personalizer rangsorolt művelet a felhasználó megjelenítendő felső adja vissza.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

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

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
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
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

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
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
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

Hozza létre és futtassa a programot. A rövid útmutató program rákérdez, hogy gyűjtse össze a felhasználói beállítások, szolgáltatások, más néven kérdések néhány majd biztosít a top művelet.

![A rövid útmutató program rákérdez, hogy gyűjtse össze a felhasználói beállítások, szolgáltatások, más néven kérdések néhány majd biztosít a top művelet.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések

[Personalizer működése](how-personalizer-works.md)


