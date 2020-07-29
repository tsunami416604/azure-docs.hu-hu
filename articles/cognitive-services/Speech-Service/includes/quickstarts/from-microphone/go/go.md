---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 99540e8518aa31a2221844aa954ff665609d8217
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375770"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="setup-your-environment"></a>A környezet beállítása

A go. mod fájl frissítése a legújabb SDK-verzióra a sor hozzáadásával
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal
1. Cserélje le a forrásfájl tartalmát (például `sr-quickstart.go` ) az alábbira, amely a következőket tartalmazza:

- "fő" csomag definíciója
- a szükséges modulok importálása a Speech SDK-ból
- a rövid útmutató későbbi részében lecserélt előfizetési adatok tárolására szolgáló változók
- egyszerű implementáció a mikrofon használatával hangbemenethez
- eseménykezelők különböző eseményekhez, amelyek a beszédfelismerés során megtörténik

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Cserélje le a `YOUR_SUBSCRIPTION_KEY` és az `YOUR_SUBSCRIPTIONKEY_REGION` értékeket a tényleges értékekre a beszédfelismerési erőforrásból.

- Navigáljon a Azure Portal, és nyissa meg a beszédfelismerési erőforrást
- A bal oldali **kulcsok** alatt két elérhető előfizetési kulcs létezik
    - `YOUR_SUBSCRIPTION_KEY`Érték helyett használja a értéket
- A bal oldali **Áttekintés** alatt jegyezze fel a régiót, és rendelje hozzá a régió-azonosítóhoz
- Használja a régió azonosítóját `YOUR_SUBSCRIPTIONKEY_REGION` érték helyett, például: `"westus"` **USA nyugati** régiója

## <a name="code-explanation"></a>Kód magyarázata
A beszédfelismerési konfigurációs objektum létrehozásához a Speech előfizetési kulcs és a régió szükséges. A beszédfelismerési objektum létrehozásához a konfigurációs objektum szükséges.

A felismerő példány több módszert tesz elérhetővé a beszéd felismeréséhez. Ebben a példában a beszédet folyamatosan ismerjük. Ez a funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy sok kifejezést küld az elismerésre, és ha a program leáll, a beszédfelismerés felismerése leáll. Az eredmények kiértékelése után a kód a konzolba írja azokat.

## <a name="build-and-run"></a>Buildelés és futtatás
Most már beállította a projekt felépítését és a beszédfelismerés tesztelését a beszédfelismerési szolgáltatás használatával.
1. Hozza létre a projektet, például: **"Go build"**
2. Futtassa a modult, és beszéljen egy mondattal vagy mondattal az eszköz mikrofonjában. A beszéd a beszédfelismerési szolgáltatáshoz lett továbbítva, és szövegbe kerül, amely megjelenik a kimenetben.


> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .


## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
