---
title: 'Rövid útmutató: aszinkron szintézis a hosszú formátumú hanghoz (előzetes verzió) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: A hosszú hang API-val aszinkron módon alakíthatja át a szövegeket beszédre, és lekérheti a hangkimenetet a szolgáltatás által megadott URI-ból. Ez a REST API ideális olyan tartalomszolgáltatók számára, amelyeknek a 10 000 vagy 50 bekezdésnél nagyobb szövegfájlokat kell konvertálnia a szintetizált beszédbe.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 52e9fd986973c15d30790b981a5c2ccd1e6974d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507692"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Rövid útmutató: a hosszú formátumú hang aszinkron szintézise a Pythonban (előzetes verzió)

Ebben a rövid útmutatóban a hosszú hangalapú API használatával aszinkron módon alakíthatja át a szövegeket beszédre, és lekérheti a hangkimenetet a szolgáltatás által megadott URI-ból. Ez a REST API ideális olyan tartalomszolgáltatók számára, amelyeknek a 10 000 vagy 50 bekezdésnél nagyobb szövegfájlokat kell konvertálnia a szintetizált beszédbe. További információ: [Long audio API](../../long-audio-api.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7. x vagy 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy a kedvenc szövegszerkesztője.
* Egy Azure-előfizetés és egy Speech Service-előfizetési kulcs. [Hozzon létre egy fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a kódrészletet egy `voice_synthesis_client.py`nevű fájlba.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Ha még nem használta ezeket a modulokat, a program futtatása előtt telepítenie kell őket. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests`.

Ezek a modulok az argumentumok elemzésére, a HTTP-kérelem összeállítására, valamint a szöveg-beszéd hosszú hangREST API meghívására szolgálnak.

## <a name="get-a-list-of-supported-voices"></a>A támogatott hangok listájának beolvasása

Ez a kód beolvassa az elérhető hangok listáját, amelyeket a szöveg-beszéd átalakítására használhat. Adja hozzá a következő kódot `voice_synthesis_client.py`:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>A kód tesztelése

Tesztelje az eddigi teendőket. Futtassa ezt a parancsot, és cserélje le a `<your_key>`t a Speech előfizetési kulccsal, és `<region>` a beszédfelismerési erőforrást létrehozó régióval (például: `eastus` vagy `westus`). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

A következőhöz hasonló kimenetnek kell megjelennie:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>Szöveg konvertálása beszédre

A következő lépés egy bemeneti szövegfájl előkészítése. Egyszerű szöveg-vagy SSML is lehet, de 10 000 karakternél vagy 50 bekezdésnél nagyobbnak kell lennie. A követelmények teljes listáját lásd: [Long audio API](../../long-audio-api.md).

A szövegfájl előkészítése után. A következő lépés a beszédfelismerési kód hozzáadása a projekthez. Adja hozzá ezt a kódot a `voice_synthesis_client.py`hoz:

> [!NOTE]
> Alapértelmezés szerint a hang kimenete a riff-16khz-16bit-mono-PCM értékre van beállítva. További információ a támogatott hangkimenetekről: [Long audio API](../../long-audio-api.md#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Operation-Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>A kód tesztelése

Próbáljon meg egy szöveget szintetizáló kérést használni a bemeneti fájllal forrásként. Néhány dolgot frissítenie kell az alábbi kérelemben:

* Cserélje le a `<your_key>`t a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le a `<region>`t arra a régióra, ahol a beszédfelismerési erőforrás létrejött (például: `eastus` vagy `westus`). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le a `<input>`t annak a szövegfájlnak az elérési útjára, amelyet szöveggé szeretne alakítani.
* Cserélje le a `<locale>`t a kívánt kimeneti területi beállítással. További információ: [nyelvi támogatás](../../language-support.md#neural-voices).
* Cserélje le a `<voice_guid>`t az audió kimenet kívánt hangján. Használja a [támogatott hangok listáját,](#get-a-list-of-supported-voices) vagy használja a [nyelvi támogatásban](../../language-support.md#neural-voices)biztosított neurális hangok listáját.

Szöveg konvertálása beszédre a következő paranccsal:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> a "concatenateResult" egy opcionális paraméter, ha ez a paraméter nincs megadva, a kimenet több Wave-fájlként lesz elküldve, egy az egyes sorokhoz.

A következőhöz hasonló kimenetnek kell megjelennie:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

A megadott eredmény tartalmazza a bemeneti szöveget és a szolgáltatás által generált hangkimeneti fájlokat. Ezek zip-fájlként lesznek letöltve.

## <a name="remove-previous-requests"></a>Korábbi kérelmek eltávolítása

Az egyes előfizetésekhez legfeljebb 2 000 kérelem érkezett. Ezért előfordulhat, hogy a korábban elküldött kéréseket el kell távolítania, mielőtt újat hozna. Ha nem távolítja el a meglévő kérelmeket, hibaüzenet jelenik meg, ha túllépi a 2 000-et.

Adja hozzá ezt a kódot a `voice_synthesis_client.py`hoz:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>A kód tesztelése

Futtassa ezt a parancsot, és cserélje le a `<your_key>`t a Speech előfizetési kulccsal, és `<region>` a beszédfelismerési erőforrást létrehozó régióval (például: `eastus` vagy `westus`). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

Ekkor a rendszer visszaküldi a kért syntheses listáját. A következőhöz hasonló kimenetnek kell megjelennie:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Most használjuk ezeket az értékeket a korábban elküldött kérések eltávolítására vagy törlésére. Futtassa ezt a parancsot, és cserélje le a `<your_key>`t a Speech előfizetési kulccsal, és `<region>` a beszédfelismerési erőforrást létrehozó régióval (például: `eastus` vagy `westus`). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el. A `<synthesis_id>`nak az előző kérelemben visszaadott értékek egyikének kell lennie.

> [!NOTE]
> A "Running"/"Waiting" állapotú kérelmeket nem lehet eltávolítani vagy törölni.

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

A következőhöz hasonló kimenetnek kell megjelennie:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>A teljes ügyfél beszerzése

A teljes `voice_synthesis_client.py` letölthető a [githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a hosszú hang API-ról](../../long-audio-api.md)
