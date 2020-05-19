---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 1a1207b056a0cd86d1855d990a8857ef8d9752e4
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82979575"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="create-a-new-website-folder"></a>Új webhelymappa létrehozása

Hozzon létre egy új, üres mappát. Abban az esetben, ha a mintát egy webkiszolgálón szeretné futtatni, győződjön meg arról, hogy a webkiszolgáló eléri a mappát.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A JavaScripthez készült Speech SDK kicsomagolása a mappába

Töltse le a Speech SDK-t [.zip-csomagként](https://aka.ms/csspeech/jsbrowserpackage), és csomagolja ki az újonnan létrehozott mappába. Ennek eredményeképpen a rendszer két fájlt csomagol ki, `microsoft.cognitiveservices.speech.sdk.bundle.js` és `microsoft.cognitiveservices.speech.sdk.bundle.js.map` .
Az utóbbi fájl nem kötelező, és az SDK-kódban való hibakereséshez hasznos.

## <a name="create-an-indexhtml-page"></a>Az index.html lap létrehozása

Hozzon létre egy új fájlt a mappában `index.html` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

1. Hozza létre a következő HTML-vázat:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-mic.html)]

## <a name="create-the-token-source-optional"></a>A jogkivonatforrás létrehozása (nem kötelező)

Ha egy webkiszolgálón szeretné futtatni a weblapot, opcionálisan megadhat egy jogkivonatforrást a bemutató alkalmazáshoz.
Így az előfizetői azonosítója soha nem fog kikerülni a kiszolgálóról, közben viszont a felhasználóknak úgy teheti lehetővé a beszédfelismerési képességek használatát, hogy ők maguk semmilyen engedélyezési kódot nem adnak meg.

Hozzon létre egy új fájlt `token.php` néven. Ebben a példában feltételezzük, hogy a webkiszolgáló támogatja a PHP parancsnyelv használatát. Írja be a következő kódot:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Az engedélyezési jogkivonatok csupán korlátozott élettartammal rendelkeznek.
> Ez az egyszerűsített példa nem mutatja be, hogyan lehet frissíteni az engedélyezési jogkivonatokat. Felhasználóként manuálisan betöltheti az oldalt újból, vagy megnyomhatja az F5 billentyűt a frissítéshez.

## <a name="build-and-run-the-sample-locally"></a>A minta helyi buildelése és futtatása

Az alkalmazás elindításához kattintson duplán az index.html fájlra, vagy nyissa meg az index.html fájlt kedvenc webböngészőjében. Egy egyszerű grafikus felhasználói felület fog megjelenni, amelyen megadhatja az előfizetői azonosítót és a [régiót](../../../../regions.md), majd a mikrofon használatával aktiválhatja a felismerést.

> [!NOTE]
> Ez a metódus nem működik a Safari böngészőben.
> A Safari webhelyen a minta weblapot webkiszolgálón kell üzemeltetni; A Safari nem teszi lehetővé a helyi fájlokból betöltött webhelyek használatát a mikrofon használatához.

## <a name="build-and-run-the-sample-via-a-web-server"></a>A minta buildelése és futtatása webkiszolgáló használatával

Az alkalmazás elindításához nyissa meg kedvenc webböngészőjét, és mutasson a nyilvános URL-címre, amelyen a mappát tárolja, adja meg a [régiót](../../../../regions.md), majd a mikrofon használatával aktiválja a felismerést. Ha konfigurálva van, az beszerez egy jogkivonatot a jogkivonatforrásból.


## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

