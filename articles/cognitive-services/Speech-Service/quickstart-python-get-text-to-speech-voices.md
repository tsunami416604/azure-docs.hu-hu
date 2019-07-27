---
title: 'Gyors útmutató: Szöveg-beszéd hangok listázása, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját egy régió/végpont számára a Python használatával. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ac96c3ce3924b8b2fe834e2b350e95ce23c52e1f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559344"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Gyors útmutató: A szöveg-beszéd hangok listájának beolvasása a Python használatával

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a standard és a neurális hangok teljes listáját egy régió/végpont számára a Python használatával. A rendszer JSON-ként adja vissza a listát, és a hang elérhetősége régiónként változik. A támogatott régiók listáját a [régiók](regions.md)című részben tekintheti meg.

Ehhez a rövid útmutatóhoz egy Speech Services-erőforrással rendelkező [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* A Speech Serviceshez tartozó Azure-előfizetési kulcs

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-voices.py` nevű fájlba a projektjében.

```python
import requests
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

A rendszer a szöveges és a beszédfelismerési szolgáltatás felé irányuló HTTP-kérelmekre vonatkozó kéréseket használ.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Állítsa az előfizetési kulcsot, és hozzon létre egy kérdés Szövegfelolvasás

A következő néhány szakaszban módszerek engedélyezés kezeléséhez, az szöveg-hang transzformációs API-t és érvényesíteni a választ fog létrehozni. Kezdjük egy osztály létrehozásával. Ez az, ahol elhelyezzük a jogkivonatcsere, és a szöveg-hang transzformációs API meghívására szolgáló módszerek.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

A `subscription_key` az egyedi kulcs, az Azure Portalról.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Ehhez a végponthoz hozzáférési jogkivonat szükséges a hitelesítéshez. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a példa a Speech Services előfizetési kulcsát egy hozzáférési jogkivonat számára `issueToken` a végpont használatával cseréli.

Ez a példa feltételezi, hogy a Speech Services-előfizetése az USA nyugati régiójában található. Ha egy másik régiót használ, módosítsa a `fetch_token_url`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Másolja be ezt a kódot a `GetVoices` osztály:

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
> A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés hozzáférési](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)jogkivonattal.

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Itt fogja felépíteni a kérést, és menti a visszaadott hangok listáját. Először be kell a `base_url` és `path`. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `base_url`. További információ: [Speech Services-régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejléceket. Végül paritásadatok egy kérelmet a szolgáltatáshoz. Ha a kérelem sikeres, és a rendszer egy 200 állapotkódot ad vissza, a válasz fájlba lesz írva.

Másolja be ezt a kódot a `GetVoices` osztály:

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

Már majdnem kész. Az utolsó lépés, hogy az osztály példányosítható, és a függvényeket.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi, készen áll a minta futtatására. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
