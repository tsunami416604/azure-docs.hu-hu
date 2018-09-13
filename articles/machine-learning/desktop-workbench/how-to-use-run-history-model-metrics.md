---
title: Hogyan Futtatás előzmények és modell mérőszámok az Azure Machine Learning Workbench |} A Microsoft Docs
description: Útmutató az Azure Machine Learning Workbench futtatási előzmények és modell metrikák funkciójának használatával
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 34fe72087a3de133d65ea4a4737ab5dba45242f4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645036"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Futtatási előzmények használata és a modell mérőszámok az Azure Machine Learning Workbenchben

Az Azure Machine Learning Workbench data science Kísérletezési keresztül támogatja a **futtatási előzmények** és **modell metrikák** funkciókat.
**Futtatási előzmények** segítségével nyomon követheti a machine learning-példakísérleteket kimeneteire, és lehetővé teszi a szűrési és az eredmények összehasonlítása.
**Metrikák modell** parancsfájlját, függetlenül a data science kísérletek a legfontosabb értékei követési bármely pontról is naplózva.
Ez a cikk ismerteti, hogyan javíthatja ezeket a szolgáltatásokat a sebesség és a data science Kísérletezési minőségének növelése eredményes használatához.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató elvégezhető, kell tennie:
* [Hozzon létre és telepítse az Azure Machine Learning](../service/quickstart-installation.md)
- [Projekt létrehozása](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Az Azure ml saját naplózási API – áttekintés
A [Azure ML API-naplózás](reference-logging-api.md) keresztül érhető el a **azureml.logging** modul Python (amennyiben van telepítve, az Azure ML workbenchben.) Ez a modul az importálás után is használhatja a **get_azureml_logger** metódus példányosítása egy **naplózó** objektum.
Ezután használhatja a naplózó **log** a Python-szkriptek által előállított metódus kulcs-érték párok tárolására.
Jelenleg skaláris naplózási modell metrikáit és a lista típusok támogatottak látható módon.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
A naplózó belül az Azure ML Workbench projektek használatához egyszerűen, és ez a cikk bemutatja, hogyan.

## <a name="create-a-project-in-azure-ml-workbench"></a>Az Azure ML Workbench-projekt létrehozása
Ha még nem rendelkezik egy projektet, létrehozhat egyet a [létrehozása és telepítése a rövid útmutató](../service/quickstart-installation.md) származó a **projekt-irányítópult**, megnyithatja a **iris_sklearn.py** parancsfájl () ahogy.)

![a fájlok lapon a parancsfájl elérése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Használhatja ezt a szkriptet segítségképp bejelentkezett az Azure gépi Tanulási modell metrika várt végrehajtásához.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Paraméterezni és jelentkezhet be a modell metrikák a parancsfájl
Az a **iris_sklearn.py** szkriptet, az importálás és a Python naplózó lehet szűkíteni a következő kódsorokat szerkezet várt mintának.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Létrehozása után hívhatók meg a **log** módszer bármely név-érték pár.

Fejlesztési befejeződése után célszerű gyakran paraméterezni a parancsfájlokat úgy, hogy az értékek a parancssor használatával adhatók át.
Az alábbi minta bemutatja, hogyan fogadja el a Python-kódtárakat használó parancssori paraméterek (ha van ilyen).
Ez a szkript egyetlen paramétert szükséges időt a Regularizációs arány (*reg*) használja annak érdekében, hogy növelje a besorolási modell igazítás *pontossága* overfitting nélkül.
Ezeket a változókat majd jelentkezett *Regularizációs arány* és *pontossága* úgy, hogy az optimális eredmények elérése érdekében a modell könnyedén azonosíthatók.

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

A fenti lépések végrehajtását a parancsfájlok engedélyezheti őket, hogy optimális használatát **futtatási előzmények**.

## <a name="launch-runs-from-project-dashboard"></a>Indítsa el a projekt-irányítópult fut
Térjen vissza a **projekt-irányítópult**, elindíthatja egy **nyomon követett futtatási** kiválasztásával a **iris_sklearn.py** parancsfájl, és írja be a **regularizációs arány**  paramétert a **argumentumok** szerkesztési mezőhöz.

![paraméterek megadásának és a futtatások indítása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Mivel a nyomon követett futtatások indítása nem tiltja le az Azure ML Workbench, több párhuzamos indulhat.
Minden egyes követett futtatási állapota látható az **feladatok Panel** látható módon.

![követési fut, a feladatok panel](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Ez lehetővé teszi optimális erőforrás-használatot, anélkül, hogy minden feladatot futtathat a soros.

## <a name="view-results-in-run-history"></a>Az eredmények futtatási előzmények megtekintése
Állapotának és eredményeinek megtekintéséhez a nyomon követett futtatások érhetők el az Azure ML Workbenchben elemzéshez **futtatási előzmények**.
**Futtatási előzmények** három különböző nézeteket biztosítja:
- Irányítópult
- Részletek
- Összehasonlítás

A **irányítópult** megtekintése az adatokat egy adott parancsfájl, a grafikus, mind táblázatos formában jelenik meg az összes kísérletei jeleníti meg.
A **részletek** a nézet jeleníti meg minden adat, létrehozott egy adott Futtatás egy adott parancsfájl, beleértve a naplózott mérőszámok és a kimeneti fájlok (például eredménykészletből jeleníti meg.) A **összehasonlító** nézet lehetővé teszi, hogy két vagy három futtatást kell egymás mellett megtekintett eredményeit, a naplózott mérőszámok, és a kimeneti fájlokat is beleértve.

Nyolc különböző nyomon követett futtatásának **iris_sklearn.py**, értékei a **regularizációs arány** paraméter és **pontossága** result megmutatják, hogyan használható a Futtatás kerültek naplózásra. Előzmények nézeteinek.

### <a name="run-history-dashboard"></a>Futtatási előzmények irányítópultja
Minden nyolc futtatásának eredményeit láthatók a **futtatási előzmények irányítópult**.
Mint **iris_sklearn.py** naplók *Regularizációs arány* és *pontossága*, a **futtatási előzmények irányítópult** ezeket az értékeket a diagramot jelenít meg Alapértelmezés szerint.

![futtatási előzmények irányítópultja](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

A **futtatási előzmények irányítópult** testre szabható, így naplózott értékeket is a rács jelennek meg.  Kattintson a **testreszabása** ikon megjelenik a **nézet testreszabása** párbeszéd látható módon.

![futtatási előzmények irányítópultján rács testreszabása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Bármely követett futtatások során naplózott értékek a következők jeleníthetők meg, és válassza **Regularizációs arány** és **pontossága** hozzáadja őket a rácsra.

![a testre szabott rácsban naplózott értékek](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Érdekes futtatások keresése az egérmutatót a diagramokban pontok könnyebbé vált.  Ebben az esetben a 7 futtatása egy jó pontosságát az alacsony időtartam összefüggő eredményezte.

![egy érdekes futtatás keresése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kattintson egy pont 7 futtatása bármely diagramra vagy a hivatkozásra kattintva futtassa 7 a rács jeleníti meg a társított a **Futtatás részletei**.

### <a name="run-history-details"></a>Futtatás részletei
Ebben a nézetben a Futtatás 7 futtatása 7 által létrehozott összes összetevőt együtt teljes eredményei jelennek meg.

![Futtatás részletei](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

A **Futtatás részletei** nézetet is lehetővé teszi, hogy **letöltése** írt fájlok a **. / kimenete** (e fájlok biztonsági az Azure ML Workbench mappa felhőtárhely a futtatási előzmények, egy másik cikk tárgyát képező.)

Végül **Futtatás részletei** lehetővé teszi a projekt visszaállítása állapotában futtatni időpontjában.
Kattintson a **visszaállítása** gombra a megerősítő párbeszédpanelen látható módon jelenik meg.

![Futtatás Visszaállítás megerősítése](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Megerősítést, ha a fájlok felülírhatja, vagy eltávolítani, tehát a funkció használatához alaposan.

### <a name="run-history-comparison"></a>Futtassa az előzmények összehasonlítása
Két vagy három beállítást fut a **futtatási előzmények irányítópult** parancsra **összehasonlítása** biztosítható, hogy az a **előzmények összehasonlító futtatása** megtekintése.
Másik lehetőségként kattintson **összehasonlítása** és a egy Futtatás belül a **Futtatás részletei** nézetet is kínál a **előzmények összehasonlító futtatása** megtekintése.
Mindkét esetben a **futtatása előzmények összehasonlító** nézet lehetővé teszi a naplózás és a két vagy három futtatást egymás melletti összetevők.

![Futtassa az előzmények összehasonlítása](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Ez a nézet különösen hasznos a grafikon összehasonlítása, de általában futtatások tulajdonságokat itt hasonlítható.

### <a name="command-line-interface"></a>Parancssori felület
Az Azure Machine Learning Workbench futtatási előzmények keresztül is hozzáférést biztosít a **parancssori felület**.
Hozzáférés a **parancssori felület**, kattintson a **parancssor megnyitása** menü látható módon.

![Nyissa meg a parancssor használatával](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Érhetők el a futtatási előzmények esetén használható parancsokat `az ml history`, az online súgó hozzáadásával érhető el a `-h` jelzőt.
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
Ezek a parancsok ugyanazokat a szolgáltatásokat biztosít, és látható ugyanazokat az adatokat adja vissza a **futtatása előzmények nézeteinek**.
Utolsó futtatás eredményeit például JSON-objektumként is megjeleníthetők.
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
Is minden Futtatás listájában is megjelenik a táblázatos formában.
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
A **parancssori felület** van egy alternatív út eléréséhez az Azure Machine Learning Workbench hatékonyságát.

## <a name="next-steps"></a>További lépések
Ezek a funkciók érhetők el, amelyek segítik a data science Kísérletezési folyamata.
Reméljük, hogy megtalálhassa azokat, hogy hasznos legyen, és nagy mértékben kellene értékeljük a visszajelzését.
Ez a kezdeti implementációjában, és tervezett fejlesztései nagy fokú van.
Örömmel várjuk a folyamatosan küldésére az Azure Machine Learning Workbench. 
