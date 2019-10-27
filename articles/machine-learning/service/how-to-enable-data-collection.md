---
title: Adatok gyűjtése az üzemi modelleken
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan gyűjthet Azure Machine Learning bemeneti modell adatait az Azure Blob Storage-ban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 25017e6ea0be5d4320832298cdadbec7ec5a05cc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929365"
---
# <a name="collect-data-for-models-in-production"></a>Adatok gyűjtése a termelési modellekhez

>[!IMPORTANT]
> Ez az SDK hamarosan kivonásra kerül. Ez az SDK továbbra is megfelelő a fejlesztők számára a modellekben való adateltolódás figyeléséhez, de a legtöbb fejlesztőnek az egyszerűsített [adatfigyelést](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)kell használnia Application Insightsokkal. 

Ebből a cikkből megtudhatja, hogyan gyűjtheti be a bemeneti modell adatait az Azure Kubernetes-fürtbe (ak) az Azure Blob Storage-ba üzembe helyezett Azure Machine Learningból. 

Ha engedélyezve van, az összegyűjtött adatok segítenek a következőkben:
* Az [adateltolódások monitorozása](how-to-monitor-data-drift.md) , mivel a termelési adatként a modellbe kerül

* Jobb döntések a modell újraképzése vagy optimalizálása során

* A modell újratanítása a gyűjtött adatokkal

## <a name="what-is-collected-and-where-does-it-go"></a>Mit gyűjt, és hol tart?

A következő adatokat gyűjthetjük össze:
* Az Azure Kubernetes-fürtben (ak) üzembe helyezett webszolgáltatások **bemeneti** adatainak modellezése (hang, képek és videók gyűjtése **nem** történik meg) 
  
* Modellek előrejelzése éles bemeneti adatok használatával.

> [!Note]
> Az ilyen adatmennyiségek előzetes összesítése vagy előszámításai nem részei a szolgáltatásnak.   

A kimenet egy Azure-blobban lesz mentve. Mivel az Azure-Blobba bekerül az adatgyűjtés, kiválaszthatja kedvenc eszközét az elemzés futtatásához. 

A blob kimeneti adatelérési útja a következő szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> Az SDK verziójában `0.1.0a16` előtt a `designation` argumentum neve `identifier`. Ha a kódot egy korábbi verzióval fejlesztették ki, akkor ennek megfelelően frissítenie kell.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Egy Azure Machine Learning munkaterület, egy helyi könyvtár, amely a parancsfájlokat tartalmazza, valamint a Azure Machine Learning SDK for Python telepítve van. Ebből a cikkből megtudhatja, hogyan kérheti le ezeket az előfeltételeket a [fejlesztői környezet konfigurálási](how-to-configure-environment.md) dokumentumának használatával.

- Az Azure Kubernetes Service (ak) szolgáltatásba üzembe helyezett, betanított gépi tanulási modell. Ha még nem rendelkezik ilyennel, tekintse meg a [betanítási modell besorolása](tutorial-train-models-with-aml.md) című oktatóanyagot.

- Egy Azure Kubernetes Service-fürt. További információ a létrehozásáról és telepítéséről: a [telepítés és](how-to-deploy-and-where.md) a dokumentum helye.

- [Állítsa be a környezetet](how-to-configure-environment.md) , és telepítse a [figyelési SDK](https://aka.ms/aml-monitoring-sdk)-t.

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése
Az adatgyűjtés a Azure Machine Learning vagy más eszközökön keresztül üzembe helyezett modelltől függetlenül is engedélyezhető. 

Az engedélyezéséhez a következőket kell tennie:

1. Nyissa meg a pontozási fájlt. 

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

    A *correlationId* egy opcionális paraméter, nem kell beállítania, ha a modell nem igényli. Ha a correlationId a helyén van, a könnyebben kitérképezhető más adattal. (Ilyenek például a következők: LoanNumber, Vevőkód stb.)
    
    Az *azonosító* később a mappa struktúrájának a blobban való létrehozásához használható, így a "nyers" és a "feldolgozott" adatmennyiség osztható fel.

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
    A AppInsights a konfiguráció módosításával is bekapcsolhatók:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Új rendszerkép létrehozásához és a szolgáltatás üzembe helyezéséhez tekintse meg a [hogyan helyezheti üzembe és hová](how-to-deploy-and-where.md) a dokumentumot.


Ha már van olyan szolgáltatás, amelynek függőségei a **környezet fájljában** és a **pontozási fájlban**vannak telepítve, engedélyezze az adatgyűjtést az alábbiak szerint:

1. Lépjen [Azure Portal](https://portal.azure.com).

1. Nyissa meg a munkaterületet.

1. Lépjen az **üzembe helyezések** ->  Select Service  -> **Szerkesztés** **elemre**.

   ![Szolgáltatás szerkesztése](media/how-to-enable-data-collection/EditService.PNG)

1. A **Speciális beállítások**területen törölje a **modell adatgyűjtésének engedélyezése**beállítást. 

    [Adatgyűjtés![](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Ebben az ablakban a szolgáltatás állapotának nyomon követéséhez a "Appinsights-diagnosztika engedélyezése" lehetőséget is választhatja.  

1. A módosítás alkalmazásához válassza a **frissítés** elemet.


## <a name="disable-data-collection"></a>Adatgyűjtés letiltása
Bármikor leállíthatja az adatgyűjtést. Az adatgyűjtés letiltásához használja a Python-kódot vagy a Azure Portal.

+ 1\. lehetőség – letiltás a Azure Portalban: 
  1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

  1. Nyissa meg a munkaterületet.

  1. Lépjen az **üzembe helyezések** ->  Select Service  -> **Szerkesztés** **elemre**.

     [![szerkesztési lehetőség](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. A **Speciális beállítások**területen törölje a **modell adatgyűjtésének engedélyezése**beállítást. 

     [Az adatgyűjtési![törlése](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. A módosítás alkalmazásához válassza a **frissítés** elemet.

  Ezeket a beállításokat a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)is elérheti.

+ 2\. lehetőség – az adatgyűjtés letiltása a Python használatával:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Ellenőrizze az adatait, és elemezze
Az Azure-Blobba összegyűjtött adatok elemzéséhez bármelyik előnyt kiválaszthatja. 

A blob adatainak gyors elérése:
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Nyissa meg a munkaterületet.
1. Kattintson a **tárterület**elemre.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse a blobban lévő kimeneti adatokat az alábbi szintaxissal:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Modell-adatok elemzése Power BI

1. [Power bi Desktop](https://www.powerbi.com) letöltése és megnyitása

1. Válassza **az adatlekérdezés** lehetőséget, majd kattintson az [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)lehetőségre.

    [![PBI blob beállítása](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adja meg a Storage-fiók nevét, és adja meg a Storage-kulcsát. Ezeket az információkat a blob **beállításaiban** találja > > hozzáférési kulcsokat. 

1. Válassza ki a tároló **modeldata** , és kattintson a **Szerkesztés**gombra. 

    [![PBI-navigátor](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezés-szerkesztőben kattintson a "Name" (név) oszlop alatt található, és adja hozzá az 1. Storage-fiókot. A modell elérési útja a szűrőbe. Megjegyzés: Ha csak egy adott évből vagy hónapból származó fájlokat szeretne megtekinteni, egyszerűen bontsa ki a szűrő elérési útját. Például tekintse meg a márciusi adatvesztést:/modeldata/subscriptionid >/resourcegroupname >/workspacename >/webservicename >/modelname >/modelversion > alapanyagok/megjelölés >/év >/3

1. A **név**alapján szűrheti a megfelelő adatokat. A **jóslatok** és a **bemenetek**tárolásához létre kell hoznia egy lekérdezést.

1. Kattintson a dupla nyílra félretéve a **tartalom** oszlopban a fájlok egyesítéséhez. 

    [![PBI tartalma](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kattintson az OK gombra, és az adatküldés megtörténik.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Most kattintson **a Bezárás gombra, és alkalmazza a** t.

1.  Ha hozzáadta a bemeneteket és az előrejelzéseket, a táblák automatikusan korrelálnak a **kérelemazonosító**.

1. Megkezdheti az egyéni jelentések összeállítását a modell adatain.


### <a name="analyzing-model-data-using-databricks"></a>Modell-adatok elemzése a Databricks használatával

1. Hozzon létre egy [Databricks-munkaterületet](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Nyissa meg a Databricks-munkaterületet. 

1. A databricks munkaterületen válassza az **adatok feltöltése**lehetőséget.

    [ADATBÁZIS![feltöltése](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Hozzon létre új táblát, és válassza az **egyéb adatforrások** – > Azure Blob Storage-> CREATE TABLE in notebook (jegyzetfüzetben) elemet.

    [![DB tábla](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Frissítse az adatai helyét. Például:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Dbsetup program](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Az adatai megtekintéséhez és elemzéséhez kövesse a sablon lépéseit. 

## <a name="example-notebook"></a>Példa jegyzetfüzetre

A [How-to-use-azureml/Deployment/Enable-Data-Collection-for-models-in-AKS/Enable-Data-Collection-for-models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) jegyzetfüzet bemutatja a cikkben szereplő fogalmakat.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
