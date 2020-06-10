---
title: 'Oktatóanyag: Azure notebook – személyre szabott'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag szimulál egy személyre szabott hurkot _system egy Azure jegyzetfüzetben, amely azt jelzi, hogy az ügyfél milyen típusú kávét rendel. A felhasználókat és azok beállításait egy felhasználói adatkészletben tárolja a rendszer. A kávéval kapcsolatos információk a Coffee-adatkészletekben is elérhetők és tárolhatók.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: diberry
ms.custom: tracking-python
ms.openlocfilehash: 30897e1bdd5d139d3a11980430cbcc6b10052ecc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608485"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Oktatóanyag: személyre szabott használata az Azure jegyzetfüzetben

Ez az oktatóanyag egy személyre szabott hurkot futtat egy Azure-jegyzetfüzetben, amely egy személyre szabott hurok teljes életciklusának végét szemlélteti.

A hurok azt sugallja, hogy az ügyfélnek milyen típusú kávét kell megrendelnie. A felhasználókat és azok beállításait egy felhasználói adatkészletben tárolja a rendszer. A kávéval kapcsolatos információkat a rendszer egy kávét tartalmazó adatkészletben tárolja.

## <a name="users-and-coffee"></a>Felhasználók és kávé

A notebook, amely szimulálja a felhasználói interakciót egy webhellyel, kiválasztja a véletlenszerű felhasználót, napszakot és az adatkészletből származó időjárási típust. A felhasználói adatok összegzése:

|Ügyfelek – környezeti funkciók|Napszakok|Időjárási típusok|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Reggel<br>Délután<br>Esténként|Sunny<br>Rainy<br>Havas|

Ha segítségre van a személyre szabott tanulásban, akkor a _rendszer_ az egyes személyek kávé-kiválasztási adatait is ismeri.

|Kávé-műveleti funkciók|Hőmérsékleti típusok|Származási hely|A sült típusok|organikus|
|--|--|--|--|--|
|Cappacino|Gyakori|Kenya|Sötét|organikus|
|Hideg főzet|Ritka elérésű|Brazília|Világos|organikus|
|Jeges Mocha|Ritka elérésű|Etiópia|Világos|Nem ökológiai|
|Tejeskávé|Gyakori|Brazília|Sötét|Nem ökológiai|

A személyre szabott hurok **célja** , hogy a lehető legnagyobb mértékben megkeresse a felhasználók és a kávé közötti legjobb egyezést.

A jelen oktatóanyaghoz tartozó kód a [megszemélyesítő minták GitHub-tárházában](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook)érhető el.

## <a name="how-the-simulation-works"></a>A szimuláció működése

A futó rendszer elején a személyre szabott javaslatok csak 20 – 30%-kal sikeresek. Ezt a sikert a személyre szabott jutalom API-ra visszaküldő jutalom jelzi, amelynek pontszáma 1. Néhány rang és jutalmazási hívás után a rendszeren is javul.

A kezdeti kérelmek után futtasson egy offline kiértékelést. Ez lehetővé teszi, hogy a személy megtekintse az adatelemzést, és jobb tanulási szabályzatot javasoljon. Alkalmazza az új képzési szabályzatot, és futtassa újra a jegyzetfüzetet az előző kérelmek számának 20%-ában. A hurok jobb teljesítményt nyújt az új tanulási szabályzattal.

## <a name="rank-and-reward-calls"></a>Rangsorolási és jutalmazási hívások

A személyre szabott szolgáltatáshoz tartozó néhány ezer hívás esetében az Azure notebook a **rangsorra** vonatkozó kérelmet küld a REST APInak:

* A rangsor/kérelem esemény egyedi azonosítója
* Környezeti funkciók – a felhasználó véletlenszerű választása, időjárás és napszak – felhasználók szimulálása webhelyeken vagy mobileszközökön
* Funkciókkal rendelkező műveletek – az _összes_ Coffee-adatok – amelyből a személyre szabott javaslat

A rendszer megkapja a kérést, majd összehasonlítja az előrejelzést a felhasználó ismert választási lehetőségével a nap és az idő azonos időpontjában. Ha az ismert választási lehetőség megegyezik az előre jelzett választási lehetőséggel, az 1 **jutalmat** a személyre szabott értékre küldi vissza. Ellenkező esetben a jutalom visszaküldve 0.

> [!Note]
> Ez egy szimuláció, így a jutalom algoritmusa egyszerű. Egy valós forgatókönyv esetén az algoritmusnak az üzleti logikát kell használnia, amely valószínűleg az ügyfél felhasználói élményének különböző szempontjait használja fel a jutalom pontszámának meghatározásához.


## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure notebook](https://notebooks.azure.com/) -fiók.
* Egy [Azure-beli személyre szabott erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Ha már használta a személyre szabott erőforrást, ügyeljen arra, hogy törölje az erőforráshoz tartozó Azure Portal lévő [adatforrást](how-to-settings.md#clear-data-for-your-learning-loop) .
* Töltse fel a [minta](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) összes fájlját egy Azure notebook-projektbe.

Fájl leírása:

* A [személyre szabott. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) az oktatóanyaghoz tartozó Jupyter-jegyzetfüzet.
* A [felhasználói adatkészletet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) egy JSON-objektum tárolja.
* A [kávé-adatkészlet](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) egy JSON-objektumban tárolódik.
* [Példa a JSON-kérelemre](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) a post-kérések várt formátuma a Rank API-hoz.

## <a name="configure-personalizer-resource"></a>Személyre szabott erőforrás konfigurálása

A Azure Portalban konfigurálja a [személyre szabott erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) a **frissítési modell gyakorisága** beállításnál 15 másodpercre, a **jutalom várakozási idejét** pedig 15 másodpercre. Ezek az értékek a **[konfiguráció](how-to-settings.md#configure-service-settings-in-the-azure-portal)** oldalon találhatók.

|Beállítás|Érték|
|--|--|
|modell frissítési gyakorisága|15 másodperc|
|jutalom várakozási ideje|15 másodperc|

Ezeknek az értékeknek nagyon rövid az időtartama, hogy megjelenjenek az oktatóanyag változásai. Ezeket az értékeket nem ajánlott éles környezetben használni, ha nem ellenőrzi, hogy a cél a személyre szabott hurok használatával van-e megvalósítva.

## <a name="set-up-the-azure-notebook"></a>Az Azure notebook beállítása

1. Módosítsa a kernelt a következőre: `Python 3.6` .
1. Nyissa meg az `Personalizer.ipynb` fájlt.

## <a name="run-notebook-cells"></a>Jegyzetfüzet-cellák futtatása

Futtassa az egyes végrehajtható cellákat, és várjon, amíg vissza nem tér. Akkor is tudja, ha a cella melletti szögletes zárójelek egy számot jelenítenek meg a helyett `*` . A következő szakaszokból megtudhatja, hogy az egyes cellák hogyan végzik el programozott módon, és mire számíthatnak a kimenetre.

### <a name="include-the-python-modules"></a>A Python-modulok belefoglalása

Adja meg a szükséges Python-modulokat. A cellának nincs kimenete.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Személyre szabott erőforrás kulcsának és nevének megadása

A Azure Portal keresse meg a kulcsot és a végpontot a személyre szabott erőforrás rövid **útmutató lapján.** Módosítsa a `<your-resource-name>` személyre szabott erőforrás nevének értékét. Módosítsa a `<your-resource-key>` személyre szabott kulcs értékét.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Aktuális dátum és idő nyomtatása
Ezzel a függvénnyel megjegyezheti az iterációs függvény kezdési és befejezési időpontját.

Ezek a cellák nem rendelkeznek kimenettel. A függvény az aktuális dátumot és időpontot jeleníti meg a híváskor.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Az utolsó modell frissítési időpontjának beolvasása

A függvény neve esetén `get_last_updated` a függvény kinyomtatja a modell frissítésének utolsó módosításának dátumát és időpontját.

Ezek a cellák nem rendelkeznek kimenettel. A függvény az utolsó modell tanítási dátumát jeleníti meg a híváskor.

A függvény a GET REST API használatával kérdezi le a [modell tulajdonságait](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

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

### <a name="get-policy-and-service-configuration"></a>Házirend és szolgáltatás konfigurációjának beolvasása

Ellenőrizze a szolgáltatás állapotát a következő két REST-hívással.

Ezek a cellák nem rendelkeznek kimenettel. A függvény kiírja a szolgáltatás értékeit a híváskor.

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

### <a name="construct-urls-and-read-json-data-files"></a>URL-címek kiépítése és JSON-adatfájlok olvasása

Ez a cella

* felépíti a REST-hívásokban használt URL-címeket
* a biztonsági fejléc beállítása a személyre szabott erőforrás kulcsa alapján
* a rangsorolási esemény AZONOSÍTÓjának véletlenszerű magot állítja be
* beolvasás a JSON-adatfájlokban
* hívások `get_last_updated` metódusa – a tanulási szabályzat el lett távolítva példaként a kimenetben
* hívások `get_service_settings` metódusa

A cella kimenete a hívás és a `get_last_updated` `get_service_settings` függvény.

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

Ellenőrizze, hogy a kimenet `rewardWaitTime` és `modelExportFrequency` mindkettő 15 másodpercre van-e állítva.

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

Ez az előző cella az első olyan cella, amely a személyre irányuló hívást kéri. Győződjön meg arról, hogy a kimenetben a REST-állapotkód szerepel `<Response [200]>` . Ha hibaüzenet jelenik meg (például 404), de biztos benne, hogy az erőforrás kulcsa és neve helyes, töltse be újra a jegyzetfüzetet.

Győződjön meg arról, hogy a kávé és a felhasználók száma 4. Ha hibaüzenetet kap, ellenőrizze, hogy az összes 3 JSON-fájlt feltöltötte-e.

### <a name="set-up-metric-chart-in-azure-portal"></a>Metrikus diagram beállítása a Azure Portalban

Az oktatóanyag későbbi részében az 10 000-kérelmek hosszú futású folyamata látható a böngészőben egy frissítési szövegmezővel. Előfordulhat, hogy egy diagramon vagy teljes összegen belül könnyebben látható, ha a hosszan futó folyamat véget ér. Ezen információk megtekintéséhez használja az erőforráshoz megadott metrikákat. Létrehozhatja a diagramot most, hogy elvégezte a kérést a szolgáltatáshoz, majd rendszeresen frissíti a diagramot, amíg a hosszú ideig futó folyamat zajlik.

1. A Azure Portal válassza ki a személyre szabott erőforrást.
1. Az erőforrás-navigáció területen válassza a **mérőszámok** lehetőséget a figyelés alatt.
1. A diagramon válassza a **metrika hozzáadása**elemet.
1. Az erőforrás és a metrika névtere már be van állítva. Csak a **sikeres hívások** mérőszámát és az **összeg**összesítését kell kiválasztania.
1. Módosítsa az időszűrőt az utolsó 4 órára.

    ![Metrikus diagram beállítása a Azure Portalban, metrika hozzáadása a sikeres hívásokhoz az elmúlt 4 órában.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Három sikeres hívást kell látnia a diagramon.

### <a name="generate-a-unique-event-id"></a>Egyedi eseményazonosító létrehozása

Ez a függvény egyedi azonosítót hoz létre az egyes rangsorolási hívásokhoz. Az azonosító a Range és a jutalmazási hívás adatainak azonosítására szolgál. Ez az érték olyan üzleti folyamatokból származhat, mint a webes nézet azonosítója vagy a tranzakció azonosítója.

A cellának nincs kimenete. A függvény az egyedi azonosítót adja meg a híváskor.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Véletlenszerű felhasználói, időjárási és napszakok beolvasása

Ez a függvény kiválaszt egy egyedi felhasználót, időjárást és időpontot, majd hozzáadja ezeket az elemeket a JSON-objektumhoz, hogy elküldhesse a rangsor kérését.

A cellának nincs kimenete. A függvény hívásakor a rendszer visszaadja a véletlenszerű felhasználó nevét, a véletlenszerű időjárási időt és a nap véletlenszerű időpontját.

A 4 felhasználó és az azokhoz tartozó beállítások listája – csak bizonyos beállítások láthatók a rövid távú megjelenítéshez:

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


### <a name="add-all-coffee-data"></a>Az összes Coffee-érték hozzáadása

Ez a függvény hozzáadja a teljes listát a kávéhoz a JSON-objektumhoz, amely a rangsorba küldi a kérelmet.

A cellának nincs kimenete. A függvény módosítja a metódust, `rankjsonobj` Ha a hívása megtörténik.


Az egyetlen kávé funkcióinak példája:

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

### <a name="compare-prediction-with-known-user-preference"></a>Az előrejelzés összehasonlítása ismert felhasználói beállításokkal

Ezt a függvényt a Rank API meghívása után hívja meg az egyes iterációk esetében.

Ez a függvény összehasonlítja a felhasználót a kávéra, az időjárási és a napszakok alapján, valamint a felhasználóra vonatkozó javaslattal a szűrőkhöz. Ha a javaslat megfelel az értéknek, az 1. pontszámot adja vissza, ellenkező esetben a pontszám 0. A cellának nincs kimenete. A függvény kiírja a pontszámot a híváskor.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>A rangsorban és a jutalomban megjelenő hívások ismétlése

A következő cella a notebook _fő_ munkája, amely egy véletlenszerű felhasználó beszerzése, a kávék listájának beolvasása, valamint a rangsor API-nak való küldése. Hasonlítsa össze az előrejelzést a felhasználó ismert beállításaival, majd küldje vissza a jutalmat a személyre szabott szolgáltatásnak.

A hurok időnként fut `num_requests` . A személyre szabottan néhány ezer hívást kell megkövetelni a modell létrehozásához és Jutalmazásához.

A Range API-nak eljuttatott JSON-példa a következő:. A kávé listája nem fejeződött be, a rövidség kedvéért. Megtekintheti a kávé teljes JSON-fájlját `coffee.json` .

A Range API-nak eljuttatott JSON:

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

Végezetül minden hurok a felhasználó, időjárás, napszak és meghatározott jutalom véletlenszerű kiválasztását mutatja be. Az 1. jutalom azt jelzi, hogy a személyre szabott erőforrás kiválasztotta a megfelelő kávét a megadott felhasználóhoz, időjáráshoz és napszakhoz.

```console
1 Alice Rainy Morning Latte 1
```

A függvény a következőket használja:

* Rank: egy POST REST API a [rangsor beszerzéséhez](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Jutalom: a POST REST API a [jutalom jelentéséhez](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

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

## <a name="run-for-10000-iterations"></a>Futtatás 10 000 iterációk esetén
Futtassa a személyre szabott hurok 10 000-es ismétlését. Ez egy hosszú ideig futó esemény. Ne zárjuk be a jegyzetfüzetet futtató böngészőt. Rendszeresen frissítse a metrikák diagramot a Azure Portal a szolgáltatás összes hívásának megtekintéséhez. Ha körülbelül 20 000 hívása van, az ismétlések rangsora és jutalma a hurok minden egyes iterációja esetén.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>A diagram eredményeinek megjelenítésével megtekintheti a fejlesztést

Diagram létrehozása a és a alkalmazásból `count` `rewards` .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Diagram futtatása 10 000 Rank-kérelmekhez

Futtassa a `createChart` függvényt.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>A diagram olvasása

Ez a diagram a modell sikerességét mutatja az aktuális alapértelmezett tanulási szabályzat esetében.

![Ez a diagram a jelenlegi tanulási szabályzat sikerességét mutatja be a teszt időtartamára.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Az ideális cél, amely a teszt végére esik, a hurok átlagosan 100%-os sikerességi arányt eredményez, amely mínusz a feltárás. A feltárás alapértelmezett értéke 20%.

`100-20=80`

Ez a kutatási érték a személyre szabott erőforráshoz tartozó Azure Portal található a **konfiguráció** lapon.

Ha jobb tanulási szabályzatot szeretne találni a rangsor API adatai alapján, a személyre szabott hurokhoz futtasson egy [Offline értékelést](how-to-offline-evaluation.md) a portálon.

## <a name="run-an-offline-evaluation"></a>Offline kiértékelés futtatása

1. A Azure Portal nyissa meg a személyre szabott erőforrás- **értékelések** lapot.
1. Válassza az **Értékelés létrehozása**lehetőséget.
1. Adja meg a kiértékelési név és a dátumtartomány értékeit a hurok kiértékeléséhez. A dátumtartomány csak azokat a napokat tartalmazza, amelyeknek a kiértékelésére koncentrál.
    ![A Azure Portal nyissa meg a személyre szabott erőforrás-értékelések lapot. Válassza az értékelés létrehozása lehetőséget. Adja meg a kiértékelés nevét és a dátumtartományt.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Az offline értékelés futtatásának célja annak megállapítása, hogy van-e jobb tanulási szabályzat az ebben a hurokban használt funkciókhoz és műveletekhez. Ha szeretné megkeresni a jobb tanulási szabályzatot, ellenőrizze, hogy az **optimalizálási felderítés** be van-e kapcsolva.

1. A kiértékelés elindításához kattintson **az OK gombra** .
1. Ez az **értékelési** oldal felsorolja az új értékelést és a jelenlegi állapotát. Attól függően, hogy mennyi adattal rendelkezik, ez a kiértékelés hosszabb időt is igénybe vehet. A találatok megjelenítéséhez néhány perc múlva visszatérhet erre az oldalra.
1. A kiértékelés befejezése után válassza ki a kiértékelést, majd válassza a **különböző képzési szabályzatok összehasonlítását**. Ez megjeleníti a rendelkezésre álló képzési szabályzatokat, valamint azt, hogy azok hogyan viselkedjenek az adatkezelés során.
1. Válassza ki a legfelső szintű tanulási szabályzatot a táblázatban, és válassza az **alkalmaz**lehetőséget. Ez a _legjobb_ tanulási szabályzatot alkalmazza a modellre, és áttanítja őket.

## <a name="change-update-model-frequency-to-5-minutes"></a>Frissítési modell gyakoriságának módosítása 5 percre

1. A Azure Portal továbbra is a személyre szabott erőforrás lapon válassza a **konfiguráció** lapot.
1. Módosítsa a **modell frissítési gyakoriságát** és a **jutalmazási várakozási időt** 5 percre, majd válassza a **Mentés**lehetőséget.

További információ a [jutalmazási várakozási idő](concept-rewards.md#reward-wait-time) és a [modell frissítési gyakoriságáról](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Ellenőrizze, hogy a kimenet `rewardWaitTime` és `modelExportFrequency` mindkettő 5 percre van-e állítva.
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

## <a name="validate-new-learning-policy"></a>Új képzési szabályzat érvényesítése

Térjen vissza az Azure jegyzetfüzetbe, és folytassa ugyanezt a hurkot, de csak 2 000 iteráció esetén. Rendszeresen frissítse a metrikák diagramot a Azure Portal a szolgáltatás összes hívásának megtekintéséhez. Ha körülbelül 4 000 hívása van, az ismétlések rangsora és jutalma a hurok minden egyes iterációja esetén.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Diagram futtatása 2 000 Rank-kérelmekhez

Futtassa a `createChart` függvényt.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>A második diagram áttekintése

A második diagramon látható növekedés jelenik meg a rangsorban a felhasználói beállításokkal összehangoló rangsorolási előrejelzések között.

![A második diagramon látható növekedés jelenik meg a rangsorban a felhasználói beállításokkal összehangoló rangsorolási előrejelzések között.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem kívánja folytatni az oktatóanyag-sorozatot, törölje a következő erőforrásokat:

* Törölje az Azure notebook-projektet.
* A személyre szabott erőforrás törlése.

## <a name="next-steps"></a>Következő lépések

Az ebben a mintában használt [Jupyter-jegyzetfüzet és-adatfájlok](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) elérhetők a GitHub-tárházban a személyre szabáshoz.

