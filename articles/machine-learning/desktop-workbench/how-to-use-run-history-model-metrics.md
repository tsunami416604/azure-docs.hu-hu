---
title: Hogyan Futtatás előzmények és az Azure-ban modell metrikák gép munkaterület tanulási |} Microsoft Docs
description: Útmutató az Azure Machine Learning-munkaterület az előzmények futtatása és a modell metrikák funkciók használatához
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dddf6af506269c7f22cea69e39b55effa2f14d63
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Használjon futtatási előzményei és az Azure Machine Learning-munkaterület modell metrikák

Az Azure Machine Learning-munkaterület adatok tudományos kísérletezhet keresztül támogatja a **futtatása előzmények** és **modell metrikák** szolgáltatásokat.
**Futtatási előzményei** segítségével nyomon követheti a machine learning kísérleteket kimenetének, és lehetővé teszi a szűrés és az eredmények összehasonlítása.
**A modell metrikák** bejelentkezhet a parancsfájlok, függetlenül az adatok tudományos kísérletekben legfontosabb értékei követési egyetlen pontról.
A cikkből megtudhatja, hogyan végezheti el ezeket a funkciókat, növelje a sebesség és az adatok tudományos kísérletezhet minőségének eredményes használatához.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, kell:
* [Hozzon létre és telepítse az Azure gépi tanulás](../service/quickstart-installation.md)
- [A projekt létrehozása](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Az Azure ML naplózási API – áttekintés
A [Azure ML-naplózás API](reference-logging-api.md) keresztül érhető el a **azureml.logging** (amely telepítve van az Azure ML-munkaterület.) a Python modul Ez a modul importálása után is használhatja a **get_azureml_logger** metódus példányának létrehozása egy **naplózó** objektum.
Ezután használhatja a naplózó **napló** a Python-parancsfájl által visszaadott metódus kulcs/érték párok tárolására.
Jelenleg naplózási modell metrikája skaláris és lista típusok támogatottak látható módon.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Könnyen használható az Azure ML munkaterület projektek belül naplózó, és ez a cikk bemutatja, hogyan.

## <a name="create-a-project-in-azure-ml-workbench"></a>Az Azure ML munkaterület-projekt létrehozása
Ha még nem rendelkezik a projekt, létrehozhat egyet a [létrehozása és telepítése gyors üzembe helyezés](../service/quickstart-installation.md) a a **projekt irányítópultján**, megnyithatja a **iris_sklearn.py** parancsfájl () ahogy.)

![a parancsfájl eléréséhez a fájlok lap](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Ez a parancsfájl útmutatóként használhatja a várt végrehajtási modell mérőszám Azure ML-naplózás.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizálja és modell metrikák jelentkeznek, a parancsfájl
Az a **iris_sklearn.py** parancsfájl, a várt minta importálása és a Python naplózó csökkenthető, az alábbi kódsorokat szerkezet.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Létrehozása után hívhat meg a **napló** metódus egyetlen név-érték pár.

Ha fejlesztési befejeződött, célszerű gyakran parametrizálja parancsfájlok, így értékeket tudja átadni a parancssorból.
Az alábbi példa bemutatja, hogyan fogadja el a szabványos Python könyvtárak segítségével parancssori paraméterek (ha van ilyen).
Ezt a parancsfájlt egy egyetlen paramétert fogad, a exportügyletek sebessége (*reg*) használja annak érdekében, hogy növelje a besorolási modell megfelelően *pontossága* overfitting nélkül.
Ezek a változók vannak kerül naplózásra *exportügyletek arány* és *pontossága* , hogy optimális eredmények elérése érdekében a modell könnyen azonosítható legyen.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

A fenti lépések végrehajtását a parancsfájlokban engedélyezése, hogy állítsák be az optimális használat **futtatása előzmények**.

## <a name="launch-runs-from-project-dashboard"></a>Indítsa el a projekt irányítópultján fut
Visszatérés a **projekt irányítópultján**, indítja el egy **nyomon követett Futtatás** kiválasztásával a **iris_sklearn.py** parancsfájlt, majd gépelje be a **exportügyletek arány**  paramétere a **argumentumok** szerkesztési mezőhöz.

![sérült volt fut és paraméterek megadása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Mivel a nyomon követett futtatása indítása nem blokkolja az Azure ML munkaterület, több párhuzamosan indulhat.
Minden egyes követett futtatásához állapota látható a **feladatok Panel** látható módon.

![a feladatok panelen fut nyomon követése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Ez lehetővé teszi, hogy optimális erőforrás-használat anélkül, hogy minden feladat futtatásának sorozatszámát.

## <a name="view-results-in-run-history"></a>Az eredmények megtekintése futtatási előzményei
Állapotának és eredményeinek nyomon követett futtatása érhetők el az Azure ML munkaterület elemzés **futtatása előzmények**.
**Futtatási előzményei** három különböző nézeteket biztosítja:
- Irányítópult
- Részletek
- Összehasonlítása

A **irányítópult** nézet összes frissítési kísérletei során egy adott parancsfájl, grafikus, mind a táblázatos formában megjelenített adatokat jeleníti meg.
A **részletek** a nézet jeleníti meg az összes adat jön létre egy adott futni, hanem egy adott parancsfájl, beleértve a naplózott metrikák és a kimeneti fájlok (például egy megjelenített tevékenységtérkép.) A **összehasonlító** nézet lehetővé teszi a két vagy három futtatása kell egymás mellett megjelenített eredményeit, is beleértve metrikák naplózott kimeneti fájlok.

Nyolc keresztül nyomon követheti a frissítési kísérletei során **iris_sklearn.py**, értékei a **exportügyletek arány** paraméter és **pontossága** eredményét mutatja be a Futtatás naplózta Előzmények nézetek.

### <a name="run-history-dashboard"></a>Futtatási előzményei irányítópult
Minden nyolc futtatása eredményei láthatók a **futtatása előzmények irányítópult**.
Mint **iris_sklearn.py** naplók *exportügyletek arány* és *pontossága*, a **futtatása előzmények irányítópult** ezeket az értékeket a diagramot jelenít meg alapértelmezett érték.

![Futtatási előzményei irányítópult](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

A **futtatása előzmények irányítópult** testre szabható, hogy a naplózott értékek is megjelennek a rács.  Kattintson a **testreszabása** ikon megjelenítése a **nézet testreszabása** párbeszéd látható módon.

![futtatási előzményei irányítópult rács testreszabása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Bármely nyomon követett futtatása során naplózott értékei jeleníthetők meg, és választ **exportügyletek arány** és **pontossága** felveszi őket.

![testre szabott rácsban naplózott értékek](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Nem található érdekes futtatása pontok diagramok fölé.  Futtatás 7 ebben az esetben egy kis időtartam alapján kialakulhat egy jó pontossága eredményezte.

![Futtassa az érdekes keresése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kattintson egy pont futtatásához 7 bármelyik olyan diagram vagy a 7 futtassa a rács jelennek meg a társított a **futtatása előzmények részletek**.

### <a name="run-history-details"></a>Futtassa az előzmények részletei
Ebben a nézetben együtt futtatása 7 által előállított összetevőihez futtatása 7 teljes eredményei jelennek meg.

![Futtassa az előzmények részletei](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

A **futtatása előzmények részletek** nézet is lehetővé teszi a **letöltése** azon fájlok írása a **. / kimenete** (ezek a fájlok által támogatott Azure ML munkaterület mappa felhőalapú tárolás előzmények futtassa egy másik cikk tárgyát képező.)

Végezetül **futtatása előzmények részletek** lehetővé teszi a projekt visszaállítása állapotában ehhez a futtató időpontjában.
Kattintson a **visszaállítása** gomb látható jeleníti meg a megerősítését kérő párbeszédpanel.

![visszaállítási futtatás megerősítése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Ha megerősítette, a fájlok felülírhatja, vagy eltávolítja, így a szolgáltatás használatához gondosan.

### <a name="run-history-comparison"></a>Futtassa az előzmények összehasonlítása
Két vagy három kiválasztása fut a **futtatása előzmények irányítópult** elemre kattintva **összehasonlítása** biztosítható az Ön a **előzmények összehasonlítás futtassa** nézet.
Másik lehetőségként kattintson **összehasonlítása** , majd válassza a Futtatás belül a **előzmények részletek futtassa** nézet is jelent, a **előzmények összehasonlítás futtassa** nézet.
Mindkét esetben a **futtatása előzmények összehasonlító** nézet, a naplózott eredményeit, és a két vagy három futtatása egymás melletti összetevők módszert biztosít.

![Futtassa az előzmények összehasonlítása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Ez a nézet különösen hasznos az előkészítésére összehasonlítása, de általában olyan tulajdonsága fut, itt hasonlítható.

### <a name="command-line-interface"></a>Parancssori felület
Az Azure Machine Learning-munkaterület futtatása előzmények keresztül is hozzáférést biztosít a **parancssori felület**.
Hozzáférés a **parancssori felület**, kattintson a **nyissa meg a parancssort** menü látható módon.

![Nyissa meg a parancssort](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Elérhető előzmények futtassa a parancsokat keresztül elért `az ml history`, az online súgó elérhető hozzáadásával a `-h` jelzőt.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Ezek a parancsok azonos szolgáltatásokat nyújtanak, és térjen vissza a látható ugyanazokat az adatokat a **futtatása előzmények nézetek**.
Például a legutóbbi futtatás eredményeit jeleníthetőek JSON-objektumként.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Az összes futtatásának listáját is, jeleníthetőek táblázatos formában.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
A **parancssori felület** van egy alternatív út Azure Machine Learning-munkaterület a teljesítmény eléréséhez.

## <a name="next-steps"></a>További lépések
Ezek a funkciók érhetők el segít az adatok tudományos kísérletezhet folyamatát.
Reméljük, hogy azokat, hogy hasznos legyen, és jelentős mértékben fogadjuk visszajelzéseit.
Ez csak a kezdeti telepítés, és tervezett fejlesztései nagy mennyiségű van.
Kíváncsian folyamatosan kézbesíti őket az Azure Machine Learning-munkaterület. 
