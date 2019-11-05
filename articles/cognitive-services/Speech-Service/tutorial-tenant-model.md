---
title: Bérlői modell létrehozása (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Automatikusan hozhatja ki a Office 365-adatait használó egyéni beszédfelismerési modellt, hogy optimális hangfelismerést nyújtson a szervezetre vonatkozó, biztonságos és megfelelő feltételekhez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 85b9291ee24c024ebc8ce81ddba46d04f7744081
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502659"
---
# <a name="create-a-tenant-model-preview"></a>Bérlői modell létrehozása (előzetes verzió)

A bérlői modell egy olyan Office 365 vállalati ügyfelek számára elérhető, amely automatikusan létrehoz egy egyéni beszédfelismerési modellt a szervezet Office 365-adataiból. A létrehozott modell a technikai feltételekhez, a zsargonhoz és a személyek neveihez van optimalizálva, mindezt biztonságos és megfelelő módon.

> [!IMPORTANT]
> Ha a szervezete bérlői modellel regisztrál, a beszédfelismerési szolgáltatás elérheti a szervezet nyelvi modelljét, amelyet az Office 365-erőforrások, például az e-mailek és a dokumentumok generálnak. A szervezet Office 365 rendszergazdája be-és kikapcsolhatja a szervezeti szintű nyelvi modell használatát az Office 365 felügyeleti portál használatával.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Regisztrálás a bérlői modell használatára a Microsoft 365 felügyeleti központban
> * Beszédfelismerési előfizetés kulcsának beolvasása
> * Bérlői modell létrehozása
> * Bérlői modell üzembe helyezése
> * Bérlői modell használata a Speech SDK-val

![Bérlői modell diagramja](media/tenant-language-model/tenant-language-model-diagram.png)

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Regisztrálás a Microsoft 365 felügyeleti központtal

A bérlői modell üzembe helyezése előtt először regisztrálnia kell a Microsoft 365 felügyeleti központban. Ezt a feladatot csak a Microsoft 365 rendszergazdája végezheti el.

1. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://admin.microsoft.com ).
2. A bal oldali panelen válassza a **Beállítások** , majd az **alkalmazások**lehetőséget.

   ![Bérlői modell diagramja](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Keresse meg és válassza ki az **Azure Speech Services**elemet.

   ![Bérlői modell diagramja](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Kattintson a jelölőnégyzetre, és mentse.

Ha ki kell kapcsolnia a bérlői modellt, térjen vissza erre a képernyőre, törölje a jelölőnégyzet jelölését, és mentse a következőt:.

## <a name="get-a-speech-subscription-key"></a>Beszédfelismerési előfizetés kulcsának beolvasása

Ahhoz, hogy a Speech SDK-val bérlői modellt lehessen használni, szüksége lesz egy beszédfelismerési erőforrásra és a hozzá tartozó előfizetési kulcsra.

1. Jelentkezzen be az [Azure Portalra](https://aka.ms/azureportal).
2. Válassza **az erőforrás létrehozása**lehetőséget.
3. A keresősáv mezőbe írja be a következőt: **Speech**.
4. Válassza a **beszéd**lehetőséget, majd kattintson a **Létrehozás**gombra.
5. Az erőforrás létrehozásához kövesse a képernyőn megjelenő utasításokat. biztosra menni:
   * A **hely** a **eastus** vagy a **westus**értékre van beállítva.
   * A **díjszabás** a **S0**értékre van állítva.
6. Kattintson a **Létrehozás** elemre.
7. Néhány perc elteltével létrejön az erőforrás. Az előfizetési kulcs az erőforrás **Áttekintés** szakaszában érhető el.

## <a name="create-a-model"></a>Modell létrehozása

Miután a rendszergazda engedélyezte a bérlői modellt a szervezet számára, létrehozhat egy nyelvi modellt a Office 365 adatai alapján.

1. Jelentkezzen be a [Speech studióba](https://speech.microsoft.com/).
2. A jobb felső sarokban keresse meg és kattintson a fogaskerék ikonra (beállítások), majd válassza a **bérlői modell beállításai**lehetőséget.

   ![Beállítások menü](media/tenant-language-model/tenant-language-settings.png)

3. Ekkor megjelenik egy üzenet, amely tájékoztatja, ha Ön jogosult a bérlői modell létrehozására.
   > [!NOTE]
   > Az Office 365 Enterprise ügyfelei Észak-Amerika jogosultak a bérlői modell (angol) létrehozására. Ha Ön Ügyfélszéf (CLB) vagy az ügyfél kulcsának (CK) ügyfelének, ez a funkció nem érhető el. A következő utasításokat követve állapíthatja meg, hogy Ön Ügyfélszéf vagy Customer Key-ügyfél-e:
   > * [Ügyfélszéf](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Ügyfél kulcsa](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)

4. Ezután válassza **a**bekapcsolás lehetőséget. E-mailt fog kapni, ha a bérlői modell elkészült.

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Ha a bérlői modell elkészült, kövesse az alábbi lépéseket a modell üzembe helyezéséhez:

1. Kattintson a **modell megtekintése** gombra a megerősítő e-mailben, vagy jelentkezzen be a [Speech studióba](https://speech.microsoft.com/).
2. A jobb felső sarokban keresse meg és kattintson a fogaskerék ikonra (beállítások), majd válassza a **bérlői modell beállításai**lehetőséget.

   ![Beállítások menü](media/tenant-language-model/tenant-language-settings.png)

3. Kattintson a **telepítés**elemre.
4. A modell telepítésekor a rendszer az állapotot **telepítettre**módosítja.

## <a name="use-your-model-with-the-speech-sdk"></a>Modell használata a Speech SDK-val

Most, hogy üzembe helyezte a modellt, használhatja azt a Speech SDK-val. Ebben a szakaszban a megadott mintakód használatával hívja meg a Speech szolgáltatást az Azure AD-hitelesítéssel.

Nézzük meg azt a kódot, amelyet a Speech SDK meghívásához fog használni C#. Ebben a példában a beszédfelismerést a bérlői modell használatával hajtja végre. Ez az útmutató feltételezi, hogy a platform már be van állítva. Ha segítségre van szüksége a beállításához, tekintse meg a gyors útmutató [: beszédfelismerés felismerése C# (.net Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)című témakört.

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

    // Note: ServiceApplicationId is a fixed value.  No need to change.

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

Ezután újra kell építenie és futtatnia kell a projektet a parancssorból. A parancs futtatása előtt néhány paramétert frissítenie kell.

1. Cserélje le a `<Username>` és `<Password>` egy érvényes bérlői felhasználó értékeit.
2. Cserélje le a `<Subscription-Key>`t a beszédfelismerési erőforráshoz tartozó előfizetési kulcsra. Ez az érték a [Azure Portal](https://aka.ms/azureportal)beszédfelismerési erőforrásának **Áttekintés** szakaszában érhető el.
3. Cserélje le a `<Endpoint-Uri>`t az alábbi végpontra. Győződjön meg arról, hogy lecserélte `{your-region}`t arra a régióra, ahol a beszédfelismerési erőforrás létrejött. Ezek a régiók támogatottak: `westus`, `westus2`és `eastus`. A régióval kapcsolatos információk a [Azure Portal](https://aka.ms/azureportal)beszédfelismerési erőforrásának **Áttekintés** szakaszában találhatók.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Futtassa a parancsot:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>További lépések

* [Speech Studio](https://speech.microsoft.com/)
* [Beszéd SDK](speech-sdk.md)
