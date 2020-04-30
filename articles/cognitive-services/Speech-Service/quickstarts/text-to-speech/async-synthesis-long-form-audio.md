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
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: dcdc942999e45eb779e54cd5f92432c54d65fc6a
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561981"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Rövid útmutató: a hosszú formátumú hang aszinkron szintézise a Pythonban (előzetes verzió)

Ebben a rövid útmutatóban a hosszú hangalapú API használatával aszinkron módon alakíthatja át a szövegeket beszédre, és lekérheti a hangkimenetet a szolgáltatás által megadott URI-ból. Ez a REST API ideális olyan tartalomszolgáltatók számára, amelyeknek a hanganyagot 5 000 karakternél nagyobb (vagy több mint 10 percnél hosszabb) szövegből kell szintetizálni. További információ: [Long audio API](../../long-audio-api.md).

> [!NOTE]
> A hosszú formátumú hang aszinkron szintézise csak [Egyéni neurális hangokkal](../../how-to-custom-voice.md#custom-neural-voices)használható.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7. x vagy 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy a kedvenc szövegszerkesztője.
* Egy Azure-előfizetés és egy Speech Service-előfizetési kulcs. [Hozzon létre egy Azure-fiókot](../../get-started.md#new-resource) , és [hozzon létre egy beszédfelismerési erőforrást](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) a kulcs lekéréséhez. A beszédfelismerési erőforrás létrehozásakor győződjön meg arról, hogy a díjszabási szintje **S0**, és a hely egy [támogatott régióra](../../regions.md#standard-and-neural-voices)van beállítva.

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a kódrészletet egy nevű `voice_synthesis_client.py`fájlba.

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
> Ha még nem használta ezeket a modulokat, telepítenie kell őket a program futtatása előtt. A csomagok telepítéséhez futtassa a következő parancsot: `pip install requests urllib3`.

Ezek a modulok az argumentumok elemzésére, a HTTP-kérelem összeállítására, valamint a szöveg-beszéd hosszú hangREST API meghívására szolgálnak.

## <a name="get-a-list-of-supported-voices"></a>A támogatott hangok listájának beolvasása

Ez a kód beolvassa az elérhető hangok listáját, amelyeket a szöveg-beszéd átalakítására használhat. Adja hozzá a kódot `voice_synthesis_client.py`a következőhöz:

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

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

Tesztelje az eddigi teendőket. Néhány dolgot frissítenie kell az alábbi kérelemben:

* Cserélje `<your_key>` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<region>` le a helyére azt a régiót, ahol a beszédfelismerési erőforrás `eastus` létrejött `westus`(például: vagy). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Ekkor a következőhöz hasonló kimenet jelenik meg:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Bemeneti fájlok előkészítése

Készítse elő a bemeneti szövegfájlt. Egyszerű szöveges vagy SSML szöveg lehet. A bemeneti fájlra vonatkozó követelményekért lásd: a [tartalom előkészítése a szintézishez](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Szöveg konvertálása beszédre

A bemeneti szövegfájl előkészítését követően adja hozzá ezt a kódot a Speech szintézishez a következőhöz `voice_synthesis_client.py`:

> [!NOTE]
> a "concatenateResult" paraméter nem kötelező. Ha ez a paraméter nincs megadva, a rendszer a hangkimeneteket egy bekezdés alapján hozza létre. A hanganyagot 1 kimenetre is összefűzheti a paraméter beállításával. Alapértelmezés szerint a hang kimenete a riff-16khz-16bit-mono-PCM értékre van beállítva. További információ a támogatott hangkimenetekről: [hang kimeneti formátumai](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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
        location = response.headers['Location']
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

Tegyük fel, hogy a szöveg szintetizálása a bemeneti fájllal a forrásként művelet. Néhány dolgot frissítenie kell az alábbi kérelemben:

* Cserélje `<your_key>` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<region>` le a helyére azt a régiót, ahol a beszédfelismerési erőforrás `eastus` létrejött `westus`(például: vagy). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<input>` le a szöveget a szövegről beszédre előkészített szövegfájl elérési útjára.
* Cserélje `<locale>` le a helyére a kívánt kimeneti területi beállítást. További információ: [nyelvi támogatás](../../language-support.md#neural-voices).
* Cserélje `<voice_guid>` le a gombot a kívánt kimeneti hangra. A [támogatott hangok listájának beszerzéséhez](#get-a-list-of-supported-voices)használja az egyiket.

Szöveg konvertálása beszédre a következő paranccsal:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Ha egynél több bemeneti fájllal rendelkezik, több kérést is el kell küldenie. Néhány korlátozást kell figyelembe venni. 
> * Az ügyfél legfeljebb **5** kérést küldhet a kiszolgálónak másodpercenként az egyes Azure-előfizetési fiókokhoz. Ha meghaladja a korlátozást, akkor az ügyfél 429 hibakódot kap (túl sok kérés). Csökkentse a kérelmek mennyiségét másodpercenként
> * A kiszolgáló az egyes Azure-előfizetésekhez tartozó fiókokhoz legfeljebb **120** kérelmet futtathat, és a várólistára állíthatja. Ha meghaladja a korlátozást, a kiszolgáló 429 hibakódot ad vissza (túl sok kérés). Várjon, és ne küldje el az új kérést, amíg néhány kérelem be nem fejeződik

Ekkor a következőhöz hasonló kimenet jelenik meg:

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

Az eredmény tartalmazza a bemeneti szöveget és a szolgáltatás által létrehozott hangkimeneti fájlokat. Ezeket a fájlokat zip-fájlból töltheti le.

## <a name="remove-previous-requests"></a>Korábbi kérelmek eltávolítása

A kiszolgáló minden egyes Azure-előfizetési fiók esetében **20 000** -kérelmeket fog tartani. Ha a kérelem összege meghaladja ezt a korlátozást, távolítsa el az előző kéréseket, mielőtt újakat hozna. Ha nem távolítja el a meglévő kérelmeket, a rendszer hibaüzenetet küld.

Adja hozzá a kódot `voice_synthesis_client.py`a következőhöz:

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

Most nézzük meg, hogy milyen kérelmeket korábban küldött be. A folytatás előtt frissítenie kell néhány dolgot a kérelemben:

* Cserélje `<your_key>` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<region>` le a helyére azt a régiót, ahol a beszédfelismerési erőforrás `eastus` létrejött `westus`(például: vagy). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Ekkor a rendszer visszaküldi a készített összefoglaló kérelmek listáját. A következőhöz hasonló kimenetet fog látni:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Most vegyünk fel egy korábban elküldött kérelmet. Néhány dolgot frissítenie kell az alábbi kódban:

* Cserélje `<your_key>` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<region>` le a helyére azt a régiót, ahol a beszédfelismerési erőforrás `eastus` létrejött `westus`(például: vagy). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje `<synthesis_id>` le az értéket az előző kérelemben visszaadott értékre.

> [!NOTE]
> A "Running"/"Waiting" állapotú kérelmeket nem lehet eltávolítani vagy törölni.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

A következőhöz hasonló kimenetet fog látni:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>A teljes ügyfél beszerzése

A kész `voice_synthesis_client.py` letölthető a [githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a hosszú hang API-ról](../../long-audio-api.md)
