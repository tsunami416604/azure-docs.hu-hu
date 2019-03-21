---
title: 'Gyors útmutató: Szöveg-beszéd átalakítás, Python - beszédszolgáltatások konvertálása'
titleSuffix: Azure Cognitive Services
description: Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Python és a szöveg-hang transzformációs REST API használatával. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 534473ccde52af69b6dad1e84ef77da326d29d5c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872567"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Gyors útmutató: Átalakítás szöveg-hang transzformációs Python használatával

Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs Python és a szöveg-hang transzformációs REST API használatával. Ebben az útmutatóban a kérelem törzsében van strukturálva, [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md), amely lehetővé teszi, hogy a válasz nyelv és válassza ki.

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatások erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetés kulcsot a beszédszolgáltatások

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `tts.py` nevű fájlba a projektjében.

```python
import os, requests, time
from xml.etree import ElementTree
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

Ezek a modulok a speech választ, és a egy időbélyeg fájlba írni, hozza létre a HTTP-kérés és az szöveg-hang transzformációs API segítségével.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Állítsa az előfizetési kulcsot, és hozzon létre egy kérdés Szövegfelolvasás

A következő néhány szakaszban módszerek engedélyezés kezeléséhez, az szöveg-hang transzformációs API-t és érvényesíteni a választ fog létrehozni. Kezdésnek adja hozzá egy kódrészletet, amely biztosítja, hogy ez a minta úgy működik, a Python 2.7.x és 3.x.

```python
try: input = raw_input
except NameError: pass
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

A `subscription_key` az egyedi kulcs, az Azure Portalról. `tts` kéri a felhasználót, adja meg a szöveg, amely a speech lesz átalakítva. A bemeneti karakterláncot,, így nem kell karaktereket escape-karakterrel kell. Végül `timestr` lekérdezi az aktuális időpontot, amely segítségével a fájl neve.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a minta kicserél egy hozzáférési token használatával beszédszolgáltatások előfizetői azonosítóját a `issueToken` végpont.

Ez a minta azt feltételezi, hogy a Speech Services-előfizetés az USA nyugati régiójában. Ha egy másik régiót használ, módosítsa a `fetch_token_url`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja be ezt a kódot a `TextToSpeech` osztály:

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
> A hitelesítés további információkért lásd: [hitelesítés hozzáférési jogkivonatot a](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog hozhat létre a kérést, és mentse a speech választ. Először be kell a `base_url` és `path`. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `base_url`. További információkért lásd: [beszédszolgáltatások régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Következő lépésként hozzá kell a kéréshez szükséges fejlécek. Győződjön meg arról, hogy frissítenie `User-Agent` az erőforrás (az Azure Portalon található), és a készlet nevére `X-Microsoft-OutputFormat` , az előnyben részesített hangkimeneti. Kimeneti formátumok teljes listáját lásd: [hang kimenete](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Ezután hozhat létre a kérelem törzsében Speech összefoglaló Markup Language (SSML) használatával. Ez a példa a struktúrát határozza meg, és használja a `tts` adjon meg a korábban létrehozott.

>[!NOTE]
> Ebben a példában a `ZiraRUS` hangtípusú. A Microsoft teljes listáját adott beszédhangot/nyelvek, lásd: [nyelvi támogatás](language-support.md).
> Ha érdekli a saját márkáját egy egyedi, könnyen felismerhető névre hangalapú létrehozása, [létrehozása egyéni hangtípust](how-to-customize-voice-font.md).

Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és egy 200 állapotkódot adott vissza, a beszéd válasz van egy időbélyegzővel fájlba írni.

Másolja be ezt a kódot a `TextToSpeech` osztály:

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
    voice.set('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
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

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a szöveg-hang transzformációs mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
python tts.py
```

Amikor a rendszer kéri, írjon be bármilyen kívánt szöveg-hang transzformációs konvertálása. Ha ez sikeres, a beszéd fájl található a projektmappába. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megtekintése a Githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
