---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: 7cebceed534531849e46e527e470bfc41b03348a
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377086"
---
A beszédfelismerési szolgáltatás egyik fő funkciója az emberi beszéd (más néven beszéd – szöveg) felismerése és átírása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben a kiváló minőségű beszéd-szöveg átalakítás elvégzéséhez.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a [Gyorsindítás példák](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer) a githubon című részt.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Go-hoz készült [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet%2Cwindows%2Cjre%2Cbrowser&pivots=programming-language-go)-t.

## <a name="speech-to-text-from-microphone"></a>Beszéd – szöveg a mikrofonból

Az alábbi mintakód használatával futtathatja a beszédfelismerést az alapértelmezett eszköz mikrofonból. Cserélje le a változókat `subscription` és az `region` előfizetéssel és a régió kulcsaival. A szkript futtatásával elindít egy felismerési munkamenetet az alapértelmezett mikrofonon és a kimeneti szövegen.

```go
import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

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
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

A [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.13.0/speech#SpeechConfig) és osztályokkal kapcsolatos részletes információkért tekintse meg a dokumentációs dokumentációt [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.13.0/speech#SpeechRecognizer) .

## <a name="speech-to-text-from-audio-file"></a>Beszéd és szöveg közötti hangfájlból

A következő minta használatával futtasson beszédfelismerést egy hangfájlból. Cserélje le a változókat `subscription` és az `region` előfizetéssel és a régió kulcsaival. Továbbá cserélje le a változót egy `file` . wav-fájl elérési útjára. A szkript futtatása felismeri a fájl beszédét, és kiírja a szöveg eredményét.

```go
import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
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
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

A [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.13.0/speech#SpeechConfig) és osztályokkal kapcsolatos részletes információkért tekintse meg a dokumentációs dokumentációt [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.13.0/speech#SpeechRecognizer) .