---
title: "Modell üzembe helyezése Azure Machine Learning-szolgáltatásokoz (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljeskörűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez a harmadik rész, amely a modell üzembe helyezését ismerteti."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 11/29/2017
ms.openlocfilehash: 70286104db1b70aebd2f8b0feb4a0854b3cc2bb9
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Írisz osztályozása, 3. rész: Modell üzembe helyezése
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára létrehozott átfogó, integrált és fejlett adatelemzési megoldás. Az adatszakértők a használatával az adatok előkészítését, a kísérletek kidolgozását és a modellek felhőszinten való üzembe helyezését hajthatják végre.

Ez az oktatóanyag egy háromrészes sorozat harmadik része. Az oktatóanyagnak ebben a részében az Azure Machine Learning-szolgáltatások (előzetes verzió) segítségével a következőket hajtja végre:

> [!div class="checklist"]
> * A modellfájl megkeresése
> * Pontozószkript és sémafájl létrehozása.
> * A környezet előkészítése.
> * Valós idejű webszolgáltatás létrehozása.
> * A valós idejű webszolgáltatás futtatása.
> * A kimeneti blobadatok vizsgálata. 

 Az oktatóanyag a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren szinte azonos a felhasználói élmény.

## <a name="prerequisites"></a>Előfeltételek
Végezze el az oktatóanyag első két részét:

   * Kövesse az [Adatok előkészítése útmutató](tutorial-classifying-iris-part-1.md) lépéseit a Machine Learning-erőforrások létrehozásához és az Azure Machine Learning Workbench alkalmazás telepítéséhez.

   * Kövesse a [Modell-létrehozási útmutató](tutorial-classifying-iris-part-2.md) lépéseit egy logisztikai regressziós modell létrehozásához az Azure Machine Learningben.

Rendelkeznie kell egy helyben telepített és futtatott Docker-motorral. Alternatív megoldásként az üzembe helyezést az Azure egy Azure Container Service-fürtjében is elvégezheti.

## <a name="download-the-model-pickle-file"></a>A modell pickle-fájljának letöltése
Az oktatóanyag előző részében az **iris_sklearn.py** szkriptet helyileg, az Azure Machine Learning Workbenchben futtattuk. Ez a művelet szerializálta a logisztikai regressziós modellt a népszerű Python-alapú objektumszerializáló csomag, a [pickle](https://docs.python.org/2/library/pickle.html) használatával. 

1. Nyissa meg a Machine Learning Workbench alkalmazást, majd az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után a bal oldali ablaktáblán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza ki az **iris_sklearn.py** fájlt. A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a Python-kód.

4. Tekintse át az **iris_sklearn.py** fájlt és keresse meg, hol jött létre a pickle-fájl. A Control+F billentyűkombinációval nyissa meg a **Keresés** párbeszédpanelt, és a Python-kódban keresse meg a **pickle** szót.

   Ez a kódrészlet megmutatja, hogyan jött létre a pickle kimeneti fájlja. A kimeneti pickle-fájl neve a lemezen **model.pkl**. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Keresse meg a modell pickle-fájlját egy korábbi futtatás kimeneti fájljai között.
   
   Az **iris_sklearn.py** szkript futtatásakor a szkript a modellfájlt az **outputs** mappába írta **model.pkl** néven. Ez a mappa abban a végrehajtási környezetben található, ahol a szkriptet futtatta, és nem a helyi projektmappában. 
   
   - A fájl kereséséhez kattintson a **Futtatások** gombra (óra ikon) a bal oldali ablaktáblán a **Minden futtatás** lista megnyitásához.  
   - Ekkor megnyílik a **Minden futtatás** lap. A futtatások tábláján válassza ki valamelyik közelmúltbeli futtatást, ahol a cél értéke **helyi**, a szkript neve pedig **iris_sklearn.py** volt. 
   - Megnyílik a **Futtatás tulajdonságai** ablaktábla. Az ablaktábla jobb felső részén látható a **Kimenetek** terület. 
   - A pickle-fájl letöltéséhez jelölje be a **model.pkl** fájl melletti jelölőnégyzetet, majd kattintson a **Letöltés** gombra. Mentse a fájlt a projektmappa gyökérkönyvtárába. A fájlra a későbbi lépések során szükség lesz.

   ![A pickle-fájl letöltése](media/tutorial-classifying-iris/download_model.png)

   Az `outputs` mappával kapcsolatos további információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

## <a name="get-the-scoring-script-and-schema-files"></a>Pontozó szkript és sémafájlok beszerzése
A webszolgáltatás üzembe helyezéséhez a modellfájl mellett szükség van egy pontozószkriptre és adott esetben egy, a webszolgáltatás bemeneti adataihoz tartozó sémára is. A pontozó szkript betölti a **model.pkl** fájlt az aktuális mappából, és a segítségével előállít egy újonnan előrejelzett Iris-osztályt.  

1. Indítsa el az Azure Machine Learning Workbench alkalmazást, és nyissa meg az oktatóanyag előző részében létrehozott **myIris** projektet.

2. A projekt megnyitása után a bal oldali ablaktáblán kattintson a **Fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához.

3. Válassza a **score_iris.py** fájlt. Megnyílik a Python-szkript. Ezt a fájlt használjuk pontozófájlként.

   ![Pontozófájl](media/tutorial-classifying-iris/model_data_collection.png)

4. A sémafájl lekéréséhez futtassa a szkriptet. Válassza ki a **helyi** környezetet és a **score_iris.py** szkriptet a parancssorban, majd válassza a **Futtatás** gombot. 

5. A szkript létrehoz az **Outputs** szakaszban egy JSON-fájlt, amely a modellhez szükséges bemenetiadat-sémát tartalmazza.

6. Figyelje meg a **Projekt-irányítópult** panel jobb oldalán található **Feladatok** panelt. Várja meg, hogy a legfrissebb **score_iris.py** feladat mellett a zöld **Befejezve** állapot jelenjen meg. Ezután válassza a legfrissebb feladatfuttatáshoz tartozó **score_iris.py [1]** hiperhivatkozásra az **score_iris.py** futtatási részleteinek megtekintéséhez. 

7. A **Futtatás tulajdonságai** lap **Kimenetek** részében válassza ki az újonnan létrehozott **service_schema.json** fájlt.  Jelölje be a fájl neve melletti jelölőnégyzetet, majd válassza a **Letöltés** gombot. Mentse a fájlt a projektmappa gyökérkönyvtárába.

8. Lépjen vissza az előző lapra, ahol megnyitotta a **score_iris.py** szkriptet. Az adatgyűjtés lehetővé teszi modellbemenetek és előrejelzések rögzítését a webszolgáltatásból. A következő lépések érdekesek az adatgyűjtés szempontjából:

9. Tekintse át a **ModelDataCollector** fájlimportálási osztály tetején található kódot, mert tartalmazza a következő modelladat-gyűjtési funkciót:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Tekintse át a következő, a **ModelDataCollector** osztályt példányosító kódsorokat az **init()** függvényben:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Tekintse át a következő, bemeneti és előrejelzési adatokat gyűjtő kódsorokat a **run(input_df)** függvényben.

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Most már készen áll arra, hogy előkészítse a környezetet a modell üzembe helyezéséhez.



## <a name="prepare-to-operationalize-locally"></a>Üzembe helyezés helyi előkészítése
Használja a _helyi módú_ üzembe helyezést a helyi számítógépen lévő Docker-tárolókban történő futtatáshoz.

A _helyi mód_ fejlesztési és tesztelési célokra használható. A modell üzembe helyezésének következő lépéséhez helyileg kell futtatnia a Docker-motort. A parancsokkal kapcsolatos segítségért használja a parancsok végén lévő `-h` jelzőt.

>[!NOTE]
>Ha nincs helyi Docker-motor, akkor másik megoldásként létrehozhat egy fürtöt az Azure-ban az üzembe helyezéshez. Ilyenkor ügyeljen arra, hogy az oktatóanyag elvégzését követően törölje a fürtöt, hogy az ne járjon további költségekkel.

1. Nyissa meg a parancssori felületet (CLI)
   Az Azure Machine Learning Workbench alkalmazás **Fájl** menüjében válassza a **Parancssor megnyitása** lehetőséget.

   Ekkor megnyílik a parancssor az aktuális projektmappában (**c:\temp\myIris>**).

2. Győződjön meg arról, hogy a **Microsoft.ContainerRegistry** Azure-erőforrásszolgáltató regisztrálva van az előfizetésben. Ezt az erőforrás-szolgáltatót regisztrálnia kell, mielőtt létrehozna egy környezetet a 3. lépésben. A következő paranccsal ellenőrizheti, hogy regisztrálva van-e már:
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
   A regisztráció eltarthat pár percig. Az állapotát az előző **az provider list** paranccsal vagy a következő paranccsal ellenőrizheti:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A kimenet harmadik sora a következő: **"registrationState": "Registering"**. Várjon néhány pillanatot, és ismételje meg a **show** parancsot, amíg a kimenet a következő nem lesz: **"registrationState": "Registered"**.

3. Hozza létre a környezetet. Ezt a lépést környezetenként egyszer kell futtatnia. Például egyszer kell futtatni fejlesztési, és egyszer éles környezetben. Ehhez az első környezethez használja a _helyi módot_. A következő parancsban a `-c` vagy a `--cluster` kapcsolóval egy _fürtmódú_ környezetet is létrehozhat később.

   Vegye figyelembe, hogy a következő telepítési parancs használatához Közreműködői hozzáférés szükséges az előfizetéshez. Ha ezzel nem rendelkezik, akkor ahhoz az erőforráscsoporthoz szükséges Közreműködői hozzáférés, ahová a telepítést végzi. Utóbbi esetben meg kell adnia az erőforráscsoport nevét a telepítési parancs részeként a `-g` jelző használatával. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Kövesse a képernyőn megjelenő utasításokat, amelyekkel üzembe helyezhet egy tárfiókot Docker-rendszerképek tárolásához, egy Azure Container Registryt Docker-rendszerképek listázásához és egy AppInsight-fiókot telemetria gyűjtéséhez. Ha az `-c` kapcsolót használta, egy ACS- (Azure Container Service-) fürt is létrejön.
   
   A fürt nevével azonosítható a környezet. A hely legyen ugyanaz, mint az Azure Portalról létrehozott modellkezelési fiók helye.

4. Hozzon létre egy Modellkezelési fiókot. (Ez egy egyszeri beállítás.)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Állítsa be a modellkezelési fiókot.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Állítsa be a környezetet.

   A beállítás befejezése után az alábbi paranccsal állíthatja be a környezet üzembe helyezéséhez szükséges környezeti változókat. Használja ugyanazt a környezetnevet, amelyet korábban, a 4. lépésben használt. Használja ugyanazt az erőforráscsoport-nevet, amelyet a beállítási folyamat végeztével a parancsablakban kimenetként kapott.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. A következő paranccsal ellenőrizze, hogy megfelelően konfigurálta-e az üzembe helyezett környezetet a helyi webszolgáltatás üzembe helyezéséhez:

   ```azurecli
   az ml env show
   ```

Most már készen áll a valós idejű webszolgáltatás létrehozására.

>[!NOTE]
>Újból felhasználhatja a Modellkezelési fiókot és környezetet a webszolgáltatások későbbi üzembe helyezéséhez. Nem kell őket minden webszolgáltatáshoz külön-külön létrehozni. Egy fiókkal vagy környezettel több webszolgáltatás is lehet társítva.

## <a name="create-a-real-time-web-service-in-one-command"></a>Valós idejű webszolgáltatás létrehozása egy paranccsal
1. Valós idejű webszolgáltatás létrehozásához használja a következő parancsot:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c amlconfig\conda_dependencies.yml
   ```
   Ez a parancs létrehoz egy webszolgáltatás-azonosítót, amelyet később felhasználhat.

   Az **az ml service create realtime** parancsot az alábbi kapcsolókkal lehet használni:
   * `-n`: Az alkalmazás neve, csak kisbetűkből állhat.
   * `-f`: A pontozó szkript fájlneve
   * `--model-file`: A modellfájl. Ebben az esetben ez a model.pkl pickle-fájl.
   * `-r`: A modell típusa. Ebben az esetben ez a Python-modell.
   * `--collect-model-data true`: Ez engedélyezi az adatgyűjtést.
   * `-c`: A további csomagokat tartalmazó Conda-függőségfájl elérési útja.

   >[!IMPORTANT]
   >A szolgáltatásnév, amely az új Docker-rendszerkép neve is, csak kisbetűkből állhat. Ellenkező esetben a rendszer hibaüzenetet küld. 

2. A parancs futtatásakor a modellt és a pontozófájlt a rendszer feltölti a környezet beállítása során létrehozott tárfiókba. A telepítési folyamat összeállít egy Docker-rendszerképet, amely tartalmazza a modellt, a sémát és a pontozófájlt, majd leküldi azt az Azure Container Registrybe: **\<ACR_név\>.azureacr.io/\<rendszerképnév\>:\<verzió\>**. 

   A parancs lekéri a rendszerképet a helyi számítógépre, és a rendszerkép alapján elindít egy Docker-tárolót. Ha a környezet fürt üzemmódban lett konfigurálva, a Docker-tároló az Azure Cloud Services Kubernetes-fürtön lesz üzembe helyezve.

   Az üzembe helyezés részeként a webszolgáltatáshoz létrejön egy HTTP REST-végpont a helyi számítógépen. A parancs futtatása néhány percen belül befejeződik egy sikert jelző üzenettel, és a webszolgáltatás készen áll a használatra.

3. A futó Docker-tárolót a **docker ps** paranccsal tekintheti meg:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Valós idejű webszolgáltatás létrehozása külön parancsokkal
Az előző **az ml service create realtime** parancs alternatívájaként külön-külön is végrehajthatja a lépéseket. 

Először regisztrálja a modellt. Ezután hozza létre a jegyzéket, állítsa össze a Docker-rendszerképet, és hozza létre a webszolgáltatást. Ezzel a lépésekre bontott eljárással minden lépést rugalmasabban hajthat végre. Ráadásul így újból felhasználhatja az előző lépésben létrehozott entitásokat, és csak szükség esetén kell őket újraépítenie.

1. Regisztrálja a modellt a pickle-fájl nevének megadásával.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Ez a parancs létrehoz egy modellazonosítót.

2. Hozzon létre egy jegyzéket.

   Jegyzék létrehozásához használja az alábbi parancsot, és adja meg az előző lépésből származó modellazonosító-kimenetet:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Ez a parancs létrehoz egy jegyzékazonosítót.

3. Hozzon létre egy Docker-rendszerképet.

   Docker-rendszerkép létrehozásához használja a következő parancsot, és adja meg a jegyzékazonosító kimenetét az előző lépésből. Szükség esetén belefoglalhatja a Conda-függőségeket is a `-c` kapcsoló használatával.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c amlconfig\conda_dependencies.yml
   ```
   Ez a parancs létrehoz egy Docker-rendszerképazonosítót.
   
4. Hozza létre a szolgáltatást.

   Szolgáltatás létrehozásához használja az alábbi parancsot, és adja meg a rendszerkép-azonosító kimenetét az előző lépésből:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Ez a parancs létrehoz egy webszolgáltatás-azonosítót.

Most már készen áll a webszolgáltatás futtatására.

## <a name="run-the-real-time-web-service"></a>A valós idejű webszolgáltatás futtatása

A futó **irisapp** webszolgáltatás teszteléséhez használjon egy JSON kódolású rekordot, amely egy négy véletlenszerű számból álló tömböt tartalmaz:

1. A webszolgáltatás mintaadatokat tartalmaz. Helyi módban való futtatáskor meghívhatja az **az ml service usage realtime** parancsot. Ez a meghívás lekér egy mintául szolgáló futtatási parancsot, amellyel tesztelheti a szolgáltatást. A hívás a pontozás URL-címét is lekéri, amellyel belefoglalhatja a szolgáltatást az egyéni alkalmazásába:

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. A szolgáltatás teszteléséhez hajtsa végre a visszaadott szolgáltatásfuttatási parancsot:

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   A kimenet **„2”**, amely az előrejelzett osztály. (Az Ön eredménye ettől eltérő lehet.) 

3. A szolgáltatás parancssori felületen kívülről való futtatásához a szolgáltatást, le kell kérnie a kulcsokat a hitelesítéshez:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Az összegyűjtött adatok megtekintése az Azure Blob Storage-ban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Storage-fiókjait. Ehhez válassza a **További szolgáltatások** lehetőséget.

3. A keresőmezőbe írja be a **Storage-fiókok** kifejezést, majd nyomja le az **Enter** billentyűt.

4. A **Storage-fiókok** keresőoldalán válassza ki a környezetének megfelelő **Storage-fiók** erőforrást. 

   > [!TIP]
   > A használatban lévő Storage-fiók meghatározásához:
   > 1. Nyissa meg az Azure Machine Learning Workbenchet.
   > 2. Válassza ki a projektet, amelyen dolgozik.
   > 3. Nyisson meg egy parancssort a **Fájl** menüben.
   > 4. A parancssorban írja be az `az ml env show -v` parancsot, és ellenőrizze a *storage_account* értéket. Ez az Ön Storage-fiókjának neve

5. A **Storage-fiók** panel megnyílása után válassza a bal oldali listán a **Tárolók** elemet. Keresse meg a **modeldata** nevű tárolót. 
 
   Ha nem jelennek meg adatok, lehet, hogy az első webszolgáltatás-kérés elküldése után várnia kell kb. 10 percet ahhoz, hogy az adatok propagálódjanak a Storage-fiókba.

   Az adatok a következő tárolóútvonallal vannak a blobokba továbbítva:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Ezeket az adatokat felhasználhatja az Azure Blob Storage-ból. Számos különféle, Microsoft szoftvereket és nyílt forráskódú eszközöket használó eszköz van, például:

   - Azure Machine Learning: a CSV-fájlt adatforrásként való felvételével nyissa meg a CSV-fájlt. 
   - Excel: nyissa meg táblázatként a napi CSV-fájlokat.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): hozzon létre diagramokat a blobok CSV-adataiból.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): töltsön be CSV-adatokat egy Hive-táblába, és végezzen SQL-lekérdezéseket közvetlenül a blobokon.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): hozzon létre DataFrame-et nagy mennyiségű CSV-adatból.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Következő lépések
A háromrészes oktatóanyag-sorozat jelen, harmadik részében megtanulhatta, hogyan használhatók az Azure Machine Learning-szolgáltatások a következőkre:
> [!div class="checklist"]
> * A modellfájl megkeresése
> * Pontozószkript és sémafájl létrehozása.
> * A környezet előkészítése.
> * Valós idejű webszolgáltatás létrehozása.
> * A valós idejű webszolgáltatás futtatása.
> * A kimeneti blobadatok vizsgálata. 

Sikeresen futtatott egy tanítási szkriptet különféle számítási környezetekben, létrehozott egy modellt, szerializálta a modellt, és üzembe helyezte azt egy Docker-alapú webszolgáltatással. 

Most már készen áll a fejlett adat-előkészítés elvégzésére:
> [!div class="nextstepaction"]
> [Fejlett adatelőkészítés](tutorial-bikeshare-dataprep.md)
