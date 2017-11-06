---
title: "Modell üzembe helyezése Azure Machine Learning-szolgáltatásokoz (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljes körűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez a 3. rész, amely a modell üzembe helyezését ismerteti."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/27/2017
ms.openlocfilehash: 048d734277f855086a48ad00a52b873adbf419b4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="classifying-iris-part-3-deploy-a-model"></a>Írisz osztályozása, 3. rész: Modell üzembe helyezése
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ez az oktatóanyag egy háromrészes sorozat harmadik része. Az oktatóanyagnak ebben a részében az Azure Machine Learning-szolgáltatások (előzetes verzió) segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> * A modellfájl megkeresése
> * Pontozó szkript és sémafájl létrehozása
> * A környezet előkészítése
> * Valós idejű webszolgáltatás létrehozása
> * A valós idejű webszolgáltatás futtatása
> * A kimeneti blobadatok vizsgálata 

 Az oktatóanyag az egyszerűség kedvéért a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren tapasztalható felhasználói élmény közel azonos.

## <a name="prerequisites"></a>Előfeltételek
Végezze el az oktatóanyag első két részét:

1. Kövesse az [Adatok előkészítése útmutató](tutorial-classifying-iris-part-1.md) lépéseit Azure Machine Learning-erőforrások létrehozásához és az Azure Machine Learning Workbench alkalmazás telepítéséhez.

2. Kövesse a [Modell létrehozása útmutató](tutorial-classifying-iris-part-2.md) lépéseit egy logisztikai regressziós modell létrehozásához az Azure Machine Learningben.

3. A Docker-motornak helyben kell telepítve lennie és helyben kell futnia. Alternatív megoldásként az üzembe helyezést az Azure egy Azure Container Service-fürtjében is elvégezheti.

## <a name="download-the-model-pickle-file"></a>A modell pickle-fájljának letöltése
Az oktatóanyag előző részében az **iris_sklearn.py** szkriptet helyileg, az Azure Machine Learning Workbench alkalmazásban futtattuk. Ez a művelet szerializálta a logisztikai regressziós modellt a népszerű Python-alapú objektumszerializáló csomag, a **[pickle](https://docs.python.org/2/library/pickle.html)** használatával. 

1. Indítsa el az **Azure Machine Learning Workbench** alkalmazást, és nyissa meg az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után az Azure Machine Learning Workbench bal oldali eszköztárán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza ki az **iris_sklearn.py** fájlt. A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a Python-kód.

4. Tekintse át az **iris_sklearn.py** fájlt és keresse meg, hol jött létre a pickle-fájl. A Control+F billentyűkombinációval nyissa meg a keresési párbeszédpanelt, és a Python-kódban keresse meg a **pickle** szót.

   Ez a kódrészlet megmutatja, hogyan jött létre a pickle kimeneti fájlja. A kimeneti pickle-fájl neve a lemezen **model.pkl**. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Keresse meg a modell pickle-fájlját egy korábbi futtatás kimeneti fájljai között.
   
   Az **iris_sklearn.py** szkript futtatásakor a szkript a modellfájlt az **outputs** mappába írta **model.pkl** néven. Ez a mappa abban a végrehajtási környezetben található, ahol a szkriptet futtatta, és nem a helyi projektmappában. 
   
   - A fájl megkereséséhez az Azure Machine Learning Workbench alkalmazásban kattintson a **Futtatások** gombra (óra ikon) a bal oldali eszköztáron a **Minden futtatás** lista megnyitásához.  
   - Ekkor megnyílik a **Minden futtatás** lap. A Futtatások táblán válassza ki valamelyik közelmúltbeli futtatást, ahol a cél értéke **helyi**, a szkript neve pedig **iris_sklearn.py** volt. 
   - Megnyílik a **Futtatás tulajdonságai** lap. Ennek a jobb felső részén található a **Kimenetek** rész. 
   - A pickle-fájl letöltéséhez jelölje be a **model.pkl** fájl melletti jelölőnégyzetet, majd kattintson a **Letöltés** gombra. Mentse a fájlt a projektmappa gyökérkönyvtárába. A későbbi lépések során szükség lesz rá.

   ![A pickle-fájl letöltése](media/tutorial-classifying-iris/download_model.png)

   Az **outputs** mappával kapcsolatos további információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

## <a name="get-scoring-and-schema-files"></a>Pontozó- és sémafájlok lekérése
A webszolgáltatás üzembe helyezéséhez a modellfájl mellett szükség van egy pontozó szkriptre és adott esetben egy, a webszolgáltatás bemeneti adataihoz tartozó sémára is. A pontozó szkript betölti a **model.pkl** fájlt az aktuális mappából, és a segítségével előállít egy újonnan előrejelzett Iris-osztályt.  

1. Indítsa el az **Azure Machine Learning Workbench** alkalmazást, és nyissa meg az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után az Azure Machine Learning Workbench bal oldali eszköztárán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza ki az **iris_score.py** fájlt. Megnyílik a Python-szkript. Ezt a fájlt használjuk pontozófájlként.

   ![Pontozófájl](media/tutorial-classifying-iris/model_data_collection.png)

4. A sémafájl lekéréséhez futtassa ezt a szkriptet. Válassza ki a **helyi** környezetet és az **iris-score.py** szkriptet a parancssorban, majd kattintson a **Futtatás** gombra. 

5. A szkript létrehoz az **outputs** mappában egy JSON-fájlt, amely a modellhez szükséges bemenetiadat-sémát tartalmazza.

6. Figyelje meg a Machine Learning Workbench ablak jobb oldalán található Feladatok panelt. Várja meg, hogy a legfrissebb **iris-score.py** feladat mellett a zöld **Befejezve** állapot jelenjen meg. Ezután kattintson a legfrissebb feladatfuttatáshoz tartozó **iris-score.py [1]** hiperhivatkozásra az **iris-score.py** futtatási részleteinek megtekintéséhez. 

7. A Futtatás tulajdonságai lap **Kimenetek** részében válassza ki az újonnan létrehozott **service_schema.json** fájlt. **Ellenőrizze** a fájlt, és kattintson a **Letöltés** elemre. Mentse a fájlt a projektmappa gyökérkönyvtárába.

8. Lépjen vissza az előző lapra, ahol megnyitotta az **iris-score.py** szkriptet. 

   Érdemes megfigyelni az adatgyűjtés használatát, amely lehetővé teszi modellbemenetek és előrejelzések rögzítését a webszolgáltatásból. A következő pontok érdekesek az adatgyűjtés szempontjából:

9. Tekintse át a ModelDataCollector fájlimportálási osztály tetején található kódot, amely a következő modelladat-gyűjtési funkciót tartalmazza:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. A következő kódsorok az **init()** függvényben példányosítják a ModellDataCollector osztályt:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. A következő kódsorok a **run(input_df)** függvényben bemeneti és előrejelzési adatokat gyűjtenek:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Most már készen áll arra, hogy előkészítse a környezetet a modell üzembe helyezéséhez.

>[!NOTE]
>A modellek üzembe helyezéséhez tulajdonosi hozzáféréssel kell rendelkeznie egy Azure-előfizetéshez.

## <a name="prepare-to-operationalize-locally"></a>Üzembe helyezés helyi előkészítése
Használja a _helyi módú_ üzembe helyezést a helyi számítógépen lévő Docker-tárolókban történő futtatáshoz.

A _helyi mód_ fejlesztési és tesztelési célokra használható. A Docker Engine-nek helyileg kell futnia a modell üzembe helyezése következő lépéseinek végrehajtásához. A parancsokkal kapcsolatos segítségért használja a parancsok végén lévő `-h` jelzőt.

>[!NOTE]
>Ha nincs helyi Docker-motor, akkor másik megoldásként létrehozhat egy fürtöt az Azure-ban az üzembe helyezéshez. Ilyenkor ügyeljen arra, hogy az oktatóanyag elvégzését követően törölje a fürtöt, hogy az ne járjon további költségekkel.

1. Nyissa meg a parancssori felület. Az Azure Machine Learning Workbench ablak Fájl menüjében kattintson a **Parancssor megnyitása** lehetőségre.

   Ekkor megnyílik a parancssor az aktuális projektmappában (**c:\temp\myIris>**).

2. Győződjön meg arról, hogy a **Microsoft.ContainerRegistry** Azure-erőforrásszolgáltató regisztrálva van az előfizetésben. Regisztrálja ezt az erőforrás-szolgáltatót, mielőtt létrehozna egy környezetet a 3. lépésben. A következő paranccsal ellenőrizheti, hogy regisztrálva van-e már:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   A következőhöz hasonló kimenetnek kell megjelennie: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Ha a **Microsoft.ContainerRegistry** nincs regisztrálva, a következő paranccsal regisztrálhatja:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   A regisztráció néhány percet vesz igénybe, és az állapotát a fenti **az provider list** paranccsal vagy a következő paranccsal ellenőrizheti:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A kimenet harmadik sora a következő: **"registrationState": "Registering"**. Várjon néhány pillanatot, és ismételje meg a bemutatott parancsot, amíg a kimenet a következő nem lesz: **"registrationState": "Registered"**.

3. Hozza létre a környezetet. Ezt a lépést környezetenként egyszer futtatni kell (például egyszer fejlesztési, és egyszer éles környezetben). Ehhez az első környezethez használja a _helyi módot_. (A következő parancsban a `-c` vagy a `--cluster` kapcsolóval egy _fürt módú_ környezetet is létrehozhat később.)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Kövesse a képernyőn megjelenő utasításokat, amelyekkel üzembe helyezhet egy tárfiókot Docker-képek tárolásához, egy ACR-t (Azure Container Registryt) Docker-képek listázásához és egy AppInsight-fiókot telemetria gyűjtéséhez. Ha a `-c` kapcsolót használta, az létrehoz egy ACS- (Azure Container Service-) fürtöt is.
   
   A fürt nevével azonosítható a környezet. A hely legyen ugyanaz, mint az Azure Portalról létrehozott modellkezelési fiók helye.

4. Hozzon létre egy modellkezelési fiókot (ez egy egyszeri beállítás)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Állítsa be a modellkezelési fiókot  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Állítsa be a környezetet.
A beállítás befejezése után állítsa be az üzembe helyezéshez szükséges környezeti változókat a következő paranccsal. Használja ugyanazt a környezetnevet, amelyet korábban, a 4. lépésben használt. Használja ugyanazt az erőforráscsoport-nevet, amelyet a beállítási folyamat végeztével a parancsablakban kimenetként kapott.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. A következő paranccsal ellenőrizze, hogy megfelelően konfigurálta-e az üzembe helyezett környezetet a helyi webszolgáltatás üzembe helyezéséhez:

   ```azurecli
   az ml env show
   ```

Most már készen áll a valós idejű webszolgáltatás létrehozására.

>[!NOTE]
>Újból felhasználhatja a modellkezelési fiókot és környezetet a webszolgáltatások későbbi üzembe helyezéséhez. Nem kell őket minden webszolgáltatáshoz külön-külön létrehozni. Egy fiókkal vagy környezettel több webszolgáltatás is lehet társítva.

## <a name="create-a-real-time-web-service-in-one-command"></a>Valós idejű webszolgáltatás létrehozása egy paranccsal
1. Valós idejű webszolgáltatás létrehozásához használja a következő parancsot:

   ```azurecli
   az ml service create realtime -f iris-score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Ez létrehoz egy webszolgáltatás-azonosítót, amelyet később felhasználhat.

   Az **az ml service create realtime** parancsot az alábbi kapcsolókkal lehet használni:
   * -n: alkalmazásnév, csak kisbetűkből állhat.
   * -f: pontozó szkript fájlneve
   * --model-file: modellfájl, ebben az esetben a model.pkl pickle-fájl
   * -r: a modell típusa, ebben az esetben egy python-modell
   * --collect-model-data true: engedélyezi az adatgyűjtést

   >[!IMPORTANT]
   >A szolgáltatásnév (amely az új Docker-rendszerkép neve is) csak kisbetűkből állhat. Ellenkező esetben a rendszer hibaüzenetet küld. 

2. A parancs futtatásakor a modellt és a pontozófájlt a rendszer feltölti a környezet beállítása során létrehozott tárfiókba. A telepítési folyamat összeállít egy Docker-rendszerképet, amely tartalmazza a modellt, a sémát és a pontozófájlt, majd leküldi azt az ACR-beállításjegyzékbe: **\<ACR_név\>.azureacr.io/\<rendszerképnév\>:\<verzió\>**. 

   Ezután lekéri a rendszerképet a helyi számítógépre, és a rendszerkép alapján elindít egy Docker-tárolót. Ha a környezet fürt üzemmódban lett konfigurálva, a Docker-tároló az ACS Kubernetes-fürtön lesz üzembe helyezve.

   Az üzembe helyezés részeként a webszolgáltatáshoz létrejön egy HTTP REST-végpont a helyi számítógépen. A parancs futtatása néhány percen belül befejeződik egy sikert jelző üzenettel, és a webszolgáltatás készen áll a használatra.

3. A futó Docker-tárolót a **docker ps** paranccsal tekintheti meg:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-using-separate-commands"></a>Valós idejű webszolgáltatás létrehozása külön parancsokkal
A fenti **az ml service create realtime** parancs alternatívájaként külön-külön is végrehajthatja a lépéseket. Először regisztrálja a modellt, majd hozza létre a jegyzéket, állítsa össze a Docker-rendszerképet, és hozza létre a webszolgáltatást. Ezzel a lépésenkénti megközelítéssel nagyobb rugalmasságot biztosíthat magának az egyes lépések végrehajtásakor, ráadásul így újból felhasználhatja az előző lépésben létrehozott entitásokat, és csak szükség esetén kell őket újraépítenie.

1. Regisztrálja a modellt a pickle-fájl nevének megadásával.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Ez a parancs létrehoz egy modellazonosítót.

2. Hozzon létre egy jegyzéket.

   Jegyzék létrehozásához használja a következő parancsot, és adja meg az előző lépésből származó modellazonosító-kimenetet:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris-score.py -r python -i <model ID> -s service_schema.json
   ```
   Ez a parancs létrehoz egy jegyzékazonosítót.

3. Hozzon létre egy Docker-rendszerképet.

   Docker-rendszerkép létrehozásához használja a következő parancsot, és adja meg a jegyzékazonosító kimenetét az előző lépésből:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Ez a parancs létrehoz egy Docker-rendszerképazonosítót.
   
4. A szolgáltatás létrehozása

   Szolgáltatás létrehozásához használja az alábbi parancsot, és adja meg a rendszerkép-azonosító kimenetét az előző lépésből:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Ez a parancs létrehoz egy webszolgáltatás-azonosítót.

Most már készen áll a webszolgáltatás futtatására.

## <a name="run-the-real-time-web-service"></a>A valós idejű webszolgáltatás futtatása

A futó **irisapp** webszolgáltatás teszteléséhez adjon meg egy JSON kódolású rekordot, amely egy négy véletlenszerű számból álló tömböt tartalmaz.

1. A webszolgáltatás létrehozásával mintaadatok is létrejöttek. Helyi módban való futtatáskor meghívhatja az **az ml service show realtime** parancsot. Ez a meghívás lekér egy mintául szolgáló futtatási parancsot, amellyel tesztelheti a szolgáltatást. A pontozás URL-címét is lekéri, amellyel belefoglalhatja a szolgáltatást az egyéni alkalmazásába:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. A szolgáltatás teszteléséhez hajtsa végre a visszaadott szolgáltatásfuttatási parancsot.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   A kimenet **„2”**, amely az előrejelzett osztály. (Az Ön eredménye ettől eltérő lehet.) 

3. Ha a parancssori felületen kívülről szeretné futtatni a szolgáltatást, le kell kérnie a kulcsokat a hitelesítéshez:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Az összegyűjtött adatok megtekintése az Azure Blob Storage-ban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Storage-fiókjait. Ehhez kattintson a **További szolgáltatások** elemre

3. A keresőmezőbe írja be a **Storage fiókok** kifejezést, és nyomja le az **Enter** billentyűt

4. A **Storage-fiókok** keresőoldalán válassza ki a környezetének megfelelő **Storage-fiók** erőforrást. 

   > [!TIP]
   > Annak megállapításához, hogy melyik Storage-fiókot használja nyissa meg az Azure Machine Learning Workbench alkalmazást, válassza ki a projektet, amelyen dolgozik, majd nyisson meg egy parancssort a **Fájl** menüből. A parancssorba írja be az `az ml env show -v` parancsot, és tekintse meg a *storage_account* értéket. Ez a Storage-fiók neve

5. Amikor megnyílik a **Storage-fiók** lapja, kattintson a bal oldali lista **Tárolók** elemére. Keresse meg a **modeldata** nevű tárolót. 
 
   Ha nem jelennek meg adatok, lehet, hogy az első webszolgáltatás-kérés elküldése után várnia kell kb. 10 percet ahhoz, hogy az adatok propagálódjanak a Storage-fiókba.

   Az adatok a következő tárolóútvonallal vannak a blobokba továbbítva:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Ezeket az adatokat felhasználhatja az Azure-blobokból. Számos különféle, Microsoft szoftvereket és nyílt forráskódú eszközöket használó eszköz van, például:

   - Azure ML Workbench: adja hozzá a CSV-fájlt adatforrásként, majd nyissa meg a CSV-fájlt az Azure ML Workbenchben. 
   - Excel: nyissa meg táblázatként a napi CSV-fájlokat.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): hozzon létre diagramokat a blobok CSV-adataiból.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): töltsön be CSV-adatokat egy Hive-táblába, és végezzen SQL-lekérdezéseket közvetlenül a blobon.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): hozzon létre DataFrame-et nagy mennyiségű CSV-adatból.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Következő lépések
A háromrészes oktatóanyag-sorozat jelen, harmadik részében megtanulhatta, hogyan használhatók az Azure Machine Learning-szolgáltatások a következőkre:
> [!div class="checklist"]
> * A modellfájl megkeresése
> * Pontozó szkript és sémafájl létrehozása
> * A környezet előkészítése
> * Valós idejű webszolgáltatás létrehozása
> * A valós idejű webszolgáltatás futtatása
> * A kimeneti blobadatok vizsgálata 

Sikeresen futtatott egy tanítási szkriptet különféle számítási környezetekben, létrehozott egy modellt, szerializálta a modellt, és üzembe helyezte azt egy Docker-alapú webszolgáltatással. 

Készen áll a fejlett adatelőkészítés elvégzésére:
> [!div class="nextstepaction"]
> [Fejlett adatelőkészítés](tutorial-bikeshare-dataprep.md)

