---
title: Az Azure Content Moderator az egyéni kifejezéslisták mérsékelt |} A Microsoft Docs
description: Az egyéni kifejezés közepes hogyan sorolja fel, .NET-hez készült Azure Content Moderator SDK használatával.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 55233198c4553f9838036e4eb91cff380af1988d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182297"
---
# <a name="moderate-with-custom-term-lists-in-net"></a>A .NET-keretrendszerben egyéni kifejezéslisták mérsékelt

Az alapértelmezett globális listáját az Azure Content Moderator feltételeket is elegendő legtöbb tartalom moderálása igényei szerint. Azonban szüksége lehet a szervezet adott feltételek képernyőn. Érdemes például versenytárs címkenevek további ellenőrzésre. 

Használhatja a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) egyéni listák a moderálási API használata a feltételek létrehozásához.

> [!NOTE]
> A maximális korlát **5 kifejezés sorolja fel** az egyes lista **nem haladhatja meg a 10 000 feltételek**.
>

Ez a cikk nyújt információt, és kódminták segítségével .NET-Content Moderator SDK használatának első lépései:
- Hozzon létre egy listát.
- Feltételek hozzáadása a listához.
- Képernyőn elleni egy lista a használati feltételeket.
- Feltételek törlése egy listáról.
- Egy lista törölhető.
- Lista adatainak szerkesztése.
- Frissítse az index, hogy a lista módosításainak egy új vizsgálatot szerepelnek.

Ez a cikk azt feltételezi, hogy már ismeri a Visual Studio és C#.

## <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a Content Moderator szolgáltatások

A REST API-t vagy az SDK-t a Content Moderator szolgáltatások használata előtt szüksége van egy előfizetési kulcsot.

A Content Moderator irányítópultján található az előfizetési kulcs a **beállítások** > **hitelesítő adatok** > **API**  >  **Próbaverziós Ocp-Apim-Subscription-Key**. További információkért lásd: [áttekintése](overview.md).

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Vegyen fel egy új **Console app (.NET Framework)** projektet a megoldáshoz.

1. Adja a projektnek **TermLists**. Jelölje ki a projektet a megoldáshoz egyetlen indítási projektként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő, a TermLists projekt NuGet-csomagok telepítéséhez:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Frissítés a program által utasítások segítségével.

Módosítsa a program által utasítások segítségével.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>A Content Moderator ügyfél létrehozása

Adja hozzá a következő kódot a Content Moderator ügyfélbeállítások az előfizetéshez.

> [!IMPORTANT]
> Frissítés a **AzureRegion** és **CMSubscriptionKey** mezőket a régió azonosítója és az Előfizetés kulcs értékét.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

### <a name="add-private-properties"></a>Adja hozzá a saját tulajdonságai

Adja hozzá a következő privát tulajdonságok névtérhez TermLists, osztály Program.

    /// <summary>
    /// The language of the terms in the term lists.
    /// </summary>
    private const string lang = "eng";

    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Content Moderator APIs.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of minutes to delay after updating the search index before
    /// performing image match operations against the list.
    /// </summary>
    private const double latencyDelay = 0.5;

## <a name="create-a-term-list"></a>Hozzon létre egy kifejezéslista

Az előfizetési időszak lista létrehozása **ContentModeratorClient.ListManagementTermLists.Create**. Az első paraméterként **létrehozás** egy karakterlánc, amely tartalmazza a MIME-típust, amely "application/json" kell lennie. További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). A második paraméter egy **törzs** objektum, amely egy nevet és leírást az új kifejezés listáját tartalmazza.

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

> [!NOTE]
> A Content Moderator Szolgáltatáskulcs rendelkezik egy második (RPS) sávszélesség-korlátjának kérelemre, és ha túllépi a korlátot, az SDK kivételt 429 hibakód. 
>
> Ingyenes szint kulcs esetében egy függő Entitás sebessége.

    /// <summary>
    /// Creates a new term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <returns>The term list ID.</returns>
    static string CreateTermList (ContentModeratorClient client)
    {
        Console.WriteLine("Creating term list.");

        Body body = new Body("Term list name", "Term list description");
        TermList list = client.ListManagementTermLists.Create("application/json", body);
        if (false == list.Id.HasValue)
        {
            throw new Exception("TermList.Id value missing.");
        }
        else
        {
            string list_id = list.Id.Value.ToString();
            Console.WriteLine("Term list created. ID: {0}.", list_id);
            Thread.Sleep(throttleRate);
            return list_id;
        }
    }

## <a name="update-term-list-name-and-description"></a>Kifejezés nevének és leírásának frissítése

Az előfizetési időszak adatok frissítése **ContentModeratorClient.ListManagementTermLists.Update**. Az első paraméterként **frissítés** az előfizetési időszak lista azonosítója. A második paraméter egy MIME-típust, amely "application/json" kell lennie. További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). A harmadik paraméter egy **törzs** objektum, amely tartalmazza az új nevet és leírást.

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Update the information for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="name">The new name for the term list.</param>
    /// <param name="description">The new description for the term list.</param>
    static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
    {
        Console.WriteLine("Updating information for term list with ID {0}.", list_id);
        Body body = new Body(name, description);
        client.ListManagementTermLists.Update(list_id, "application/json", body);
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-term-to-a-term-list"></a>Egy kifejezés listához kifejezés hozzáadása

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Add a term to the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="term">The term to add to the term list.</param>
    static void AddTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
        client.ListManagementTerm.AddTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

## <a name="get-all-terms-in-a-term-list"></a>Minden használati lépjen be egy kifejezéslista

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Get all terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to get all terms.</param>
    static void GetAllTerms(ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
        Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
        TermsData data = terms.Data;
        foreach (TermsInList term in data.Terms)
        {
            Console.WriteLine(term.Term);
        }
        Thread.Sleep(throttleRate);
    }

## <a name="add-code-to-refresh-the-search-index"></a>Adja hozzá a kódot a search-index frissítése

A kifejezéslista módosítása után frissíti a search-index része lesz a következő képernyő szövege kifejezés lista használatakor a módosítások. Ez hasonlít hogyan egy keresőmotor, az asztalon (Ha engedélyezve van) és a egy webes keresőmotor folyamatosan frissíti új fájlok és lapok indexét.

Frissíti az előfizetési időszak lista keresési indexet **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Refresh the search index for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to refresh.</param>
    static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
        client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
        Thread.Sleep((int)(latencyDelay * 60 * 1000));
    }

## <a name="screen-text-using-a-term-list"></a>Képernyő szövege kifejezés lista használatával

A képernyőn az előfizetési időszak lista használatával szöveg **ContentModeratorClient.TextModeration.ScreenText**, amely a következő paramétereket fogadja.

- A feltételek a kifejezéslista nyelve.
- A MIME-típust, amely "text/html", "szöveg/xml", "szöveg/markdown" vagy "text/plain" lehet.
- A képernyő szöveg.
- Logikai érték. Adja meg a mezőben **igaz** automatikus javítási előtt megkötése azt a szöveget.
- Logikai érték. Adja meg a mezőben **igaz** a személyes azonosításra alkalmas adatok (PII) észlelheti a szövegben.
- A kifejezés listaazonosító.

További információkért lásd: a [API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** adja vissza egy **képernyő** objektum, amely rendelkezik egy **feltételek** tulajdonság, amely felsorolja az összes feltételeit, hogy a Content Moderator, a szűrés észlelhető. Ha a Content Moderator nem talált meg a vonatkozó használati feltételek a szűrés során vegye figyelembe, hogy a **feltételek** vlastnost má hodnotu **null**.

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Screen the indicated text for terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to use to screen the text.</param>
    /// <param name="text">The text to screen.</param>
    static void ScreenText (ContentModeratorClient client, string list_id, string text)
    {
        Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
        Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
        if (null == screen.Terms)
        {
            Console.WriteLine("No terms from the term list were detected in the text.");
        }
        else
        {
            foreach (DetectedTerms term in screen.Terms)
            {
                Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
            }
        }
        read.Sleep(throttleRate);
    }

## <a name="delete-terms-and-lists"></a>Listák szerződési feltételek törlése

Egy kifejezést vagy egy lista törlése nagyon egyszerű. Az SDK használatával a következő feladatokat végezheti el:

- Egy kifejezés törlése. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- A lista összes használati törlése a lista törlése nélkül. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Törölje a listáját, és annak teljes tartalmát. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Kifejezés törlése

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Delete a term from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete the term.</param>
    /// <param name="term">The term to delete.</param>
    static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
        client.ListManagementTerm.DeleteTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-all-terms-in-a-term-list"></a>Egy kifejezés lista összes feltételek törlése

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Delete all terms from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete all terms.</param>
    static void DeleteAllTerms (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
        client.ListManagementTerm.DeleteAllTerms(list_id, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-a-term-list"></a>Kifejezés listájának törlése

Adja hozzá a következő definice metody névtér TermLists, osztály Program.

    /// <summary>
    /// Delete the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to delete.</param>
    static void DeleteTermList (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Deleting term list with ID {0}.", list_id);
        client.ListManagementTermLists.Delete(list_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>A teljes kép

Adja hozzá a **fő** metódus TermLists, névtér-definíciót a Program osztályhoz. Végül zárja be a Program osztályt, valamint a TermLists névteret.

    static void Main(string[] args)
    {
        using (var client = Clients.NewClient())
        {
            string list_id = CreateTermList(client);

            UpdateTermList(client, list_id, "name", "description");
            AddTerm(client, list_id, "term1");
            AddTerm(client, list_id, "term2");

            GetAllTerms(client, list_id);

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            string text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteTerm(client, list_id, "term1");

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteAllTerms(client, list_id);
            DeleteTermList(client, list_id);

            Console.WriteLine("Press ENTER to close the application.");
            Console.ReadLine();
        }
    }

## <a name="run-the-application-to-see-the-output"></a>A kimenet megtekintéséhez az alkalmazás futtatása

A kimenet a következő sorokban lesz, de az adatok eltérőek lehetnek.

    Creating term list.
    Term list created. ID: 252.
    Updating information for term list with ID 252.
    
    Adding term "term1" to term list with ID 252.
    Adding term "term2" to term list with ID 252.
    
    Getting terms in term list with ID 252.
    term1
    term2
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term1" from list ID 252 at index 32.
    Found term: "term2" from list ID 252 at index 46.
    
    Removed term "term1" from term list with ID 252.
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term2" from list ID 252 at index 46.
    
    Removing all terms from term list with ID 252.
    Deleting term list with ID 252.
    Press ENTER to close the application.
    
## <a name="next-steps"></a>További lépések

Első a [Content Moderator .NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és a [Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez, és az integrációval kapcsolatos első lépések.
