---
title: Szövegfelolvasó hangok listázása, Python – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megtudhatja, hogyan szerezheti be a régió/végpont standard és neurális hangok teljes listáját a Python használatával. A lista JSON-ként jelenik meg, és a hang elérhetősége régiónként változik.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 51fe6cea80e097f34432ab8dc7293c758bd8d720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77119805"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>A szövegfelolvasó hangok listájának beolvasása python használatával

Ebben a cikkben megtudhatja, hogyan szerezheti be a régió/végpont standard és neurális hangok teljes listáját a Python használatával. A lista JSON-ként jelenik meg, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a Régiók című [témakörben található.](regions.md)

Ez a cikk egy [Azure Cognitive Services-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) igényel egy beszédszolgáltatás-erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7.x vagy 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy kedvenc szövegszerkesztő
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-voices.py` nevű fájlba a projektjében.

```python
import requests
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

A kérelmek a szövegfelolvasó szolgáltatásHTTP-kérelmeihez használatosak.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Az előfizetési kulcs beállítása és a TTS kérdésének létrehozása

A következő néhány szakaszban az engedélyezés kezelésére, a szövegfelolvasó API-hívására és a válasz érvényesítésére szolgáló módszereket hoz létre. Kezdjük azzal, hogy létrehozunk egy osztályt. Itt fogjuk helyezni a tokencsere és a szöveg-beszéd API hívásának módszereit.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Az `subscription_key` az Ön egyedi kulcsa az Azure Portalról.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Ehhez a végponthoz hozzáférési jogkivonat szükséges a hitelesítéshez. A hozzáférési jogkivonat beszerezéséhez cserére van szükség. Ez a minta a beszédfelismerési szolgáltatás `issueToken` előfizetési kulcsát cseréli ki egy hozzáférési jogkivonathoz a végpont használatával.

Ez a minta feltételezi, hogy a beszédfelismerési szolgáltatás előfizetése az USA nyugati régiójában található. Ha másik régiót használ, frissítse a `fetch_token_url`értékét. A teljes listát a [Régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja a kódot `GetVoices` az osztályba:

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

Itt fogja felépíteni a kérelmet, és mentse a visszaküldött hangok listáját. Először be kell `base_url` állítania a és `path`a . Ez a minta feltételezi, hogy a nyugat-amerikai végpontot használja. Ha az erőforrás egy másik régióban van `base_url`regisztrálva, győződjön meg arról, hogy frissíti a . További információt a [Beszédszolgáltatási területek című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Ezután adja hozzá a szükséges fejléceket a kérelemhez. Végül, akkor, hogy egy kérelmet a szolgáltatás. Ha a kérelem sikeres, és egy 200-as állapotkódot ad vissza, a válasz fájlba kerül.

Másolja a kódot `GetVoices` az osztályba:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Már majdnem kész. Az utolsó lépés az, hogy példányosítani az osztály, és hívja a funkciókat.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen állsz a minta futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták felfedezése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szövegfelolvasó API-hivatkozások](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűtípusok létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
