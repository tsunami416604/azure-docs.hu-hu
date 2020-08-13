---
title: Long audio API (előzetes verzió) – Speech Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a hosszú hangalapú API hogyan lett kialakítva a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167759"
---
# <a name="long-audio-api-preview"></a>Long audio API (előzetes verzió)

A hosszú hangalapú API a hosszú formátumú szöveg és a beszéd aszinkron szintéziséhez készült (például hangkönyvek, Hírek és dokumentumok). Ez az API nem adja vissza a szintetizált hangot valós időben, ezért a várt érték az, hogy a válasz (ok) ra fog szavazni, és a kimenet (eke) t a szolgáltatás által elérhetővé tettnek megfelelően használja fel. A Speech SDK által használt szöveg-beszéd API-val ellentétben a hosszú hang-API 10 percnél hosszabb szintetizált hangot is létrehozhat, így a kiadók és a hangtartalom-platformok ideálisak.

A hosszú hang API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd a legjobb neurális hangokat használja.
* Nincs szükség hang-végpont üzembe helyezésére, mert a hangokat nem valós idejű batch-módban szintetizálja.

> [!NOTE]
> A Long audio API mostantól támogatja a [nyilvános neurális hangokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) és az [Egyéni neurális hangokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)is.

## <a name="workflow"></a>Munkafolyamat

A hosszú hangalapú API használatakor a rendszer a szintetizált szövegfájlt vagy fájlokat küldi el, lekérdezi az állapotot, majd ha az állapot sikeres, letöltheti az audió kimenetet.

Ez az ábra a munkafolyamat magas szintű áttekintését tartalmazza.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg róla, hogy:

* Egyszerű szöveges (. txt) vagy SSML szöveg (. txt)
* Kódolása [UTF-8, byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) értékkel
* Egyetlen fájl, nem zip
* Több mint 400 karaktert [tartalmaz az egyszerű szöveges vagy a 400](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) -es SSML-szövegekhez, és kisebb, mint 10 000 bekezdés
  * Egyszerű szöveg esetén az egyes bekezdéseket az ENTER/Return – [egyszerű szöveges beviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) **megadásával** választjuk el.
  * A SSML szövegek esetében az egyes SSML-darabok bekezdésnek tekintendők. A SSML-darabokat különböző bekezdések szerint kell elválasztani – a [SSML szövegének](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) megjelenítése – példa
> [!NOTE]
> A kínai (anyaországi), a kínai (Hongkong KKT), a kínai (Tajvan), a japán és a Koreai nyelveken egy szót két karakternek számítunk fel. 

## <a name="python-example"></a>Python-példa

Ez a szakasz olyan Python-példákat tartalmaz, amelyek a hosszú hang API alapszintű használatát mutatják be. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a kódrészletet egy nevű fájlba `voice_synthesis_client.py` .

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

Ezek a kódtárak az argumentumok elemzésére, a HTTP-kérelem összeállítására, valamint a szöveg-beszéd hosszú hang REST API meghívására szolgálnak.

### <a name="get-a-list-of-supported-voices"></a>A támogatott hangok listájának beolvasása

Ez a kód lehetővé teszi, hogy teljes listát kapjon a hangokat egy adott régióhoz vagy végponthoz, amelyet használhat. Adja hozzá a kódot a következőhöz `voice_synthesis_client.py` :

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

Futtassa a szkriptet a parancs használatával `python voice_synthesis_client.py --voices -key <your_key> -region <region>` , és cserélje le a következő értékeket:

* Cserélje le a `<your_key>` billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le `<region>` a helyére azt a régiót, ahol a beszédfelismerési erőforrás létrejött (például: `eastus` vagy `westus` ). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

Ekkor a következőhöz hasonló kimenet jelenik meg:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Ha a **PublicVoice** paraméter értéke **true (igaz**), a hang a nyilvános neurális hang. Ellenkező esetben az egyéni neurális hang.

### <a name="convert-text-to-speech"></a>Szöveg konvertálása beszédre

Készítse elő a bemeneti szövegfájlt egyszerű szöveges vagy SSML szövegben, majd adja hozzá az alábbi kódot a következőhöz `voice_synthesis_client.py` :

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

Futtassa a szkriptet a parancs használatával `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` , és cserélje le a következő értékeket:

* Cserélje le a `<your_key>` billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le `<region>` a helyére azt a régiót, ahol a beszédfelismerési erőforrás létrejött (például: `eastus` vagy `westus` ). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le a `<input>` szöveget a szövegről beszédre előkészített szövegfájl elérési útjára.
* Cserélje le `<locale>` a helyére a kívánt kimeneti területi beállítást. További információ: [nyelvi támogatás](language-support.md#neural-voices).
* Cserélje le `<voice_guid>` a gombot a kívánt kimeneti hangra. Használja az előző hívása által visszaadott hangok egyikét a `/voicesynthesis/voices` végpontra.

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

> [!NOTE]
> Ha egynél több bemeneti fájllal rendelkezik, több kérést is el kell küldenie. Néhány korlátozást kell figyelembe venni. 
> * Az ügyfél legfeljebb **5** kérést küldhet a kiszolgálónak másodpercenként az egyes Azure-előfizetési fiókokhoz. Ha meghaladja a korlátozást, akkor az ügyfél 429 hibakódot kap (túl sok kérés). Csökkentse a kérelmek mennyiségét másodpercenként
> * A kiszolgáló az egyes Azure-előfizetésekhez tartozó fiókokhoz legfeljebb **120** kérelmet futtathat, és a várólistára állíthatja. Ha meghaladja a korlátozást, a kiszolgáló 429 hibakódot ad vissza (túl sok kérés). Várjon, és ne küldje el az új kérést, amíg néhány kérelem be nem fejeződik

### <a name="remove-previous-requests"></a>Korábbi kérelmek eltávolítása

A szolgáltatás minden egyes Azure-előfizetési fiók esetében **20 000** -kérelmeket fog tartani. Ha a kérelem összege meghaladja ezt a korlátozást, távolítsa el az előző kéréseket, mielőtt újakat hozna. Ha nem távolítja el a meglévő kérelmeket, a rendszer hibaüzenetet küld.

Szúrja be a következő kódot az `voice_synthesis_client.py` fájlba:

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

A futtatásával lekérheti a `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` készített összefoglaló kérelmek listáját. A következőhöz hasonló kimenetet fog látni:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Egy kérelem törléséhez futtassa a parancsot, `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` és cserélje le `<synthesis_id>` az előző kérelemből visszaadott kérelem-azonosító értékre.

> [!NOTE]
> A "Running"/"Waiting" állapotú kérelmeket nem lehet eltávolítani vagy törölni.

A kész a `voice_synthesis_client.py` [githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)érhető el.

## <a name="http-status-codes"></a>HTTP-állapotkódok

A következő táblázat a REST API HTTP-válaszait és üzeneteit részletezi.

| API | HTTP-állapotkód | Leírás | Megoldás |
|-----|------------------|-------------|----------|
| Létrehozás | 400 | A hangszintézis nincs engedélyezve ebben a régióban. | A Speech előfizetés kulcsának módosítása egy támogatott régióval. |
|        | 400 | Csak az ehhez a régióhoz tartozó **normál** beszédfelismerési előfizetés érvényes. | Módosítsa a Speech előfizetés kulcsát a "standard" árképzési szintre. |
|        | 400 | Túllépi az Azure-fiókra vonatkozó 20 000-es kérelem korlátját. Új adatbázisok elküldése előtt távolítson el néhány kérelmet. | A kiszolgáló minden Azure-fiók esetében 20 000-kérelmeket fog tartani. Néhány kérelem törlése az újak elküldése előtt. |
|        | 400 | Ez a modell nem használható a következő hangszintézisben: {modelID}. | Győződjön meg arról, hogy a ({modelID}) állapota helyes. |
|        | 400 | A kérelem régiója nem egyezik a következő modell régiójával: {modelID}. | Győződjön meg arról, hogy a {modelID} régiója egyezik a kérelem régiójával. |
|        | 400 | A hangszintézis csak az UTF-8 kódolású szövegfájlt támogatja az byte-Order jelölővel. | Győződjön meg arról, hogy a bemeneti fájlok UTF-8 kódolással vannak ellátva a byte-Order jelölővel. |
|        | 400 | A hangszintézisi kérelemben csak érvényes SSML-bemenetek engedélyezettek. | Győződjön meg arról, hogy a bemeneti SSML-kifejezések helyesek. |
|        | 400 | A (z) {voiceName} nevű hang nem található a bemeneti fájlban. | A bemeneti SSML hangjának neve nincs igazítva a modell azonosítójával. |
|        | 400 | A bemeneti fájlban lévő bekezdések számának 10 000-nál kisebbnek kell lennie. | Győződjön meg arról, hogy a fájlban lévő bekezdések száma kisebb, mint 10 000. |
|        | 400 | A bemeneti fájlnak 400 karakternél nagyobbnak kell lennie. | Győződjön meg arról, hogy a bemeneti fájl mérete meghaladja a 400 karaktert. |
|        | 404 | A hangszintézis definíciójában deklarált modell nem található: {modelID}. | Győződjön meg arról, hogy a ({modelID}) helyes. |
|        | 429 | Túllépi az aktív hangszintézis korlátot. Várjon, amíg néhány kérelem befejeződik. | A kiszolgáló az egyes Azure-fiókokhoz legfeljebb 120 kérelem futtatására és várólistára helyezésére jogosult. Várjon, és ne küldje el az új kéréseket, amíg néhány kérelem be nem fejeződik. |
| Összes       | 429 | Túl sok kérés van. | Az ügyfél legfeljebb 5 kérést küldhet a kiszolgálónak másodpercenként minden egyes Azure-fiókhoz. Csökkentse a kérések másodpercenkénti számát. |
| Törlés    | 400 | A hangszintézisi feladat még használatban van. | Csak a **befejezett** vagy **sikertelen**kéréseket lehet törölni. |
| GetByID   | 404 | A megadott entitás nem található. | Győződjön meg arról, hogy a szintézis azonosítója helyes. |

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A hosszú hang API több régióban is elérhető egyedi végpontokkal.

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Közép-Kanada | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| USA keleti régiója | `https://eastus.customvoice.api.speech.microsoft.com` |
| Közép-India | `https://centralindia.customvoice.api.speech.microsoft.com` |
| USA déli középső régiója | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Délkelet-Ázsia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Az Egyesült Királyság déli régiója | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Nyugat-Európa | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA 2. nyugati régiója | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

A rugalmas hangkimeneti formátumokat támogatjuk. A "concatenateResult" paraméter beállításával egy adott kimenetet adhat meg, vagy egy adott kimenetet is összefűzött a hangkimenetekhez. A hosszú hang API a következő hangkimeneti formátumokat támogatja:

> [!NOTE]
> Az alapértelmezett hang formátuma a riff-16khz-16bit-mono-PCM.

* riff-8khz-16bit-mono-PCM
* riff-16khz-16bit-mono-PCM
* riff-24khz-16bit-mono-PCM
* riff-48kHz-16bit-mono-PCM
* hang-16khz-32kbitrate-mono-MP3
* hang-16khz-64kbitrate-mono-MP3
* hang-16khz-128kbitrate-mono-MP3
* hang-24khz-48kbitrate-mono-MP3
* hang-24khz-96kbitrate-mono-MP3
* hang-24khz-160kbitrate-mono-MP3

## <a name="sample-code"></a>Mintakód
A hosszú hang API-mintakód elérhető a GitHubon.

* [Mintakód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Mintakód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Mintakód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
