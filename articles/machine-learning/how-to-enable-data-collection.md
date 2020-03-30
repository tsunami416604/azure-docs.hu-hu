---
title: Adatok gyűjtése a termelési modellekről
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan gyűjtheti össze az Azure Machine Learning bemeneti modell adatait az Azure Blob storage-ban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771393"
---
# <a name="collect-data-for-models-in-production"></a>Adatok gyűjtése a termelésben lévő modellekről

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Az Azure Machine Learning Monitoring SDK hamarosan megszűnik. Az SDK továbbra is megfelelő a fejlesztők számára, akik jelenleg az SDK-t használják a modellek adateltolódásának figyelésére. De az új ügyfelek számára azt javasoljuk, hogy az egyszerűsített [adatfigyelést](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)használja az Application Insights segítségével.

Ez a cikk bemutatja, hogyan gyűjtheti a bemeneti modell adatait az Azure Machine Learning. Azt is bemutatja, hogyan telepítheti a bemeneti adatokat egy Azure Kubernetes-szolgáltatás (AKS) fürtbe, és hogyan tárolhatja a kimeneti adatokat az Azure Blob storage-ban.

A adatgyűjtés engedélyezése után az összegyűjtött adatok a következőkben segítenek:

* [Az adatok eltolódásának figyelése,](how-to-monitor-data-drift.md) ahogy a termelési adatok bejutnak a modellbe.

* Hozzon jobb döntéseket amodell újratanításával vagy optimalizálásával kapcsolatban.

* A modell újratanítása az összegyűjtött adatokkal.

## <a name="what-is-collected-and-where-it-goes"></a>Mit gyűjtenek, és hová megy

A következő adatok gyűjthetők:

* Az AKS-fürtben üzembe helyezett webszolgáltatások bemeneti adatainak modellezése. A hang, a képek és a videók *nem* kerülnek összegyűjtésre.
  
* Modell-előrejelzések éles bemeneti adatok használatával.

>[!NOTE]
> Az adatok előzetes összesítése és előszámításajelenleg nem része a gyűjtési szolgáltatásnak.

A kimenet a Blob storage-ban kerül mentésre. Mivel az adatok hozzáadódnak a Blob storage-hoz, kiválaszthatja a kedvenc eszközét az elemzés futtatásához.

A blob kimeneti adatainak elérési útja ezt a szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Az Azure Machine Learning SDK for Python 0.1.0a16-os `designation` verziónál `identifier`korábbi verzióiban az argumentum neve . Ha a kódot egy korábbi verzióval fejlesztette ki, ennek megfelelően kell frissítenie.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://aka.ms/AMLFree) mielőtt elkezdené.

- Telepíteni kell egy AzureMachine Learning-munkaterületet, egy helyi könyvtárat, amely a parancsfájlokat és az Azure Machine Learning Python-alapú SDK-t tartalmazza. A telepítésükről a [Fejlesztői környezet konfigurálása](how-to-configure-environment.md)című témakörben olvashat.

- Egy betanított gépi tanulási modell t kell telepíteni az AKS-ben. Ha nem rendelkezik modellel, tekintse meg a [Vonat képbesorolási modell](tutorial-train-models-with-aml.md) oktatóanyag.

- Szüksége van egy AKS-fürtre. Az egyik létrehozásáról és üzembe helyezéséről a [Telepítés és hely című](how-to-deploy-and-where.md)témakörben talál további információt.

- [Állítsa be a környezetet,](how-to-configure-environment.md) és telepítse az [Azure Machine Learning Monitoring SDK-t.](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

Az Azure Machine Learning en vagy más eszközökön keresztül üzembe helyezett modelltől függetlenül engedélyezheti az adatgyűjtést.

Az adatgyűjtés engedélyezéséhez a következőket kell tennie:

1. Nyissa meg a pontozási fájlt.

1. Adja hozzá a [következő kódot](https://aka.ms/aml-monitoring-sdk) a fájl tetejére:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Az adatgyűjtési változók `init` deklarálása a funkcióban:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *A correlationId* egy választható paraméter. Nem kell használnia, ha a modellnek nincs rá szüksége. A *CorrelationId* használata megkönnyíti a más adatokkal való leképezést, például *a LoanNumber* vagy *a CustomerId.*
    
    Az *azonosító* paraméter később a blob ban lévő mappastruktúra létrehozásához használatos. Segítségével megkülönböztetheti a nyers adatokat a feldolgozott adatoktól.

1. Adja hozzá a következő `run(input_df)` kódsorokat a függvényhez:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Az adatgyűjtés *nem* lesz automatikusan **igaz** értékre állítva, ha egy szolgáltatást az AKS-ben telepít. Frissítse a konfigurációs fájlt, ahogy az a következő példában is:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Az Application Insights szolgáltatásfigyeléshez is engedélyezheti a konfiguráció módosításával:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Új lemezkép létrehozásához és a gépi tanulási modell központi telepítéséhez olvassa el az Üzembe helyezés és hol című [témakört.](how-to-deploy-and-where.md)

Ha már rendelkezik olyan szolgáltatással, amelyen a függőségek telepítve vannak a környezeti fájlban és a pontozási fájlban, az alábbi lépésekkel engedélyezze az adatgyűjtést:

1. Nyissa meg az [Azure Machine Learning et.](https://ml.azure.com)

1. Nyissa meg a munkaterületet.

1. Válassza **a Központi telepítések:** > **Válassza ki a szolgáltatás** > **szerkesztése**lehetőséget.

   ![A szolgáltatás szerkesztése](././media/how-to-enable-data-collection/EditService.PNG)

1. A **Speciális beállítások**csoportban válassza a **Modelladatgyűjtés engedélyezése**lehetőséget.

    [![Adatgyűjtés kiválasztása](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   **Az AppInsights-diagnosztika engedélyezése** lehetőséget is választhatja a szolgáltatás állapotának nyomon követéséhez.

1. A módosítások alkalmazásához válassza a **Frissítés** lehetőséget.

## <a name="disable-data-collection"></a>Adatgyűjtés letiltása

Bármikor leállíthatja az adatgyűjtést. A Python-kód vagy az Azure Machine Learning használatával letilthatja az adatgyűjtést.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>1. lehetőség – Az adatgyűjtés letiltása az Azure Machine Learningben

1. Jelentkezzen be az [Azure Machine Learning be.](https://ml.azure.com)

1. Nyissa meg a munkaterületet.

1. Válassza **a Központi telepítések:** > **Válassza ki a szolgáltatás** > **szerkesztése**lehetőséget.

   [![A Szerkesztés lehetőség kiválasztása](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. A **Speciális beállítások csoportban**törölje a törlés a **Modell adatgyűjtésének engedélyezése**lehetőséget.

    [![Az adatgyűjtés jelölőnégyzetjének törlése](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. A módosítás alkalmazásához válassza a **Frissítés** lehetőséget.

Ezeket a beállításokat az [Azure Machine Learning](https://ml.azure.com)munkaterületén is elérheti.

### <a name="option-2---use-python-to-disable-data-collection"></a>2. lehetőség - Az adatgyűjtés letiltása python használatával

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Az adatok ellenőrzése és elemzése

A Blob storage-ban gyűjtött adatok elemzéséhez választhat egy előnyben részesített eszközt.

### <a name="quickly-access-your-blob-data"></a>A blobadatok gyors elérése

1. Jelentkezzen be az [Azure Machine Learning be.](https://ml.azure.com)

1. Nyissa meg a munkaterületet.

1. Válassza a **Tárolás**lehetőséget.

    [![Válassza a Tárolás lehetőséget](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse a blob kimeneti adatainak elérési útját ezzel a szintaxissal:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Modelladatok elemzése a Power BI használatával

1. Töltse le és nyissa meg a [Power BI Desktopot.](https://www.powerbi.com)

1. Válassza **az Adatok beszereznie** és [**az Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)lehetőséget.

    [![A Power BI blob beállítása](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adja meg a tárfiók nevét, és adja meg a tárolási kulcsot. Ezeket az információkat a **blob Beállítások** > **hozzáférési kulcsai** kiválasztásával találhatja meg.

1. Jelölje ki a **modelladattárolót,** és válassza **a Szerkesztés**lehetőséget.

    [![Power BI Navigátor](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezésszerkesztőben kattintson a **Név** oszlopra, és adja hozzá a tárfiókot.

1. Írja be a modell elérési útját a szűrőbe. Ha csak egy adott évből vagy hónapból származó fájlokat szeretne keresni, csak bontsa ki a szűrő elérési útját. Ha például csak a márciusi adatokat szeretné megtekinteni, használja ezt a szűrőútvonalat:

   /modeldata/\<subscriptionid\<>/\<resourcegroupname>/\<workspacename>/ webservicename>/\<modelname>/\<modelversion>/\<designation>/\<year>/3

1. Szűrje az Ön számára releváns adatokat **a Név** értékek alapján. Ha az előrejelzéseket és a bemeneteket tárolta, mindegyikhez létre kell hoznia egy lekérdezést.

1. A fájlok egyesítéséhez jelölje ki a **Tartalom** oszlop fejléce melletti lefelé mutató kettős nyilat.

    [![Power BI-tartalom](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Válassza **az OK gombot.** Az adatok előre betöltődnek.

    [![Power BI fájlok egyesítése](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Válassza **a Bezárás és alkalmaz lehetőséget.**

1. Ha bemeneteket és előrejelzéseket adott hozzá, a táblák at a **RequestId** értékek automatikusan rendezik.

1. Kezdje el az egyéni jelentések et a modelladatokra építeni.

### <a name="analyze-model-data-using-azure-databricks"></a>Modelladatok elemzése az Azure Databricks használatával

1. Hozzon létre egy [Azure Databricks-munkaterületet.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Nyissa meg a Databricks munkaterületet.

1. A Databricks-munkaterületen válassza **az Adatok feltöltése**lehetőséget.

    [![Adattéglák adatok feltöltése beállításának kiválasztása](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Válassza **az Új tábla létrehozása** lehetőséget, majd az Egyéb **adatforrások** > **az Azure Blob Storage** > **Create Table in Notebook**lehetőséget.

    [![Databricks tábla létrehozása](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Az adatok helyének frissítése. Például:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks beállítása](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Az adatok megtekintéséhez és elemzéséhez kövesse a sablon lépéseit.
