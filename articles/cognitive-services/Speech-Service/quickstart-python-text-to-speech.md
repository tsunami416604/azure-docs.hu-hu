---
title: Szöveg-beszéd átalakítás, Python-Speech szolgáltatás konvertálása
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Python és a szöveg-beszéd REST API használatával. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 63e6a2a47265eae08a653f3eadaf6bad86dd0635
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119712"
---
# <a name="convert-text-to-speech-using-python"></a>Szöveg-beszéd átalakítás a Python használatával

Ebből a cikkből megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Python és a szöveg-beszéd REST API használatával. Az útmutatóban szereplő kérelem törzse a [Speech szintézis Markup Language (SSML) nyelvre](speech-synthesis-markup.md)van strukturálva, amely lehetővé teszi a válasz hangjának és nyelvének kiválasztását.

Ehhez a cikkhez egy Speech Service-erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7.x vagy 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy a kedvenc szövegszerkesztője
* A beszédfelismerési szolgáltatáshoz tartozó Azure-előfizetési kulcs

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `tts.py` nevű fájlba a projektjében.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

Ezek a modulok a speech választ, és a egy időbélyeg fájlba írni, hozza létre a HTTP-kérés és az szöveg-hang transzformációs API segítségével.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Állítsa az előfizetési kulcsot, és hozzon létre egy kérdés Szövegfelolvasás

A következő néhány szakaszban módszerek engedélyezés kezeléséhez, az szöveg-hang transzformációs API-t és érvényesíteni a választ fog létrehozni. Kezdésnek adja hozzá egy kódrészletet, amely biztosítja, hogy ez a minta úgy működik, a Python 2.7.x és 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Következő lépésként hozzunk létre egy osztályt. Ez az, ahol elhelyezzük a jogkivonatcsere, és a szöveg-hang transzformációs API meghívására szolgáló módszerek.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

A `subscription_key` a Azure Portal egyedi kulcsa. `tts` megkéri a felhasználót, hogy adjon meg szöveget, amelyet a rendszer a beszédre konvertál. A bemeneti karakterláncot,, így nem kell karaktereket escape-karakterrel kell. Végül `timestr` beolvassa az aktuális időt, amelyet a fájl elnevezésére fogunk használni.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a példa a beszédfelismerési szolgáltatás előfizetési kulcsát egy hozzáférési jogkivonat számára a `issueToken` végpont használatával cseréli.

Ez a példa azt feltételezi, hogy a Speech Service-előfizetés az USA nyugati régiójában található. Ha más régiót használ, frissítse a `fetch_token_url`értékét. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja ezt a kódot a `TextToSpeech` osztályba:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférési jogkivonattal](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog hozhat létre a kérést, és mentse a speech választ. Először be kell állítania a `base_url` és a `path`. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióban van regisztrálva, ellenőrizze, hogy a `base_url`frissíti-e. További információ: [Speech Service Regions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Következő lépésként hozzá kell a kéréshez szükséges fejlécek. Győződjön meg arról, hogy a `User-Agent` az erőforrás nevével frissíti (a Azure Portalban található), és állítsa `X-Microsoft-OutputFormat`t az előnyben részesített hang kimenetére. A kimeneti formátumok teljes listáját itt tekintheti meg: [hang kimenetek](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután hozhat létre a kérelem törzsében Speech összefoglaló Markup Language (SSML) használatával. Ez a minta határozza meg a struktúrát, és a korábban létrehozott `tts` bemenetet használja.

>[!NOTE]
> Ez a példa a `Guy24KRUS` hangbetűkészletet használja. A Microsoft által biztosított hangok és nyelvek teljes listájáért lásd a [nyelvi támogatást](language-support.md)ismertető témakört.
> Ha szeretne létrehozni egy egyedi, felismerhető hangot a márka számára, tekintse meg az [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)című témakört.

Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és egy 200 állapotkódot adott vissza, a beszéd válasz van egy időbélyegzővel fájlba írni.

Másolja ezt a kódot a `TextToSpeech` osztályba:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés, hogy az osztály példányosítható, és a függvényeket.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

Ennyi az egész, készen áll a szöveg-hang transzformációs mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
python tts.py
```

Amikor a rendszer kéri, írjon be bármilyen kívánt szöveg-hang transzformációs konvertálása. Ha ez sikeres, a beszéd fájl található a projektmappába. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [A Python és a Speech SDK használata szöveg-beszéd átalakításra](quickstarts/speech-to-text-from-microphone.md)
* [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
