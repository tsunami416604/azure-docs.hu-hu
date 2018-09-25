---
title: Az éles környezet – az Azure Machine Learning-modellek az adatgyűjtés engedélyezése
description: Ismerje meg, hogyan gyűjtheti az Azure Machine Learning bemeneti modell adatokat egy Azure Blob Storage.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 4730003508463583d6620527e05bc330be599d80
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032395"
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
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myResGrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- Az Azure Machine Learning-munkaterület, a parancsprogramok, és az Azure Machine Learning SDK-t tartalmazó Python telepítve van egy helyi könyvtárba. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

- Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) üzembe helyezni. Ha még nincs fiókja, tekintse meg a [kép osztályozási modell betanításához](tutorial-train-models-with-aml.md) oktatóanyag.

- Egy [AKS-fürt](how-to-deploy-to-aks.md).

- Az alábbi függőségeket és telepített modul [környezetében](how-to-configure-environment.md):
  + Linux:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Windows rendszeren:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése
Az adatgyűjtés függetlenül a modell üzembe helyezéséhez az Azure Machine Learning szolgáltatás vagy más eszközök segítségével lehet engedélyezni. 

Az engedélyezéshez kell tennie:

1. Nyissa meg a pontozófájl. 

1. Adja hozzá a következő kódot a fájl elejéhez:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Az adatok gyűjtemény változói a deklarálja a `init()` függvény:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Korrelációs azonosító* egy nem kötelező paraméter, nem kell állítsa be, ha a modell nem van szükség. A correlationId kellene helyen könnyíti meg az egyéb adatokkal, a könnyebb leképezés. (Példák: LoanNumber, a CustomerId, stb.)
    
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

5. [Hozzon létre új rendszerképet, és a szolgáltatás üzembe helyezése.](how-to-deploy-to-aks.md) 


Ha már van egy szolgáltatás telepíti a függőségeket a **környezet fájl** és **pontozófájl**, által az adatgyűjtés engedélyezése:

1. Lépjen a [az Azure Portal](https://portal.azure.com).

1. Nyissa meg a munkaterületet.

1. Lépjen a **központi telepítések** -> **szolgáltatás kiválasztása** -> **szerkesztése**.

   ![Szolgáltatás szerkesztése](media/how-to-enable-data-collection/EditService.png)

1. A **speciális beállítások**, kapcsolja ki **engedélyezése a modelladatok gyűjtésének**. 

   ![Törölje a jelet az adatgyűjtést](media/how-to-enable-data-collection/CheckDataCollection.png)

   Ebben az ablakban azt is beállíthatja "Az Appinsights-diagnosztika engedélyezése" a szolgáltatás állapotának nyomon követéséhez.  

1. Válassza ki **frissítés** a módosítás alkalmazására.


## <a name="disable-data-collection"></a>Adatgyűjtés letiltása
Adatgyűjtés bármikor leállíthatja. Python-kód vagy az Azure portal használatával tiltsa le az adatgyűjtést.

+ 1. lehetőség – tiltsa le az Azure Portalon: 
  1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

  1. Nyissa meg a munkaterületet.

  1. Lépjen a **központi telepítések** -> **szolgáltatás kiválasztása** -> **szerkesztése**.

     ![Szolgáltatás szerkesztése](media/how-to-enable-data-collection/EditService.png)

  1. A **speciális beállítások**, kapcsolja ki **engedélyezése a modelladatok gyűjtésének**. 

     ![Törölje a jelet az adatgyűjtést](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Válassza ki **frissítés** a módosítás alkalmazására.

* 2 – a Python használatával tiltsa le az adatgyűjtést. lehetőség:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Példa notebook

A `00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` notebook mutatja be a jelen cikk fogalmait.  

Ez a jegyzetfüzet lekérése:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]