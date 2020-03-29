---
title: Szövegfelolvasás, Python – Beszédszolgáltatás konvertálása
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan konvertálhatja a szövegfelolvasást a Python és a Text-to-Speech REST API használatával. Az útmutatóban szereplő mintaszöveg beszédszintetizáló nyelvi (SSML) formában van felépítve. Ez lehetővé teszi a beszédválasz hangjának és nyelvének kiválasztását.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 79562049f48ba90a4f9a123919185521a82d7be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365799"
---
# <a name="convert-text-to-speech-using-python"></a>Szövegfelolvasás konvertálása Pythonnal

Ebből a cikkből megtudhatja, hogyan konvertálhatja a szövegfelolvasást a Python és a text-to-speech REST API használatával. Az útmutatóban szereplő kérelemtörzs [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)néven jelenik meg, amely lehetővé teszi a válasz hangjának és nyelvének kiválasztását.

Ez a cikk egy [Azure Cognitive Services-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) igényel egy beszédszolgáltatás-erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7.x vagy 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy kedvenc szövegszerkesztő
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz

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

Ezek a modulok egy időbélyeggel rendelkező fájl beszédválaszának írására, a HTTP-kérelem létrehozásához és a szövegfelolvasó API-hívásához használatosak.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Az előfizetési kulcs beállítása és a TTS kérdésének létrehozása

A következő néhány szakaszban az engedélyezés kezelésére, a szövegfelolvasó API-hívására és a válasz érvényesítésére szolgáló módszereket hoz létre. Kezdjük azzal, hogy hozzáad néhány kódot, amely biztosítja, hogy ez a minta működni fog a Python 2.7.x és a 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Ezután hozzunk létre egy osztályt. Itt fogjuk helyezni a tokencsere és a szöveg-beszéd API hívásának módszereit.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

Az `subscription_key` az Ön egyedi kulcsa az Azure Portalról. `tts`kéri a felhasználót, hogy írja be a beszédmé konvertálandó szöveget. Ez a bevitel egy karakterlánc-konstans, így a karaktereket nem kell megkerülni. Végül `timestr` megkapja az aktuális időt, amelyet a fájl elnevezésére használunk.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-beszéd REST API hitelesítéshez hozzáférési jogkivonatot igényel. A hozzáférési jogkivonat beszerezéséhez cserére van szükség. Ez a minta a beszédfelismerési szolgáltatás `issueToken` előfizetési kulcsát cseréli ki egy hozzáférési jogkivonathoz a végpont használatával.

Ez a minta feltételezi, hogy a beszédfelismerési szolgáltatás előfizetése az USA nyugati régiójában található. Ha másik régiót használ, frissítse a `fetch_token_url`értékét. A teljes listát a [Régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja a kódot `TextToSpeech` az osztályba:

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
> A hitelesítéssel kapcsolatos további információkért lásd: [Hitelesítés hozzáférési jogkivonattal](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Kérés kérése és mentése

Itt fogja építeni a kérelmet, és mentse a beszéd választ. Először be kell `base_url` állítania a és `path`a . Ez a minta feltételezi, hogy a nyugat-amerikai végpontot használja. Ha az erőforrás egy másik régióban van `base_url`regisztrálva, győződjön meg arról, hogy frissíti a . További információt a [Beszédszolgáltatási területek című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Ezután hozzá kell adnia a szükséges fejléceket a kérelemhez. Győződjön meg `User-Agent` arról, hogy frissíti az erőforrás nevét (az Azure Portalon található), és állítsa be `X-Microsoft-OutputFormat` a kívánt hangkimenetre. A kimeneti formátumok teljes listáját a [Hangkimenetek](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ezután készítse el a kérelem törzsét a beszédszintetizáló nyelvi (SSML) használatával. Ez a minta határozza meg `tts` a szerkezetet, és a korábban létrehozott bemenetet használja.

>[!NOTE]
> Ez a `Guy24kRUS` minta a hangbetűtípust használja. A Microsoft által biztosított hangok/nyelvek teljes listáját a Nyelvi támogatás című témakörben [tetszése idáig című](language-support.md)témakörben található.
> Ha egyedi, felismerhető hangot szeretne létrehozni a márkához, olvassa el [az Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md).

Végül, akkor, hogy egy kérelmet a szolgáltatás. Ha a kérelem sikeres, és egy 200-as állapotkódot ad vissza, a beszédválasz egy időbélyeggel ellátott fájlba kerül.

Másolja a kódot `TextToSpeech` az osztályba:

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
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
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

Már majdnem kész. Az utolsó lépés az, hogy példányosítani az osztály, és hívja a funkciókat.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a szövegfelolvasó mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
python tts.py
```

Amikor a rendszer kéri, írja be, amit szövegből beszédbe szeretne konvertálni. Ha sikeres, a beszédfájl a projekt mappájában található. Játssza le kedvenc médialejátszójával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták felfedezése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szövegfelolvasó API-hivatkozások](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [A Python és a Speech SDK használata szövegfelolvasáshoz](quickstarts/speech-to-text-from-microphone.md)
* [Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
