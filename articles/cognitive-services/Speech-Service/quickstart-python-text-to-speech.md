---
title: 'Gyors útmutató: szöveg-beszéd átalakítás, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Python és a szöveg-beszéd REST API használatával. Az útmutatóban foglalt mintaszöveg a Speech szintézis Markup Language (SSML) nyelvre van strukturálva. Ez lehetővé teszi a beszédfelismerési válasz hangjának és nyelvének kiválasztását.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 23f3a6b030b477d3dcc06317a545064da95100ff
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816208"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Gyors útmutató: szöveg-beszéd átalakítás a Python használatával

Ebből a rövid útmutatóból megtudhatja, hogyan alakíthatja át a szöveg – beszéd szöveget a Python és a szöveg-beszéd REST API használatával. Az útmutatóban szereplő kérelem törzse a [Speech szintézis Markup Language (SSML) nyelvre](speech-synthesis-markup.md)van strukturálva, amely lehetővé teszi a válasz hangjának és nyelvének kiválasztását.

Ehhez a rövid útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

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

Ezek a modulok a beszédre adott időbélyegzővel rendelkező fájlokra, a HTTP-kérések összeállítására és a szöveg – beszéd API meghívására szolgálnak.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Adja meg az előfizetési kulcsot, és hozzon létre egy promptot a TTS számára

A következő néhány szakaszban létrehozhat metódusokat az engedélyezés kezeléséhez, a szöveg-beszéd API meghívásához és a válasz érvényesítéséhez. Kezdjük egy olyan kód hozzáadásával, amely gondoskodik arról, hogy ez a minta a Python 2.7. x és 3. x verzióban működjön.

```python
try:
    input = raw_input
except NameError:
    pass
```

Ezután hozzon létre egy osztályt. Itt tesszük a metódusokat a token Exchange-hez, és meghívjuk a szöveg-beszéd API-t.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

A `subscription_key` a Azure Portal egyedi kulcsa. `tts` megkéri a felhasználót, hogy adjon meg szöveget, amelyet a rendszer a beszédre konvertál. Ez a bemeneti karakterlánc, ezért nem kell megszöknie a karaktereket. Végül `timestr` beolvassa az aktuális időt, amelyet a fájl elnevezésére fogunk használni.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-beszéd REST APIhoz hozzáférési jogkivonat szükséges a hitelesítéshez. Hozzáférési jogkivonat lekéréséhez Exchange szükséges. Ez a példa a beszédfelismerési szolgáltatás előfizetési kulcsát egy hozzáférési jogkivonat számára a `issueToken` végpont használatával cseréli.

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

## <a name="make-a-request-and-save-the-response"></a>Kérelem elkészítése és a válasz mentése

Itt fogja felépíteni a kérést, és menteni a beszédfelismerési választ. Először be kell állítania a `base_url` és a `path`. Ez a példa feltételezi, hogy az USA nyugati végpontját használja. Ha az erőforrás egy másik régióban van regisztrálva, ellenőrizze, hogy a `base_url`frissíti-e. További információ: [Speech Service Regions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután hozzá kell adnia a kéréshez szükséges fejléceket. Győződjön meg arról, hogy a `User-Agent` az erőforrás nevével frissíti (a Azure Portalban található), és állítsa `X-Microsoft-OutputFormat`t az előnyben részesített hang kimenetére. A kimeneti formátumok teljes listáját itt tekintheti meg: [hang kimenetek](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután hozza létre a kérelem törzsét a Speech szintézis Markup Language (SSML) használatával. Ez a minta határozza meg a struktúrát, és a korábban létrehozott `tts` bemenetet használja.

>[!NOTE]
> Ez a példa a `Guy24KRUS` hangbetűkészletet használja. A Microsoft által biztosított hangok és nyelvek teljes listájáért lásd a [nyelvi támogatást](language-support.md)ismertető témakört.
> Ha szeretne létrehozni egy egyedi, felismerhető hangot a márka számára, tekintse meg az [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)című témakört.

Végül egy kérést küld a szolgáltatásnak. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a beszédfelismerési válasz egy időbélyeg-fájlba íródik.

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

Már majdnem kész. Utolsó lépésként hozza létre az osztályt, és hívja meg a függvényeket.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a szöveg-beszéd minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
python tts.py
```

Ha a rendszer kéri, írja be a szöveget, amit szeretne szöveggé konvertálni. Ha a művelet sikeres, a beszédfelismerési fájl a Project mappában található. Játssza le kedvenc médialejátszó használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még:

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [A Python és a Speech SDK használata szöveg-beszéd átalakításra](quickstarts/speech-to-text-from-microphone.md)
* [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
