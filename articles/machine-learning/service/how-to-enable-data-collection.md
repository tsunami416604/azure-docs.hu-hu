---
title: Adatok gyűjtése az üzemi modelleken
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan gyűjtheti az Azure Machine Learning bemeneti modell adatokat egy Azure Blob Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406454"
---
# <a name="collect-data-for-models-in-production"></a>A modellek éles adatok gyűjtése
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Ez az SDK hamarosan kivonásra kerül. Ez az SDK továbbra is megfelelő a fejlesztők számára a modellekben való adateltolódás figyeléséhez, de a legtöbb fejlesztőnek az egyszerűsített [adatfigyelést](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)kell használnia Application Insightsokkal. 

Ebből a cikkből megtudhatja, hogyan gyűjtheti be a bemeneti modell adatait az Azure Kubernetes-fürtbe (ak) az Azure Blob Storage-ba üzembe helyezett Azure Machine Learningból. 

Ha engedélyezve van, az összegyűjtött adatok segítségével:
* Az [adateltolódások monitorozása](how-to-monitor-data-drift.md) , mivel a termelési adatként a modellbe kerül

* Jobb döntéseket hozhat a mikor újratanítása vagy a modell optimalizálása

* Az összegyűjtött adatokat a modell újratanítása

## <a name="what-is-collected-and-where-does-it-go"></a>Gyűjtött adatok, és ahol, lépi?

A következő adatok gyűjthetők össze:
* Az Azure Kubernetes-fürtben (ak) üzembe helyezett webszolgáltatások **bemeneti** adatainak modellezése (hang, képek és videók gyűjtése **nem** történik meg) 
  
* Modellek előrejelzése éles bemeneti adatok használatával

> [!Note]
> Előzetes összesítéssel, vagy ezeket az adatokat az üzem előtti számítások nem részei a szolgáltatás jelenleg.   

A kimenet egy Azure BLOB mentése. Mivel az adatok egy Azure-Blobba való hozzáadása, kiválaszthatja az elemzés futtatása a kedvenc eszközt. 

A kimeneti adatokat a BLOB elérési útja ezt a szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> Az SDK verziójában `0.1.0a16` előtt a `designation` argumentum neve `identifier`. Ha a kódot egy korábbi verzióval fejlesztették ki, akkor ennek megfelelően frissítenie kell.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma

- Egy Azure Machine Learning munkaterület, egy helyi könyvtár, amely a parancsfájlokat tartalmazza, valamint a Azure Machine Learning SDK for Python telepítve van. Ismerje meg, hogyan kérheti le ezeket az előfeltételeket a [fejlesztői környezet konfigurálási](how-to-configure-environment.md) dokumentumának használatával

- Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) üzembe helyezni. Ha még nem rendelkezik ilyennel, tekintse meg a következő témakört: a [rendszerképek besorolási modellje](tutorial-train-models-with-aml.md) oktatóanyaga

- Azure Kubernetes Service-fürt. További információ a létrehozásáról és telepítéséről: a [telepítés és](how-to-deploy-and-where.md) a dokumentum helye

- A [környezet beállítása](how-to-configure-environment.md) és a [figyelési SDK](https://aka.ms/aml-monitoring-sdk) telepítése

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése
Az adatgyűjtés a Azure Machine Learning vagy más eszközökön keresztül üzembe helyezett modelltől függetlenül is engedélyezhető. 

Az engedélyezéshez kell tennie:

1. A pontozási fájl megnyitása

1. Adja hozzá a [következő kódot](https://aka.ms/aml-monitoring-sdk) a fájl elejéhez:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Az adatgyűjtési változók deklarálása a `init()` függvényben:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    A *correlationId* egy opcionális paraméter, nem kell beállítania, ha a modell nem igényli. A correlationId kellene helyen könnyíti meg az egyéb adatokkal, a könnyebb leképezés. (Példák: LoanNumber, a CustomerId, stb.)
    
    Az *azonosítót* később a rendszer a blobban lévő mappastruktúrát felépítésére használja fel, amellyel a "nyers" és a "feldolgozott" adat osztható fel

3.  Adja hozzá a következő sornyi kódot a `run(input_df)` függvényhez:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Az adatgyűjtést a rendszer **nem** állítja be automatikusan **true** értékre, ha az AK-ban helyez üzembe egy szolgáltatást, ezért frissítenie kell a konfigurációs fájlt, például a következőt: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Ez a konfiguráció módosításával is bekapcsolható AppInsights szolgáltatás figyelésére:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Új rendszerkép létrehozásához és a szolgáltatás üzembe helyezéséhez lásd: az [üzembe helyezés és](how-to-deploy-and-where.md) a dokumentum helye


Ha már van olyan szolgáltatás, amelynek függőségei a **környezet fájljában** és a **pontozási fájlban**vannak telepítve, engedélyezze az adatgyűjtést az alábbiak szerint:

1. Ugrás a [Azure Machine learning studióra](https://ml.azure.com)

1. Munkaterület megnyitása

1. Ugrás az **üzembe helyezések** -> **válassza ki a szolgáltatás** -> **Szerkesztés**

   ![Szolgáltatás szerkesztése](media/how-to-enable-data-collection/EditService.PNG)

1. A **Speciális beállítások**területen válassza a **modell-adatgyűjtés engedélyezése** lehetőséget.

    [Adatgyűjtés ![](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Ebben az ablakban azt is megteheti, hogy a szolgáltatás állapotának nyomon követéséhez a "Appinsights-diagnosztika engedélyezése" lehetőséget választja.

1. A módosítás alkalmazásához kattintson a **frissítés** elemre.


## <a name="disable-data-collection"></a>Adatgyűjtés letiltása
Adatgyűjtés bármikor leállíthatja. A Python-kód vagy a Azure Machine Learning Studio használatával tiltsa le az adatgyűjtést.

+ 1\. lehetőség – letiltás Azure Machine Learning Studióban: 
  1. Bejelentkezés [Azure Machine learning studióba](https://ml.azure.com)

  1. Munkaterület megnyitása

  1. Ugrás az **üzembe helyezések** -> **válassza ki a szolgáltatás** -> **Szerkesztés**

     [![szerkesztési lehetőség](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. A **Speciális beállítások**területen törölje a **modell adatgyűjtésének engedélyezése** lehetőséget.

     [Az adatgyűjtési ![törlése](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. A módosítás alkalmazásához kattintson a **frissítés** elemre.

  Ezeket a beállításokat [Azure Machine learning Studióban](https://ml.azure.com)is elérheti a munkaterületén.

+ 2 – a Python használatával tiltsa le az adatgyűjtést. lehetőség:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Az adatok érvényesítése és elemezhetők a
Kiválaszthatja, hogy bármely eszköz a figyelembe vételével az Azure-Blobba való gyűjtött adatok elemzéséhez.

Gyors eléréséhez az adatok a blobból:

1. Bejelentkezés [Azure Machine learning studióba](https://ml.azure.com)

1. Munkaterület megnyitása
1. Kattintson a **tárterület** elemre.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse az elérési utat a kimeneti adatokat a BLOB ezzel a szintaxissal:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Modell adatelemzés a Power bi-ban

1. [Power bi Desktop](https://www.powerbi.com) letöltése és megnyitása

1. Válassza **az adatlekérdezés** lehetőséget, majd kattintson az [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![PBI blob beállítása](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adja hozzá a tárfiók nevét, és adja meg a tárfiók-kulcsot. Ezeket az információkat a blob **beállításaiban** találja > > hozzáférési kulcsok

1. Válassza ki a tároló **modeldata** , és kattintson a **Szerkesztés** gombra.

    [![PBI-navigátor](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezés-szerkesztőben kattintson a "Name" oszlopban, és adja hozzá a tárfiók 1. A szűrési modell elérési útja. Megjegyzés: Ha azt szeretné, csak meg fájlokba adott év és hónap, csak bontsa ki a szűrő elérési utat. Például tekintse meg a márciusi adatvesztést:/modeldata/subscriptionid >/resourcegroupname >/workspacename >/webservicename >/modelname >/modelversion > alapanyagok/megjelölés >/év >/3

1. A **név**alapján szűrheti a megfelelő adatokat. A **jóslatok** és a **bemenetek**tárolásához létre kell hoznia egy lekérdezést az egyes

1. Kattintson a dupla nyílra félretéve a **tartalom** oszlopban a fájlok egyesítéséhez

    [![PBI tartalma](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kattintson az OK gombra, és az adatküldés

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Most kattintson **a Bezárás gombra, és alkalmazza**

1.  Ha hozzáadta a bemeneteket és az előrejelzéseket, a táblák automatikusan korrelálnak a **kérelemazonosító**

1. Az egyéni jelentések összeállításának megkezdése a modell adataira


### <a name="analyzing-model-data-using-databricks"></a>Databricks használatával modell-adatok elemzése

1. Databricks- [munkaterület](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) létrehozása

1. Ugrás a Databricks-munkaterületre

1. A databricks munkaterületen válassza az **adatok feltöltése** lehetőséget.

    [ADATBÁZIS ![feltöltése](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Új tábla létrehozása és **más adatforrások** kiválasztása – > Azure Blob Storage-> CREATE TABLE in notebook

    [![DB tábla](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Az adatok helyének frissítése. Például:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Dbsetup program](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. A sablon lépéseinek követésével megtekintheti és elemezheti az adatait

## <a name="example-notebook"></a>Példa notebook

A [How-to-use-azureml/Deployment/Enable-Data-Collection-for-models-in-AKS/Enable-Data-Collection-for-models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) jegyzetfüzet bemutatja a cikkben szereplő fogalmakat.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
