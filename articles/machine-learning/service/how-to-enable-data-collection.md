---
title: Adatok gyűjtése a az üzemi modellekben
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan gyűjtheti az Azure Machine Learning bemeneti modell adatokat egy Azure Blob Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: a127a211157edb0b26d0495bc2ed05dd79323111
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842634"
---
# <a name="collect-data-for-models-in-production"></a>A modellek éles adatok gyűjtése

Ebből a cikkből megismerheti a bemeneti modell adatok gyűjtésére az Azure Machine Learning-szolgáltatások üzembe helyezte az Azure Kubernetes-fürt (AKS) egy Azure Blob storage-bA. 

Ha engedélyezve van, az összegyűjtött adatok segítségével:
* Üzemi környezetben elérhető adatokat beírja a modell adatait drifts figyelése

* Jobb döntéseket hozhat a mikor újratanítása vagy a modell optimalizálása

* Az összegyűjtött adatokat a modell újratanítása

## <a name="what-is-collected-and-where-does-it-go"></a>Gyűjtött adatok, és ahol, lépi?

A következő adatok gyűjthetők össze:
* Modell **bemeneti** adatokat az Azure Kubernetes-fürt (AKS) üzembe helyezve web servicesből (hang-, képeket, és vannak **nem** gyűjtött) 
  
* Modell előrejelzéses bemeneti termelési adatok használatával.

> [!Note]
> Előzetes összesítéssel, vagy ezeket az adatokat az üzem előtti számítások nem részei a szolgáltatás jelenleg.   

A kimenet egy Azure BLOB mentése. Mivel az adatok egy Azure-Blobba való hozzáadása, kiválaszthatja az elemzés futtatása a kedvenc eszközt. 

A kimeneti adatokat a BLOB elérési útja ezt a szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Egy Azure Machine Learning szolgáltatás munkaterületén, a parancsprogramok, és az Azure Machine Learning SDK-t tartalmazó Python telepítve van egy helyi könyvtárba. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

- Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) üzembe helyezni. Ha még nincs fiókja, tekintse meg a [kép osztályozási modell betanításához](tutorial-train-models-with-aml.md) oktatóanyag.

- Azure Kubernetes Service-fürt. A létrehozása és központi telepítését egy további információkért lásd: a [üzembe helyezése és hol](how-to-deploy-and-where.md) dokumentumot.

- [Állítsa be a környezetet](how-to-configure-environment.md) , és telepítse a [figyelési SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése
Az adatgyűjtés függetlenül a modell üzembe helyezéséhez az Azure Machine Learning szolgáltatás vagy más eszközök segítségével lehet engedélyezni. 

Az engedélyezéshez kell tennie:

1. Nyissa meg a pontozófájl. 

1. Adja hozzá a [az alábbi kód](https://aka.ms/aml-monitoring-sdk) , a fájl elejéhez:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Az adatok gyűjtemény változói a deklarálja a `init()` függvény:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Korrelációs azonosító* egy nem kötelező paraméter, nem kell állítsa be, ha a modell nem van szükség. A correlationId kellene helyen könnyíti meg az egyéb adatokkal, a könnyebb leképezés. (Példák: LoanNumber CustomerId, stb.)
    
    *Azonosító* későbbi a mappastruktúra a BLOB létrehozására használt, használat "feldolgozott" vagy "raw" adatok felosztásához.

3.  Adja hozzá a következő kódsort a `run(input_df)` függvény:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Adatgyűjtés **nem** az automatikusan beállított **igaz** központi telepítésekor a szolgáltatás az aks-ben, így frissítenie kell a konfigurációs fájlt például: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Ez a konfiguráció módosításával is bekapcsolható AppInsights szolgáltatás figyelésére:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Hozzon létre egy új rendszerképet, és telepítse a szolgáltatást, tekintse meg a [üzembe helyezése és hol](how-to-deploy-and-where.md) dokumentumot.


Ha már van egy szolgáltatás telepíti a függőségeket a **környezet fájl** és **pontozófájl**, által az adatgyűjtés engedélyezése:

1. Lépjen a [az Azure Portal](https://portal.azure.com).

1. Nyissa meg a munkaterületet.

1. Lépjen a **központi telepítések** -> **szolgáltatás kiválasztása** -> **szerkesztése**.

   ![Szolgáltatás szerkesztése](media/how-to-enable-data-collection/EditService.PNG)

1. A **speciális beállítások**, kapcsolja ki **engedélyezése a modelladatok gyűjtésének**. 

    [![Ellenőrizze az adatok gyűjtése](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Ebben az ablakban azt is beállíthatja "Az Appinsights-diagnosztika engedélyezése" a szolgáltatás állapotának nyomon követéséhez.  

1. Válassza ki **frissítés** a módosítás alkalmazására.


## <a name="disable-data-collection"></a>Adatgyűjtés letiltása
Adatgyűjtés bármikor leállíthatja. Python-kód vagy az Azure portal használatával tiltsa le az adatgyűjtést.

+ 1. lehetőség – tiltsa le az Azure Portalon: 
  1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

  1. Nyissa meg a munkaterületet.

  1. Lépjen a **központi telepítések** -> **szolgáltatás kiválasztása** -> **szerkesztése**.

     [![A beállítás szerkesztése](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. A **speciális beállítások**, kapcsolja ki **engedélyezése a modelladatok gyűjtésének**. 

     [![Törölje a jelet az adatgyűjtést](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Válassza ki **frissítés** a módosítás alkalmazására.

+ 2 – a Python használatával tiltsa le az adatgyűjtést. lehetőség:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Az adatok érvényesítése és elemezhetők a
Kiválaszthatja, hogy bármely eszköz a figyelembe vételével az Azure-Blobba való gyűjtött adatok elemzéséhez. 

Gyors eléréséhez az adatok a blobból:
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Nyissa meg a munkaterületet.
1. Kattintson a **tárolási**.

    [![Tároló](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse az elérési utat a kimeneti adatokat a BLOB ezzel a szintaxissal:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Modell adatelemzés a Power bi-ban

1. Töltse le és nyissa meg a [Power bi Desktopban](https://www.powerbi.com)

1. Válassza ki **adatok lekérése** , majd kattintson a [ **Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![A PBI Blob beállítása](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adja hozzá a tárfiók nevét, és adja meg a tárfiók-kulcsot. Ez az információ találhatja meg a blob **beállítások** >> hozzáférési kulcsokkal. 

1. Válassza ki a tárolót **modeldata** , majd kattintson a **szerkesztése**. 

    [![PBI-kezelő](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezés-szerkesztőben kattintson a "Name" oszlopban, és adja hozzá a tárfiók 1. A szűrési modell elérési útja. Megjegyzés: Ha azt szeretné, csak meg fájlokba adott év és hónap, csak bontsa ki a szűrő elérési utat. Például mindössze csak nézze meg március adatokká: / modeldata/előfizetés azonosítója > / resourcegroupname > / workspacename > / webservicename > / modelname > / modelversion > / azonosítója > / év > / 3

1. Szűrje az adatokat, amelyek az Ön alapján számára **neve**. Tárolta **előrejelzéseket** és **bemenetek** kell minden egyes lekérdezés létrehozása.

1. A fenntartott dupla nyílra a **tartalom** oszlopot úgy, hogy a fájlokat. 

    [![PBI-tartalom](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kattintson az OK gombra, és az adatok előtöltése lesz.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Rákattinthat a **bezárásához és a alkalmazni** .

1.  Bemeneti és a táblák automatikusan az előrejelzéseket Ha összekapcsolását által **kérelemazonosító**.

1. Indítsa el az egyéni jelentések készítése a modell adatait.


### <a name="analyzing-model-data-using-databricks"></a>Databricks használatával modell-adatok elemzése

1. Hozzon létre egy [Databricks-munkaterület](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Nyissa meg a Databricks-munkaterület. 

1. A databricks munkaterületen válassza **adatok feltöltése**.

    [![Adatbázis feltöltése](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Új tábla létrehozásához, és válassza ki **egyéb adatforrásokból** -> Azure Blob Storage -> Create Table jegyzetfüzetben.

    [![DB-table](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Az adatok helyének frissítése. Például:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![A dbsetup program](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Kövesse a lépéseket annak érdekében, hogy megtekintheti és elemezheti az adatokat a sablonban. 

## <a name="example-notebook"></a>Példa notebook

A [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) notebook mutatja be a jelen cikk fogalmait.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
