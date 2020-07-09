---
title: Szöveg-beszéd hangok listázása, Python-Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját egy régió/végpont számára a Python használatával. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: tracking-python
ms.openlocfilehash: ec1b03bf0b3cf95f65013bddbc54e15ab985198e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607998"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>A szöveg-beszéd hangok listájának beolvasása a Python használatával

Ebből a cikkből megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját egy régió/végpont számára a Python használatával. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a [régiók](regions.md)című részben tekintheti meg.

Ehhez a cikkhez egy Speech Service-erőforrással rendelkező [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Python 2.7.x vagy 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>vagy a kedvenc szövegszerkesztője
* A beszédfelismerési szolgáltatáshoz tartozó Azure-előfizetési kulcs

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-voices.py` nevű fájlba a projektjében.

```python
import requests
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

A rendszer a szöveges és a beszédfelismerési szolgáltatás felé irányuló HTTP-kérelmekre vonatkozó kéréseket használ.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Adja meg az előfizetési kulcsot, és hozzon létre egy promptot a TTS számára

A következő néhány szakaszban létrehozhat metódusokat az engedélyezés kezeléséhez, a szöveg-beszéd API meghívásához és a válasz érvényesítéséhez. Kezdjük egy osztály létrehozásával. Itt tesszük a metódusokat a token Exchange-hez, és meghívjuk a szöveg-beszéd API-t.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Az a `subscription_key` Azure Portal egyedi kulcsa.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Ehhez a végponthoz hozzáférési jogkivonat szükséges a hitelesítéshez. Hozzáférési jogkivonat lekéréséhez Exchange szükséges. Ez a példa a beszédfelismerési szolgáltatás előfizetési kulcsát a végpont használatával cseréli egy hozzáférési jogkivonatra `issueToken` .

Ez a példa azt feltételezi, hogy a Speech Service-előfizetés az USA nyugati régiójában található. Ha más régiót használ, frissítse a értékét `fetch_token_url` . A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja ezt a kódot a `GetVoices` osztályba:

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

Itt fogja felépíteni a kérést, és menti a visszaadott hangok listáját. Először be kell állítania a és a `base_url` `path` . Ez a példa feltételezi, hogy az USA nyugati végpontját használja. Ha az erőforrás egy másik régióban van regisztrálva, győződjön meg róla, hogy frissíti a-t `base_url` . További információ: [Speech Service Regions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejléceket. Végül egy kérést küld a szolgáltatásnak. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a válasz fájlba lesz írva.

Másolja ezt a kódot a `GetVoices` osztályba:

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

Már majdnem kész. Utolsó lépésként hozza létre az osztályt, és hívja meg a függvényeket.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi, készen áll a minta futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szöveg – beszéd API-hivatkozás](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangbetűkészletek létrehozása](how-to-customize-voice-font.md)
* [Hangminták rögzítése egyéni hang létrehozásához](record-custom-voice-samples.md)
