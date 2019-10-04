---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9c7385d3457f3f5dbed2633c20445bb9ef0b1638
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906878"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a következő kódrészletet egy `sentence-length.py` nevű fájlba a projektjében.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests uuid`.

Az első megjegyzés arra utasítja a Python-értelmezőt, hogy UTF-8 kódolást használjon. A rendszer importálja azokat a modulokat, amelyek az előfizetői azonosító egy környezeti változóból való beolvasásához, a HTTP-kérelem felépítéséhez, egy egyedi azonosító létrehozásához, illetve a Translator Text API által visszaadott JSON-válasz kezeléséhez szükségesek.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Az előfizetési kulcs, a végpont és az elérési út beállítása

Ez a minta megpróbálja beolvasni a Translator Text előfizetési kulcsot és a végpontot a környezeti `TRANSLATOR_TEXT_KEY` változóktól: és `TRANSLATOR_TEXT_ENDPOINT`. Ha nem ismeri a környezeti változókat, beállíthatja `subscription_key` és `endpoint` karakterláncként is megadhatja a feltételes utasításokat.

Másolja a projektbe a következő kódot:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

A Translator Text globális végpont beállítása `endpoint`. A `path` tulajdonság a `breaksentence` útvonalat állítja be, és meghatározza, hogy a 3-as API-verziót szeretnénk használni.

Ebben a példában a `params` a megadott szöveg nyelvének beállítására szolgál. A `breaksentence` nem szükséges a `params` útvonalhoz. Ha kihagyja a kérésből, az API megpróbálja felismerni a megadott szöveg nyelvét, és ezt az információt a megbízhatósági pontszámmal együtt adja meg a válaszban.

>[!NOTE]
> További információ a végpontokról, az útvonalakról és a kérelmek paraméteréről [: Translator Text API 3,0: Nyelvek](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
path = '/breaksentence?api-version=3.0'
params = '&language=en'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Fejlécek hozzáadása

A kérelmek hitelesítésének legegyszerűbb módja az, hogy átadja az előfizetői azonosítót `Ocp-Apim-Subscription-Key` fejlécként, amit ebben a példában alkalmazunk. Alternatív megoldásként hozzáférési jogkivonatra cserélheti az előfizetői azonosítóját, és átadhatja a hozzáférési jogkivonatot is `Authorization` fejlécként a kérelem ellenőrzése céljából. További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Másolja a projektbe a következő kódrészletet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-determine-sentence-length"></a>Egy mondat hosszának meghatározására vonatkozó kérelem létrehozása

Határozza meg azt a mondatot (vagy mondatokat), amelyeknek meg szeretné határozni a hosszúságát:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
}]
```

A következő lépésben egy POST-kérelmet hozunk létre a `requests` modullal. Ez három argumentumot fogad: az összefűzött URL-címet, a kérelemfejléceket és a kérelem törzsét:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>A válasz megjelenítése

Az utolsó lépés az eredmények kiírása. Ez a kódrészlet szebbé teszi az eredményeket a kulcsok rendezésével, a behúzás beállításával és az elem- és kulcselválasztók meghatározásával.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
python sentence-length.py
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Mintaválasz

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem változtatható módon adta meg az előfizetői azonosítót a programban, a rövid útmutató befejezése után mindenképpen távolítsa el az előfizetői azonosítót.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
