---
title: Kulcsszó-útmutató létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: f2f333cd01057ff5f6f904924880f88b0685c72f
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667693"
---
# <a name="get-started-with-custom-keyword"></a>Ismerkedés az egyéni kulcsszóval

Ebben a rövid útmutatóban megismerheti az egyéni kulcsszavakkal végzett munka alapjait a Speech Studio és a Speech SDK használatával. A kulcsszó egy szó vagy rövid kifejezés, amely lehetővé teszi, hogy a termék hangvezérelt legyen. Kulcsszavas modelleket hozhat létre a Speech Studióban, majd exportálhatja az alkalmazásaiban a Speech SDK-val használt modell-fájlt.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések egy beszédfelismerési előfizetést és a Speech SDK-t igénylik. Ha még nem rendelkezik előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free). Az SDK beszerzéséhez tekintse meg a platform [telepítési útmutatóját](quickstarts/setup-platform.md) .

## <a name="create-a-keyword-in-speech-studio"></a>Kulcsszó létrehozása a Speech Studióban

Egyéni kulcsszó használata előtt létre kell hoznia egy kulcsszót a [Speech Studio](https://aka.ms/sdsdk-speechportal) [Egyéni kulcsszavas](https://aka.ms/sdsdk-wakewordportal) oldalának használatával. A kulcsszó megadása után létrehoz egy `.table` fájlt, amelyet a SPEECH SDK-val használhat.

> [!IMPORTANT]
> Az egyéni kulcsszavas modellek és a létrejövő `.table` fájlok **csak** a Speech Studióban hozhatók létre.
> Nem hozhat létre egyéni kulcsszavakat az SDK-ból vagy REST-hívásokkal.

1. Lépjen a [Speech studióba](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha még nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)lehetőséget.

1. Az [egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon hozzon létre egy **új projektet**. 

1. Adjon meg egy **nevet**, egy opcionális **leírást**, és válassza ki a nyelvet. Nyelvenként egy projektre van szüksége, és a támogatás jelenleg a `en-US` nyelvre korlátozódik.

    ![A kulcsszavas projekt leírása](media/custom-keyword/custom-kws-portal-new-project.png)

1. Válassza ki a projektet a listából. 

    ![Válassza ki a kulcsszavas projektet](media/custom-keyword/custom-kws-portal-project-list.png)

1. Új kulcsszó-modell létrehozásához kattintson a **betanítási modell**elemre.

1. Adja meg a modell **nevét** , a választható **leírást**, valamint az Ön által választott **kulcsszót** , majd kattintson a **tovább**gombra. Tekintse meg a hatályos kulcsszó kiválasztására [vonatkozó útmutatást](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) .

    ![Adja meg a kulcsszót](media/custom-keyword/custom-kws-portal-new-model.png)

1. A portál a kulcsszóhoz jelölt kiejtéseket hoz létre. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el az összes helytelen kiejtés melletti ellenőrzéseket. Ha csak a jó kiejtéseket jelölte be, kattintson a **betanítás** gombra a Kulcsszóválasztó modell létrehozásának megkezdéséhez. 

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. A modell létrehozása akár harminc percet is igénybe vehet. A kulcsszó listája a **feldolgozástól** a **sikeres** modell befejeződése után módosul. Ezután letöltheti a fájlt.

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-download-model.png)

1. A letöltött fájl egy `.zip` Archívum. Bontsa ki az archívumot, és a kiterjesztéssel rendelkező fájl jelenik meg `.table` . Ezt a fájlt használja az SDK-val a következő szakaszban, ezért ügyeljen arra, hogy az elérési utat jegyezze fel. a fájl neve tükrözi a kulcsszava nevét, például az **eszköz aktiválása** elemnél a fájlnév `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Kulcsszavas modell használata az SDK-val

Először töltse be a kulcsszavas modellt tartalmazó fájlt a `FromFile()` statikus függvénnyel, amely egy értéket ad vissza `KeywordRecognitionModel` . Használja a `.table` Speech studióból letöltött fájl elérési útját. Emellett `AudioConfig` az alapértelmezett mikrofon használatával is létrehoz egy új példányt a `KeywordRecognizer` hangkonfiguráció használatával.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Ezután a kulcsszó-felismerés futtatása egy hívással történik `RecognizeOnceAsync()` a Model objektum átadásával. Ez elindít egy kulcsszó-felismerési munkamenetet, amely addig tart, amíg el nem ismeri a kulcsszót. Ezért ezt a kialakítási mintát általában többszálas alkalmazásokban vagy használati esetekben érdemes használni, ahol előfordulhat, hogy a rendszer határozatlan ideig várakozik.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Az itt látható példa a helyi kulcsszavas felismerést használja, mert nincs szükség `SpeechConfig` objektumra a hitelesítési környezethez, és nem lép kapcsolatba a háttérrel. A kulcsszó-felismerést és az ellenőrzést azonban [folyamatos háttérbeli kapcsolatok](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)használatával is futtathatja.

## <a name="next-steps"></a>Következő lépések

Tesztelje egyéni kulcsszavait a [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)rövid útmutatójában.
