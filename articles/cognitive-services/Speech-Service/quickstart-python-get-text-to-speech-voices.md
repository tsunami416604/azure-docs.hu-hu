---
title: 'Gyors útmutató: Szöveg-hang transzformációs beszédhangot, Python - beszédszolgáltatások listázása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megismerheti, hogyan beolvasni a standard és a Neurális beszédhangot teljes listáját a régió/végpont Python használatával fogjuk. A listát ad vissza JSON-fájlként, és a hangalapú rendelkezésre állása régiónként eltérő.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: erhopf
ms.openlocfilehash: 66bda68b1313a7c172e273671bc3a03503d08e0d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876580"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Gyors útmutató: Szöveg-hang transzformációs beszédhangot pythonnal listájának beolvasása

Ebben a rövid útmutatóban megismerheti, hogyan beolvasni a standard és a Neurális beszédhangot teljes listáját a régió/végpont Python használatával fogjuk. A listát ad vissza JSON-fájlként, és a hangalapú rendelkezésre állása régiónként eltérő. Támogatott régiók listáját lásd: [régiók](regions.md).

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatások erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](get-started.md) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetés kulcsot a beszédszolgáltatások

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `get-voices.py` nevű fájlba a projektjében.

```python
import requests
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

Kérelem HTTP-kérelmekre a szöveg-hang transzformációs szolgáltatáshoz használható.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Állítsa az előfizetési kulcsot, és hozzon létre egy kérdés Szövegfelolvasás

A következő néhány szakaszban módszerek engedélyezés kezeléséhez, az szöveg-hang transzformációs API-t és érvényesíteni a választ fog létrehozni. Először hozzon létre egy osztályt. Ez az, ahol elhelyezzük a jogkivonatcsere, és a szöveg-hang transzformációs API meghívására szolgáló módszerek.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

A `subscription_key` az egyedi kulcs, az Azure Portalról.

## <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Ezt a végpontot egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a minta kicserél egy hozzáférési token használatával beszédszolgáltatások előfizetői azonosítóját a `issueToken` végpont.

Ez a minta azt feltételezi, hogy a Speech Services-előfizetés az USA nyugati régiójában. Ha egy másik régiót használ, módosítsa a `fetch_token_url`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> A hitelesítés további információkért lásd: [hitelesítés hozzáférési jogkivonatot a](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Kérés és a válasz mentése

Ide fog hozhat létre a kérést, és mentse a visszaadott beszédhangot listáját. Először be kell a `base_url` és `path`. Ez a példa feltételezi, hogy az USA nyugati RÉGIÓJA végpont használata. Ha az erőforrás egy másik régióba regisztrálva van, ellenőrizze, hogy frissíti a `base_url`. További információkért lásd: [beszédszolgáltatások régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ezután adja hozzá a kéréshez szükséges fejlécek. Végül paritásadatok egy kérelmet a szolgáltatáshoz. A kérelem sikeres, és egy 200 állapotkódot adott vissza, ha a válasz írt fájlt.

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
            print("\nStatus code: " + str(response.status_code) + "\nvoices.json is ready to view.\n")
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

Ennyi az egész, készen áll a minta futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megtekintése a Githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
