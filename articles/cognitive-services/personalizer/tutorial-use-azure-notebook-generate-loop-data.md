---
title: 'Oktatóanyag: Azure Notebook – Personalizer'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag egy Azure-jegyzetfüzetben _system személyre szabászati hurkot szimulál, amely azt javasolja, hogy az ügyfélnek milyen típusú kávét kell rendelnie. A felhasználók és beállításaik egy felhasználói adatkészletben tárolódnak. A kávéval kapcsolatos információk egy kávé adatkészletében is rendelkezésre állnak.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986835"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Oktatóanyag: Personalizer használata az Azure Notebookban

Ez az oktatóanyag egy Personalizer hurkot futtat egy Azure-jegyzetfüzetben, amely bemutatja a Personalizer hurok életciklusának végét.

A hurok azt javasolja, hogy az ügyfélnek milyen típusú kávét kell rendelnie. A felhasználók és beállításaik egy felhasználói adatkészletben tárolódnak. A kávéval kapcsolatos információkat egy kávéadatkészlet tárolja.

## <a name="users-and-coffee"></a>Felhasználók és kávé

A jegyzetfüzet, szimulálva a felhasználói interakció egy weboldal, kiválasztegy véletlenszerű felhasználó, a napszak, és az időjárás típusa az adatkészletből. A felhasználói adatok összegzése:

|Ügyfelek - környezeti funkciók|A nap szakai|Az időjárás típusai|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Reggel<br>Délután<br>Este|Napos<br>Esős<br>Havas|

Annak érdekében, hogy a Personalizer megtanulhassa, a _rendszer_ idővel részleteket is tud az egyes személyek kávéválasztásáról.

|Kávé - akciófunkciók|A hőmérséklet típusai|Származási helyek|A pörkölés típusai|organikus|
|--|--|--|--|--|
|Cappacino között|Gyakori|Kenya|Sötét|organikus|
|Hideg sör|Ritka elérésű|Brazília|Világos|organikus|
|Jeges mokka|Ritka elérésű|Etiópia|Világos|Nem szerves|
|Latte|Gyakori|Brazília|Sötét|Nem szerves|

A Personalizer hurok **célja,** hogy a lehető legtöbb időt megtalálja a felhasználók és a kávé között.

Az oktatóanyag kódja a [Personalizer Samples GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook)érhető el.

## <a name="how-the-simulation-works"></a>A szimuláció működése

A futó rendszer kezdetén a Personalizer javaslatai csak 20% és 30% között sikeresek. Ezt a sikert a Personalizer Reward API-nak küldött jutalom jelzi, 1 ponttal. Miután néhány Rang és Jutalom hívások, a rendszer javul.

A kezdeti kérelmek után futtasson egy offline kiértékelést. Ez lehetővé teszi a Personalizer számára, hogy áttekintse az adatokat, és jobb tanulási szabályzatot javasoljon. Alkalmazza az új tanulási szabályzatot, és futtassa újra a jegyzetfüzetet az előző kérelmek számának 20%-ával. A hurok jobban teljesít az új tanulási szabályzattal.

## <a name="rank-and-reward-calls"></a>Rang és jutalom hívások

A Personalizer szolgáltatás néhány ezer hívása esetén az Azure Notebook elküldi a **Rank** kérést a REST API-nak:

* Egyedi azonosító a Rang/Kérés eseményhez
* Kontextus funkciók - A felhasználó véletlenszerű kiválasztása, időjárás és napszak - a felhasználó szimulálása egy webhelyen vagy mobileszközön
* Funkciókkal kapcsolatos műveletek - _Az összes_ kávéadat - amelyből a Personalizer javaslatot tesz

A rendszer megkapja a kérést, majd összehasonlítja az előrejelzést a felhasználó ismert választásával ugyanebben az időpontban és az időjárás. Ha az ismert választás megegyezik az előre jelzett választás, a **Jutalom** 1 küld vissza Personalizer. Ellenkező esetben a visszaküldött jutalom 0.

> [!Note]
> Ez egy szimuláció, így az algoritmus a jutalom egyszerű. Egy valós forgatókönyv, az algoritmus kell használnia az üzleti logika, esetleg súlyok különböző aspektusait az ügyfél tapasztalata, a jutalom pontszám meghatározásához.


## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure Notebook-fiók.](https://notebooks.azure.com/)
* Egy [Azure Personalizer erőforrás.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)
    * Ha már használta a Personalizer erőforrást, győződjön meg arról, hogy [törölje az adatokat](how-to-settings.md#clear-data-for-your-learning-loop) az Azure Portalon az erőforrás.
* Töltse fel a minta összes [fájlját](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) egy Azure Notebook-projektbe.

Fájlleírások:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) a Jupyter notebook ez a bemutató.
* [A felhasználói adatkészlet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) egy JSON-objektumban van tárolva.
* [A kávéadatkészlet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) egy JSON-objektumban van tárolva.
* [Példa: JSON-kérelem](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) a várt formátum a RANG API-hoz küldött POST-kérelemhez.

## <a name="configure-personalizer-resource"></a>Personalizer erőforrás konfigurálása

Az Azure Portalon konfigurálja a [Personalizer erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) a **frissítési modell gyakorisága** 15 másodpercre van állítva, és a **jutalom várakozási idő** 15 másodperc. Ezek az értékek a **[Konfiguráció](how-to-settings.md#configure-service-settings-in-the-azure-portal)** lapon találhatók.

|Beállítás|Érték|
|--|--|
|modell gyakoriságának frissítése|15 másodperc|
|jutalom várakozási idő|15 másodperc|

Ezek az értékek nagyon rövid időtartamúak, hogy megjelenítsék az oktatóanyag változásait. Ezeket az értékeket nem szabad éles környezetben használni anélkül, hogy a Personalizer-ciklussal elérné a célját.

## <a name="set-up-the-azure-notebook"></a>Az Azure-jegyzetfüzet beállítása

1. Módosítsa a `Python 3.6`kernelt a -ra.
1. Nyissa meg az `Personalizer.ipynb` fájlt.

## <a name="run-notebook-cells"></a>Jegyzetfüzet-cellák futtatása

Futtasson minden végrehajtható cellát, és várja meg, amíg visszatér. Tudod, hogy ez történik, ha a zárójelben a `*`cella mellett kijelző egy számot, hanem a . A következő szakaszok ismertetik, hogy az egyes sejtek mit csinálnak programozott módon, és mire számíthatnak a kimenethez.

### <a name="include-the-python-modules"></a>A python modulok belefoglalása

Adja meg a szükséges python modulokat. A cella nem rendelkezik kimenettel.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Personalizer erőforráskulcs és -név beállítása

Az Azure Portalon keresse meg a kulcsot és a végpontot a Personalizer-erőforrás **gyorsútmutató** lapján. Módosítsa a `<your-resource-name>` Personalizer erőforrás nevének értékét. Módosítsa a `<your-resource-key>` Personalizer kulcs értékét.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Aktuális dátum és idő nyomtatása
Ezzel a funkcióval jegyezze fel az iteratív függvény kezdési és befejezési időpontjait.

Ezeknek a celláknak nincs kimenetük. A függvény az aktuális dátumot és időt adja ki a hívott időpontban.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>A modell utolsó frissítési idejének betöltése

A függvény `get_last_updated`, a , a függvény kinyomtatja a modell frissítésének utolsó módosított dátumát és időpontját.

Ezeknek a celláknak nincs kimenetük. A függvény az utolsó modell betanítási dátumát adja ki, amikor hívják.

A függvény GET REST API-t használ a [modell tulajdonságok lehívásához.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Házirend- és szolgáltatáskonfiguráció beszereznie

Ellenőrizze a szolgáltatás állapotát ezzel a két REST-hívással.

Ezeknek a celláknak nincs kimenetük. A függvény a szolgáltatás értékeit adja ki, amikor hívják.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>URL-címek összeállítása és JSON-adatfájlok olvasása

Ez a cella

* létrehozza a REST-hívásokban használt URL-eket
* beállítja a biztonsági fejlécet a Personalizer erőforráskulcs használatával
* beállítja a rangsorolási eseményazonosító véletlenszerű magját
* olvasás a JSON adatfájlokban
* hívások `get_last_updated` módszere - a tanulási szabályzatot eltávolították a példa kimenetben
* hívások `get_service_settings` módja

A cella kimenettel `get_last_updated` rendelkezik a hívásból és `get_service_settings` a funkciókból.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Ellenőrizze, hogy a `rewardWaitTime` `modelExportFrequency` kimenet 15 másodpercre van-e beállítva.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Az első REST-hívás hibaelhárítása

Ez az előző cella az első cella, amely a Personalizer-t hívja. Győződjön meg arról, hogy `<Response [200]>`a REST állapotkódja a kimenetben . Ha hibaüzenetet kap, például a 404-es, de biztos benne, hogy az erőforráskulcs és a név helyes, töltse be újra a jegyzetfüzetet.

Győződjön meg róla, hogy a kávé és a felhasználók száma egyaránt 4. Ha hibaüzenetet kap, ellenőrizze, hogy feltöltötte-e az összes 3 JSON fájlt.

### <a name="set-up-metric-chart-in-azure-portal"></a>Metrikadiagram beállítása az Azure Portalon

Az oktatóanyag későbbi részében a 10 000 kérelem hosszú ideig futó folyamata látható a böngészőből egy frissítő szövegdobozzal. Előfordulhat, hogy könnyebben látható a diagramon vagy teljes összegként, amikor a hosszú ideig futó folyamat véget ér. Az információk megtekintéséhez használja az erőforráshoz mellékelt mutatókat. Létrehozhatja a diagramot most, hogy befejezte a szolgáltatásra vonatkozó kérést, majd rendszeresen frissítheti a diagramot, amíg a hosszú ideig futó folyamat tart.

1. Az Azure Portalon válassza ki a Personalizer erőforrást.
1. Az erőforrás-navigáció, **válassza metrikák** alatt figyelés.
1. A diagramon válassza a **Metrika hozzáadása lehetőséget.**
1. Az erőforrás és a metrika névtér már be van állítva. Csak a **sikeres hívások** mérőszámát és az összegösszesítést kell kiválasztania. **sum**
1. Módosítsa az időszűrőt az elmúlt 4 órára.

    ![Állítsa be a metrikadiagramot az Azure Portalon, és adja hozzá az elmúlt 4 óra sikeres hívásainak metrikáját.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    A diagramon három sikeres hívást kell látnia.

### <a name="generate-a-unique-event-id"></a>Egyedi eseményazonosító létrehozása

Ez a funkció egyedi azonosítót hoz létre minden rangsorolási híváshoz. Az azonosító a rang és a jutalom hívásadatainak azonosítására szolgál. Ez az érték származhat egy üzleti folyamatból, például egy webnézet-azonosítóból vagy egy tranzakcióazonosítóból.

A cella nem rendelkezik kimenettel. A függvény a hívott azonosítót adja ki.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Véletlenszerű felhasználó, időjárás és napszak

Ez a funkció kiválaszt egy egyedi felhasználót, időjárást és napszakot, majd hozzáadja ezeket az elemeket a JSON-objektumhoz, hogy elküldhesse a Rangsorolás iráskéréshez.

A cella nem rendelkezik kimenettel. Amikor a függvény neve, a véletlenszerű felhasználó nevét, véletlenszerű időjárását és véletlenszerű napszakát adja vissza.

A lista 4 felhasználó és preferenciáik - csak néhány preferenciák jelennek meg a rövidség:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Az összes kávéadat hozzáadása

Ez a függvény hozzáadja a kávé teljes listáját a JSON-objektumhoz, amelyet a Rang kéréshez kell küldeni.

A cella nem rendelkezik kimenettel. A függvény módosítja a `rankjsonobj` hívott funkciót.


Egyetlen kávé jellemzőinek példája:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Előrejelzés összehasonlítása az ismert felhasználói preferenciával

Ezt a függvényt a Rang API hívása után hívják meg minden egyes iteráció.

Ez a funkció összehasonlítja a felhasználó által előnyben részesített kávé, az időjárás és a napszak, a Personalizer javaslatát a felhasználó számára ezeket a szűrőket. Ha a javaslat megegyezik, 1-es pontszámot ad vissza, ellenkező esetben a pontszám 0. A cella nem rendelkezik kimenettel. A függvény a hívott pontszám kimenetével rendelkezik.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Hurok keresztül kéri, hogy rang és jutalom

A következő cella a jegyzetfüzet _fő_ munkája, egy véletlenszerű felhasználó beszerzése, a kávélista beszerzése, mindkettő küldése a Rank API-ba. Az előrejelzés összehasonlítása a felhasználó ismert preferenciáival, majd a jutalom visszaküldése a Personalizer szolgáltatásba.

A hurok `num_requests` az időkig fut. Personalizer szüksége van néhány ezer hívások rank and reward létrehozni egy modellt.

Egy példa a JSON küldött a Rang API következik. A kávé lista nem teljes, a rövidség. Láthatjuk az egész JSON kávé `coffee.json`.

JSON sent to the Rank API:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

JSON-válasz a Rank API-tól:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Végül, minden hurok mutatja a felhasználó véletlenszerű kiválasztását, az időjárást, a napszakot és a meghatározott jutalmat. Az 1-es jutalom azt jelzi, hogy a Personalizer erőforrás a megfelelő kávétípust választotta ki az adott felhasználónak, az időjárásnak és a napszaknak.

```console
1 Alice Rainy Morning Latte 1
```

A függvény a következőket használja:

* Rang: a POST REST [API-t kap rangot](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Jutalom: a POST REST [API-t jelenteni jutalom](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Futtatás 10 000 ismétlésért
Futtassa a Personalizer hurkot 10 000 iterációhoz. Ez egy hosszú ideig futó esemény. Ne zárja be a notebookot futtató böngészőt. Frissítse a metrikadiagramot az Azure Portalon rendszeresen a szolgáltatás összes hívásának megtekintéséhez. Ha körülbelül 20.000 hívása van, a hurok minden egyes ismétlésére rang és jutalom hívás, az ismétlések megtörténnek.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Diagram eredmények, hogy javulást

Hozzon létre `count` egy `rewards`diagramot a és a ból.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>10 000 rangsorolási kérelem diagramjának futtatása

Futtassa a `createChart` funkciót.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>A diagram olvasása

Ez a diagram a modell sikeresaz aktuális alapértelmezett tanulási szabályzat.

![Ez a diagram az aktuális tanulási szabályzat sikerességét mutatja a teszt időtartama alatt.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Az ideális cél, hogy a végén a vizsgálat, a hurok átlagosan a siker aránya, amely közel 100 százalék mínusz a feltárás. A feltárás alapértelmezett értéke 20%.

`100-20=80`

Ez a feltárási érték megtalálható az Azure Portalon, a Personalizer erőforrás, a **konfiguráció lapon.**

Annak érdekében, hogy jobb tanulási szabályzatot találjon a Rank API-ra vonatkozó adatai alapján, futtasson [offline értékelést](how-to-offline-evaluation.md) a portálon a Personalizer hurokhoz.

## <a name="run-an-offline-evaluation"></a>Kapcsolat nélküli kiértékelés futtatása

1. Az Azure Portalon nyissa meg a Personalizer erőforrás **kiértékelések lapját.**
1. Válassza **a Kiértékelés létrehozása**lehetőséget.
1. Adja meg a kiértékelés nevének és dátumtartományának szükséges adatait a ciklus kiértékeléséhez. A dátumtartománynak csak azokra a napokra kell kiterjednie, amelyekre az értékeléshez összpontosít.
    ![Az Azure Portalon nyissa meg a Personalizer erőforrás kiértékelések lapját. Válassza a Kiértékelés létrehozása lehetőséget. Adja meg a kiértékelés nevét és a dátumtartományt.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Az offline kiértékelés futtatásának célja annak megállapítása, hogy van-e jobb tanulási szabályzat az ebben a ciklusban használt funkciókhoz és műveletekhez. A jobb tanulási szabályzat megkereséséhez győződjön meg arról, hogy az **optimalizálási felderítés** be van kapcsolva.

1. A kiértékelés megkezdéséhez válassza az **OK gombot.**
1. **Ez az Értékelések** lap felsorolja az új értékelést és annak aktuális állapotát. Attól függően, hogy mennyi adatvan, ez a kiértékelés eltarthat egy ideig. Néhány perc múlva visszatérhet erre az oldalra, hogy lássa az eredményeket.
1. Amikor az értékelés befejeződött, válassza ki az értékelést, majd válassza **a Különböző tanulási irányelvek összehasonlítása**lehetőséget. Ez bemutatja a rendelkezésre álló tanulási szabályzatokat, és hogyan viselkednének az adatokkal.
1. Válassza ki a táblázatban a legfelső tanulási szabályzatot, és válassza az **Alkalmaz**lehetőséget. Ez a legjobb tanulási szabályzatot alkalmazza a modellre és a retrains.This applies the _best_ learning policy to your model and retrains.

## <a name="change-update-model-frequency-to-5-minutes"></a>A frissítési modell gyakoriságának módosítása 5 percre

1. Az Azure Portalon, még mindig a Personalizer erőforrás, válassza ki a **konfigurációs** lapot.
1. Módosítsa a **modell frissítési gyakoriságát,** és **a jutalom várakozási idejét** 5 percre módosítsa, és válassza a Mentés **lehetőséget.**

További információ a [jutalom várakozási idejéről](concept-rewards.md#reward-wait-time) és a [modellfrissítés gyakoriságáról.](how-to-settings.md#model-update-frequency)

```python
#Verify new learning policy and times
get_service_settings()
```

Ellenőrizze, hogy a `rewardWaitTime` `modelExportFrequency` kimenet és mindkettő 5 percre van-e beállítva.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Új tanulási szabályzat érvényesítése

Térjen vissza az Azure-jegyzetfüzetbe, és folytassa ugyanazt a hurkot, de csak 2000 iterációesetén. Frissítse a metrikadiagramot az Azure Portalon rendszeresen a szolgáltatás összes hívásának megtekintéséhez. Ha körülbelül 4000 hívása van, a hurok minden egyes ismétlésére rang és jutalom hívás, az ismétlések megtörténnek.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>2000 rangsorolási kérelem diagramjának futtatása

Futtassa a `createChart` funkciót.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>A második diagram áttekintése

A második diagramon látható annektálási növekedést kell mutatnia a Rangsorolási előrejelzésekben, a felhasználói beállításokkal igazodva.

![A második diagramon látható annektálási növekedést kell mutatnia a Rangsorolási előrejelzésekben, a felhasználói beállításokkal igazodva.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem kívánja folytatni az oktatóanyag-sorozatot, törölje a következő erőforrásokat:

* Törölje az Azure Notebook-projektet.
* Törölje a Personalizer erőforrást.

## <a name="next-steps"></a>További lépések

A mintában használt [Jupyter-jegyzetfüzet és adatfájlok](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) a GitHub-tárházban érhetők el a Personalizer számára.

