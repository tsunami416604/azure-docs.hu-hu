---
title: 'Rövid útmutató: Aszinkron szintézis hosszú formátumú hanghoz (előzetes verzió) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: A Hosszú hang API-val aszinkron módon konvertálhatja a szöveget beszédgé, és lekérheti a hangkimenetet a szolgáltatás által biztosított URI-ból. Ez a REST API ideális olyan tartalomszolgáltatók számára, amelyeknek a 10 000 karakternél nagyobb vagy 50 bekezdésnél nagyobb szövegfájlokat kell szintetizált beszédgé konvertálniuk.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331076"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Rövid útmutató: Aszinkron szintézis hosszú formátumú hangpythonban (előzetes verzió)

Ebben a rövid útmutatóban a Hosszú hang API-t fogja használni a szöveg beszédgé alakításához, és a szolgáltatás által biztosított URI-ból származó hangkimenet beolvasásához. Ez a REST API ideális olyan tartalomszolgáltatók számára, amelyeknek 5000 karakternél (vagy több mint 10 percnél hosszabb) szövegből kell szintetizálniuk a hangot. További információ: [Long Audio API](../../long-audio-api.md).

> [!NOTE]
> A hosszú formátumú hangaszinkron szintézis csak [egyéni neurális hangokkal](../../how-to-custom-voice.md#custom-neural-voices)használható.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Python 2.7.x vagy 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)vagy kedvenc szövegszerkesztője.
* Egy Azure-előfizetés és egy beszédszolgáltatási előfizetési kulcs. [Hozzon létre egy Azure-fiókot,](../../get-started.md#new-resource) és [hozzon létre egy beszédalapú erőforrást](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) a kulcs leolvasásához. A beszédfelismerési erőforrás létrehozásakor győződjön meg arról, hogy a tarifacsomag **S0**, a hely pedig [támogatott régióra](../../regions.md#standard-and-neural-voices)van állítva.

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja ezt a kódrészletet egy fájlba, amelynek neve `voice_synthesis_client.py`.

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

Ezek a modulok argumentumok elemzésére, a HTTP-kérelem összeállítására és a szövegfelolvasó hosszú audio REST API-nak való hívására szolgálnak.

## <a name="get-a-list-of-supported-voices"></a>A támogatott hangok listájának beszereznie

Ez a kód a szövegfelolvasás hoz való konvertáláshoz használható elérhető hangok listáját tartalmazza. Adja hozzá `voice_synthesis_client.py`a kódot a következőhöz:

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

Teszteljük le, mit tettél eddig. Az alábbi kérelemben néhány dolgot frissítenie kell:

* Cserélje `<your_key>` le a beszédszolgáltatás-előfizetési kulcsra. Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<region>` le azt a régiót, ahol `eastus` a `westus`beszédfelismerési erőforrást létrehozták (például: vagy ). Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

A következőhez hasonló kimenet jelenik meg:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Bemeneti fájlok előkészítése

Szövegfájl előkészítése. Ez lehet egyszerű szöveg vagy SSML szöveg. A bemeneti fájlra vonatkozó követelményekről a [tartalom szintézisre való előkészítéséről](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)itt látható.

## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszédményré

A bemeneti szövegfájl előkészítése után adja hozzá `voice_synthesis_client.py`ezt a kódot a beszédszintézishez a következőhöz:

> [!NOTE]
> Az "concatenateResult" egy választható paraméter. Ha ez a paraméter nincs beállítva, a hangkimenetek bekezdésenként jönnek létre. A paraméterek beállításával a hangokat 1 kimenetbe is fűzheti. Alapértelmezés szerint a hangkimenet riff-16khz-16bit-mono-pcm. A támogatott hangkimenetekről a [Hangkimeneti formátumok](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)című témakörben talál további információt.

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

Kérjük, hogy a szöveget a bemeneti fájl forrásként történő használatával szintetizálják. Az alábbi kérelemben néhány dolgot frissítenie kell:

* Cserélje `<your_key>` le a beszédszolgáltatás-előfizetési kulcsra. Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<region>` le azt a régiót, ahol `eastus` a `westus`beszédfelismerési erőforrást létrehozták (például: vagy ). Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<input>` le a szövegfelolvasásra előkészített szövegfájl elérési útját.
* Cserélje `<locale>` le a kívánt kimeneti területi beállításra. További információt a Nyelvi támogatás című témakörben [talál.](../../language-support.md#neural-voices)
* Cserélje `<voice_guid>` ki a kívánt kimeneti hangra. Használja a [támogatott hangok listájának lehívása című](#get-a-list-of-supported-voices)hang egyikét.

Szöveg átalakítása beszédté ezzel a paranccsal:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Ha 1-nél több bemeneti fájlja van, több kérelmet kell benyújtania. Vannak bizonyos korlátozások, amelyeket figyelembe kell venni. 
> * Az ügyfél másodpercenként legfeljebb **5** kérést küldhet a kiszolgálónak minden Egyes Azure-előfizetési fiókhoz. Ha túllépi a korlátozást, az ügyfél 429-es hibakódot kap (túl sok kérés). Kérjük, csökkentse a kérelem összegét másodpercenként
> * A kiszolgáló minden Egyes Azure-előfizetési fiókhoz legfeljebb **120** kérelmet futtathat és várólistára vehet. Ha túllépi a korlátozást, a kiszolgáló 429-es hibakódot (túl sok kérést) ad vissza. Várjon, és ne nyújtson be új kérelmet, amíg egyes kérelmek et nem teljesít.

A következőhez hasonló kimenet jelenik meg:

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

Az eredmény tartalmazza a bemeneti szöveget és a szolgáltatás által létrehozott hangkimeneti fájlokat. Ezeket a fájlokat zip-ben töltheti le.

## <a name="remove-previous-requests"></a>Korábbi kérések eltávolítása

A kiszolgáló minden Egyes Azure-előfizetési fiókhoz legfeljebb **20 000** kérelmet fog tartani. Ha a kérelem összege meghaladja ezt a korlátozást, kérjük, távolítsa el a korábbi kérelmeket, mielőtt újakat. Ha nem távolítja el a meglévő kérelmeket, hibaüzenetet kap.

Adja hozzá `voice_synthesis_client.py`a kódot a következőhöz:

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

Most nézzük meg, hogy korábban milyen kéréseket küldött be. A folytatás előtt frissítenie kell néhány dolgot a kérelemben:

* Cserélje `<your_key>` le a beszédszolgáltatás-előfizetési kulcsra. Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<region>` le azt a régiót, ahol `eastus` a `westus`beszédfelismerési erőforrást létrehozták (például: vagy ). Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Ez visszaadja az Ön által benyújtott összegző kérések listáját. Ekkor megjelenik egy ilyen kimenet:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Most távolítsunk el egy korábban benyújtott kérelmet. Az alábbi kódban néhány dolgot frissítenie kell:

* Cserélje `<your_key>` le a beszédszolgáltatás-előfizetési kulcsra. Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<region>` le azt a régiót, ahol `eastus` a `westus`beszédfelismerési erőforrást létrehozták (például: vagy ). Ez az információ az [Azure Portalon](https://aka.ms/azureportal)az erőforrás **áttekintése** lapján érhető el.
* Cserélje `<synthesis_id>` le az előző kérelemben visszaadott értékre.

> [!NOTE]
> A "Running"/'Waiting' állapotú kérelmek nem távolíthatók el és nem törölhetők.

Futtassa ezt a parancsot:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Ekkor megjelenik egy ilyen kimenet:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Szerezd meg a teljes ügyfél

Az `voice_synthesis_client.py` elkészült letölthető a [GitHubon.](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Hosszú hang API-ról](../../long-audio-api.md)
