---
title: Speaker Recognition rövid útmutató – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: 'Megtudhatja, hogyan használhatja a Speech SDK Speaker Recognition a következő kérdésre: "ki beszél". Ebben a rövid útmutatóban megismerheti a hangszórók ellenőrzésének és azonosításának közös kialakítási mintáit, amelyek mind a hangbiometry az egyedi hangok azonosítására használják.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/02/2020
ms.author: trbye
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: hangszórók felismerése, hangvezérelt biometry
ms.openlocfilehash: cfb16d95c0de7fabb7e939e53903f0a611f749e9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91362554"
---
# <a name="get-started-with-speaker-recognition"></a>Ismerkedés a Speaker Recognition

Ebben a rövid útmutatóban megtudhatja, milyen alapszintű tervezési mintákat Speaker Recognition a Speech SDK használatával, beleértve a következőket:

* Szövegtől függő és szöveg-független ellenőrzés
* Beszélő azonosítása a hangminták azonosításához a hangminta
* Hangprofilok törlése

A beszédfelismeréssel kapcsolatos fogalmak [áttekintését az áttekintő](speaker-recognition-overview.md) cikkben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> A Speaker Recognition jelenleg *csak* a régióban létrehozott Azure Speech-erőforrások esetében támogatott `westus` .

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET-keretrendszer <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `using` utasításokat a szkript elejére.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) . Ebben a példában egy [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) előfizetési kulcsot és egy régiót hoz létre. Emellett a cikk további részében is létrehozhat egy alapszintű, a különböző testreszabási beállításokkal módosítható egyszerű kiírási kódot.

Vegye figyelembe, hogy a régió úgy van beállítva, hogy az `westus` egyetlen támogatott régió a szolgáltatás számára.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Szövegtől függő ellenőrzés

Speaker Verification az a szabály, amely megerősíti, hogy egy beszélő ismert vagy **regisztrált** hangra illeszkedik. Az első **lépés a** hangprofil beléptetése, hogy a szolgáltatásnak van-e összehasonlítani a jövőbeli hangmintákkal. Ebben a példában egy **szövegtől függő** stratégia használatával regisztrálja a profilt, amelyhez a beléptetéshez és ellenőrzéshez egy adott pass-mondat szükséges. A támogatott pass-mondatok listáját a [dokumentációban](https://docs.microsoft.com/rest/api/speakerrecognition/) tekintheti meg.

Először hozza létre a következő függvényt az `Program` osztályban a hangprofil regisztrálásához.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Ebben a függvényben `await client.CreateProfileAsync()` valójában az új hangprofilt hozza létre. A létrehozást követően megadhatja, hogyan kívánja megadni a hangmintákat, így az ebben a `AudioConfig.FromDefaultMicrophoneInput()` példában szereplő hang rögzítése az alapértelmezett bemeneti eszközről történik. Ezután regisztrálja a hangmintákat egy `while` hurokban, amely nyomon követi a beléptetéshez szükséges minták számát és a szükséges értéket. Minden egyes iteráció során a `client.EnrollProfileAsync(profile, audioInput)` rendszer felszólítja, hogy beszéljen a pass-mondatot a mikrofonba, és adja hozzá a mintát a hangprofilhoz.

A beléptetés befejezése után a rendszer felszólítja, `await SpeakerVerify(config, profile, profileMapping)` hogy ellenőrizze az imént létrehozott profilt. Adjon hozzá egy másik függvényt a definiáláshoz `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

Ebben a függvényben átadja az `VoiceProfile` imént létrehozott objektumot egy modell inicializálásához az ellenőrzéshez. Ezután `await speakerRecognizer.RecognizeOnceAsync(model)` megkéri, hogy ismét beszéljen a pass-kifejezéssel, de ezúttal érvényesíti a hangprofilját, és egy hasonlósági pontszámot ad vissza a 0,0-1.0-tól kezdve. Az `result` objektum visszaadja a vagy értéket is `Accept` `Reject` , attól függően, hogy a pass-kifejezés egyezik-e.

Ezután módosítsa a `Main()` függvényt a létrehozott új függvények meghívásához. Azt is vegye figyelembe, hogy a `Dictionary<string, string>` függvény hívásai alapján létrehoz egy-t a hivatkozással. Ennek az az oka, hogy a szolgáltatás nem teszi lehetővé, hogy egy ember számára olvasható nevet hozzon létre `VoiceProfile` , és csak az adatmennyiséget tárolja az adatvédelem érdekében. A `VerificationEnroll` függvényben adja hozzá a szótárhoz egy bejegyzést az újonnan létrehozott azonosítóval, valamint egy szöveges névvel. Az alkalmazás-fejlesztési forgatókönyvekben, ahol egy ember által olvasható nevet kell megjeleníteni, **ezt a leképezést a szolgáltatásnak nem szabad tárolnia**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Futtassa a szkriptet, és a rendszer felszólítja, hogy beszéljen a *saját hangom* kifejezésről, és erősítse meg a regisztrációt, és még egy további időt az ellenőrzéshez. A visszaadott eredmény a hasonlósági pontszám, amelyet a saját egyéni küszöbértékek létrehozására használhat az ellenőrzéshez.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Szövegtől független ellenőrzés

A **szövegtől függő** hitelesítéssel szemben a **szövegtől független** ellenőrzés:

* Nem igényel egy bizonyos pass-mondatot, hogy elmondható legyen, bármit megadhat
* A nem igényel három hangmintát, *de a* teljes hang 20 másodpercet igényel

Készítsen pár egyszerű módosítást a `VerificationEnroll` függvényben a **szövegtől független** ellenőrzésre való váltáshoz. Először módosítsa az ellenőrzési típust a következőre: `VoiceProfileType.TextIndependentVerification` . Ezután módosítsa a `while` hurkot a nyomon követésre `result.RemainingEnrollmentsSpeechLength` , amely továbbra is felszólítja, hogy beszéljen arról, hogy a rendszer 20 másodpercet sem rögzített.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Futtassa újra a programot, és az ellenőrzési fázisban bármit beszéljen, mivel a pass-mondat megadása nem kötelező. A rendszer ismét a hasonlósági pontszámot adja vissza.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Beszélőazonosítás

A Speaker Identification segítségével megállapítható, hogy **ki** beszél a regisztrált hangok adott csoportjából. A folyamat nagyon hasonlít a **szövegtől független ellenőrzéshez**, és a fő különbség, hogy egyszerre több hangprofillal is képes ellenőrizni, nem pedig egyetlen profilon.

Hozzon létre egy függvényt `IdentificationEnroll` több hangprofil regisztrálásához. Az egyes profilok beléptetési folyamata megegyezik a beléptetési folyamattal, amely a **szövegtől független ellenőrzéshez**szükséges, és minden profilhoz 20 másodpercet igényel. Ez a függvény elfogadja a karakterláncok listáját `profileNames` , és létrehoz egy új hangprofilt a listában szereplő minden névhez. A függvény az objektumok listáját adja vissza `VoiceProfile` , amelyet a következő függvényben használ a beszélő azonosításához.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Hozza létre a következő függvényt `SpeakerIdentification` egy azonosító kérés elküldéséhez. Ebben a függvényben a **beszélő-ellenőrzési** kérelemhez képest a fő különbség a használata `SpeakerIdentificationModel.FromProfiles()` , amely elfogadja az objektumok listáját `VoiceProfile` . 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Módosítsa a `Main()` függvényt a következőre. Létrehoz egy listát a sztringekről `profileNames` , amelyet a `IdentificationEnroll()` függvénynek továbbít. Ez arra kéri, hogy hozzon létre egy új hangprofilt a listában szereplő minden névhez, így további neveket adhat hozzá a barátokhoz vagy munkatársakhoz tartozó további profilok létrehozásához.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Futtassa a szkriptet, és a rendszer felszólítja, hogy beszéljen a hangmintákat az első profilhoz. A regisztráció befejezését követően a rendszer felszólítja, hogy ismételje meg ezt a folyamatot a listában szereplő összes névhez `profileNames` . Az egyes regisztrációk befejezése után a rendszer felszólítja, hogy beszéljen **valakivel** , és a szolgáltatás megpróbálja azonosítani ezt a személyt a regisztrált Hangprofilok közül.

Ez a példa csak a legközelebbi egyezést adja vissza, és a hasonlósági pontszámot jeleníti meg, de a függvényhez való hozzáadásával teljes választ kaphat, amely az első öt hasonlóságot tartalmazza `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` `SpeakerIdentification` .

## <a name="changing-audio-input-type"></a>Hangbemenet típusának módosítása

A cikkben szereplő példák az alapértelmezett eszköz mikrofonját használják a hangmintákhoz bemenetként. Azonban olyan helyzetekben, ahol hangfájlokat kell használnia a mikrofon bemenete helyett, egyszerűen váltson át az összes példányára, `AudioConfig.FromDefaultMicrophoneInput()` `AudioConfig.FromWavFileInput(path/to/your/file.wav)` hogy átváltson egy fájl bemenetre. Vegyes bemeneteket is használhat, ha például egy mikrofont használ a regisztráláshoz és a fájlok ellenőrzéséhez.

## <a name="deleting-voice-profile-enrollments"></a>Hangprofil-regisztrációk törlése

A regisztrált profilok törléséhez használja a `DeleteProfileAsync()` függvényt az `VoiceProfileClient` objektumon. A következő példa azt mutatja be, hogyan törölhet hangprofilokat egy ismert hangprofil-AZONOSÍTÓból.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```

## <a name="next-steps"></a>További lépések

* Az osztályok és függvények részletes ismertetését az Speaker Recognition [dokumentációjában](https://docs.microsoft.com/rest/api/speakerrecognition/) találja.

* Lásd: [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) és [C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) minták a githubon.

