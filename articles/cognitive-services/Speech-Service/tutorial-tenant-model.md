---
title: Bérlői modell létrehozása (előzetes verzió) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Automatikusan létrehoz egy biztonságos, megfelelő bérlői modellt (Egyéni beszéd az Office 365-adatokkal), amely az Office 365-adatokat használja az optimális beszédfelismerés biztosítására a szervezetspecifikus kifejezésekhez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469017"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Oktatóanyag: Bérlői modell létrehozása (előzetes verzió)

A bérlői modell (Egyéni beszéd felismerés az Office 365-adatokkal) az Office 365 nagyvállalati ügyfeleinek olyan opt-in szolgáltatása, amely automatikusan létrehoz egy egyéni beszédfelismerési modellt a szervezet Office 365-adataiból. A modell a technikai kifejezésekre, a zsargonra és az emberek nevére van optimalizálva, mindezt biztonságos és megfelelő módon.

> [!IMPORTANT]
> Ha a szervezet a bérlői modell szolgáltatás használatával iratkozik be, a Beszédszolgáltatás hozzáférhet a szervezet nyelvi modelljéhez. A modell az Office 365 nyilvános csoportos e-mailjeiből és dokumentumaiból jön létre, amelyeket a szervezet en belül bárki láthatja. A szervezet Office 365-rendszergazdája az Office 365 felügyeleti portálról be- vagy kikapcsolhatja a szervezeti szintű nyelvi modell használatát.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Igénylés a bérlői modellbe a Microsoft 365 felügyeleti központ használatával
> * Beszédfelismerési előfizetési kulcs beolvasása
> * Bérlői modell létrehozása
> * Bérlői modell üzembe helyezése
> * A bérlői modell használata a beszédbeszéd SDK-val

## <a name="enroll-in-the-tenant-model-service"></a>Igénylés a bérlői modell szolgáltatásba

A bérlői modell üzembe helyezése előtt be kell iratkoznia a bérlői modell szolgáltatásba. A regisztráció a Microsoft 365 Felügyeleti központban fejeződik be, és csak a Microsoft 365 rendszergazdája végezheti el.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

1. A bal oldali ablaktáblában válassza a **Beállítások**lehetőséget, majd válassza a beágyazott menü **Beállítások parancsát,** majd válassza az Azure **Speech Services** elemet a főablakból.

   ![A "Szolgáltatások & bővítmények" ablaktábla](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Jelölje be **a Szervezeti szintű nyelvi modell engedélyezése** jelölőnégyzetet, majd a Módosítások mentése **lehetőséget.**

   ![Az Azure Beszédszolgáltatások ablaktábla](media/tenant-language-model/tenant-language-model-enrollment-2.png)

A bérlői modellpéldány kikapcsolása:
1. Ismételje meg az előző 1.
1. Törölje **a** jelet a Szervezeti szintű nyelvi modell engedélyezése jelölőnégyzetből, majd válassza **a Módosítások mentése lehetőséget.**

## <a name="get-a-speech-subscription-key"></a>Beszédfelismerési előfizetési kulcs beolvasása

A bérlői modell használata a speech SDK, szüksége van egy beszédfelismerési erőforrás és a társított előfizetési kulcs.

1. Jelentkezzen be az [Azure Portalra.](https://aka.ms/azureportal)
1. Válassza az **Erőforrás létrehozása** lehetőséget.
1. A **Keresés mezőbe** írja be a **Beszéd**szót .
1. Az eredménylistában válassza a **Beszéd**lehetőséget, majd a **Létrehozás gombot.**
1. Kövesse a képernyőn megjelenő utasításokat az erőforrás létrehozásához. Ellenőrizze a következőket:
   * **A helyszín** eastus vagy **westus.** **westus**
   * **A tarifacsomag** **S0-re**van állítva.
1. Kattintson a **Létrehozás** gombra.

   Néhány perc múlva létrejön az erőforrás. Az előfizetési kulcs az **erőforrás áttekintése** szakaszban érhető el.

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

Miután a rendszergazda engedélyezte a bérlői modellt a szervezet számára, létrehozhat egy nyelvi modellt, amely az Office 365-adatokon alapul.

1. Jelentkezzen be a [Beszédstúdióba](https://speech.microsoft.com/).
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon) lehetőséget, majd a **Bérlői modell beállításai lehetőséget.**

   ![A "Bérlői modell beállításai" hivatkozás](media/tenant-language-model/tenant-language-settings.png)

   A Speech Studio egy üzenetet jelenít meg, amely lehetővé teszi, hogy jogosult-e a bérlői modell létrehozására.

   > [!NOTE]
   > Az észak-amerikai Office 365 nagyvállalati ügyfelek jogosultak bérlői modell (angol nyelven) létrehozására. Ha Ön ügyfélszéf, ügyfélkulcs vagy Office 365 Government-ügyfél, ez a funkció nem érhető el. Annak megállapításához, hogy Ön ügyfélszéf vagy ügyfélkulcs-ügyfél, a következő témakörben találja:
   > * [Ügyfélszéf](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Vevőkulcs](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Kormányzati](https://www.microsoft.com/microsoft-365/government)

1. Válassza a **jóváhagyási** lehetőséget.

   Amikor a bérlői modell készen áll, kap egy visszaigazoló e-mailt a további utasításokat.

## <a name="deploy-your-tenant-model"></a>A bérlői modell üzembe helyezése

Ha a bérlői modellpéldány készen áll, telepítse az alábbi módon:

1. A visszaigazoló e-mailben válassza a **Modell megtekintése** gombot. Vagy jelentkezzen be a [Speech Studio](https://speech.microsoft.com/).
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon) lehetőséget, majd a **Bérlői modell beállításai lehetőséget.**

   ![A "Bérlői modell beállításai" hivatkozás](media/tenant-language-model/tenant-language-settings.png)

1. Válassza az **Üzembe helyezés** lehetőséget.

   A modell üzembe helyezése után az állapot telepítve lévő állapotra *változik.*

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>A bérlői modell használata a beszédbeszéd SDK-val

Most, hogy üzembe helyezte a modellt, használhatja azt a beszédfelismerési SDK-val. Ebben a szakaszban mintakódot használ a Speech Service hívásához az Azure Active Directory (Azure AD) hitelesítés használatával.

Nézzük meg a kód, amelyet a beszédsdk c#-ban való hívásához használni fog. Ebben a példában a bérlői modell használatával beszédfelismerést hajt végre. Ez az útmutató feltételezi, hogy a platform már be van állítva. Ha telepítési segítségre van szüksége, olvassa [el a Rövid útmutató: Beszéd felismerése, C# (.NET Core) című témakört.](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)

Másolja a projektbe a következő kódot:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Ezután újra kell építenie és futtatnia kell a projektet a parancssorból. A parancs futtatása előtt frissítsen néhány paramétert az alábbi módon:

1. Cserélje `<Username>` `<Password>` le, és az értékeket egy érvényes bérlői felhasználó.
1. Cserélje `<Subscription-Key>` le a beszédfelismerési erőforrás előfizetési kulcsára. Ez az érték az [Azure Portalon](https://aka.ms/azureportal)a beszédfelismerési erőforrás **áttekintése** szakaszában érhető el.
1. Cserélje `<Endpoint-Uri>` le a következő végpontra. Győződjön meg `{your region}` arról, hogy lecseréli azt a régiót, ahol a beszédfelismerési erőforrást létrehozták. Ezek a régiók `westus` `westus2`támogatottak: , , és `eastus`. A régióadatai az [Azure Portalon](https://aka.ms/azureportal)a Beszédfelismerés-erőforrás **Áttekintés szakaszában** érhetők el.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Futtassa az alábbi parancsot:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egyéni beszédfelismerési modellt az Office 365-adatokkal egyéni beszédfelismerési modell létrehozásához, üzembe helyezéséhez és a beszédfelismerési SDK-val való használatához.

## <a name="next-steps"></a>További lépések

* [Beszéd Stúdió](https://speech.microsoft.com/)
* [Beszéd SDK](speech-sdk.md)
