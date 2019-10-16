---
title: A kiadás újdonságai
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning legújabb frissítéseivel, valamint a gépi tanulással és az adat-előkészítéssel kapcsolatos Python SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: f51b9c3032518fb66215126c5a8bf26ab9b59526
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331575"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning kibocsátási megjegyzések

Ebben a cikkben megismerheti Azure Machine Learning kiadásait.  A teljes SDK-hivatkozási tartalomért keresse fel a Azure Machine Learning [**fő SDK for Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referenciát tartalmazó oldalt. 

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK a Python v 1.0.69

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A modell magyarázatait a legjobb futtatásra korlátozza, és nem kell minden futtatáskor számítási magyarázatot felvennie. Ez a viselkedés a helyi, a távoli és az ADB esetében is változhat.
    + Igény szerinti modellhez kapcsolódó magyarázatok támogatása a felhasználói felületen
    + A psutil hozzáadva a automl függőségéhez, és a psutil Conda függőségként szerepel a amlcompute-ben.
    + Az előrejelzési adatkészletek esetében a heurisztikus késéssel és a gördülő ablak méretével kapcsolatos probléma kijavítva egy sor, ami lineáris algebra-hibákhoz vezethet
      + Kinyomtatva az előrejelzési futtatások heurisztikus meghatározású paramétereinek kinyomtatása.
  + **azureml – datadrift**
    + A védelem a kimeneti metrikák létrehozásakor lett hozzáadva, ha az adatkészletek szintjének eltolódása nem az első szakaszban található.
  + **azureml – összetétel – értelmezés**
    + azureml – összevont – magyarázat – a modell csomagja át lett nevezve a azureml-retribal-értelmezze
  + **azureml – mag**
    + API hozzáadása az adatkészletek regisztrációjának megszüntetéséhez. `dataset.unregister_all_versions()`
    + Az adatkészlet API-ját hozzáadta az adatváltozások időpontjához. `dataset.data_changed_time` kérdésre adott válaszban foglalt lépéseket.
    + A `FileDataset` és a `TabularDataset` felhasználható bemenetként `PythonScriptStep`, `EstimatorStep` és `HyperDriveStep` értékre Azure Machine Learning folyamat során
    + A `FileDataset.mount` teljesítményének javítása a nagy mennyiségű fájllal rendelkező mappák esetében
    + A Futtatás részleteiben szereplő ismert hibajelentések URL-címe hozzáadva.
    + Kijavítva egy hiba a Futtatás során. _metrics beolvasása, ahol a kérelmek sikertelenek lesznek, ha egy Futtatás túl sok gyermeket tartalmaz
    + Támogatás hozzáadva az Arcadia-fürtön történő hitelesítéshez.
    + A kísérlet objektum létrehozása vagy létrehozása a kísérletet a Azure Machine Learning munkaterületen a futtatási előzmények nyomon követéséhez. A kísérlet azonosítója és az archivált idő a létrehozáskor a kísérlet objektumban van feltöltve. Példa: Experiment = Experiment (munkaterület, "New Experiment") Experiment_ID = experiment.id Archive () és reactivate () függvények, amelyek meghívhatók egy kísérletre, hogy elrejtsék és visszaállítsák a kísérletet az UX-ben, vagy alapértelmezés szerint visszaadott hívás a kísérletek listázásához. Ha egy új kísérlet ugyanazzal a névvel lett létrehozva, mint az archivált kísérlet, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Például: experiment1 = Experiment (munkaterület, "Active Experiment") experiment1. Archive () # hozzon létre új aktív kísérletet ugyanazzal a névvel, mint az archivált. experiment2. = Kísérlet (munkaterület, "aktív kísérlet") experiment1. reactivate (new_name = "előző aktív kísérlet") a kísérlet során a statikus metódusok listája () is megteheti a nevet és a nézet típusa szűrőt. A nézet típusa értéke "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL" Példa: archived_experiments = Experiment. list (munkaterület, view_type = "ARCHIVED_ONLY") all_first_experiments = Experiment. list (munkaterület, név = "első kísérlet", view_type = "ALL")
    + A környezet támogatása a modell üzembe helyezéséhez és a szolgáltatás frissítéséhez
  + **azureml – datadrift**
    + A DataDriftDector osztály show attribútuma nem támogatja többé a (z) "with_details" opcionális argumentumot. A show attribútum csak az adateltolódási együtthatót és a szolgáltatás oszlopainak adateltolódási hozzájárulását mutatja.
    + A "get_output" DataDriftDetector-attribútum változásai:
      + A bemeneti paraméter start_time, a end_time nem kötelező;
      + Ha egy adott nput megadott start_time és/vagy end_time adott meg, akkor a rendszer hibát eredményez, mivel ezek kölcsönösen kizárják egymást 
      + Megadott start_time és/vagy end_time csak az ütemezett futtatások eredményei lesznek visszaadva. 
      + A "daily_latest_only" paraméter elavult.
    + Támogatja az adatkészlet-alapú adateltolódások kimenetének beolvasását.
  + **azureml – magyarázat – modell**
    + Átnevezi a AzureML-magyarázza-Model csomagot a AzureML-értelmezésre, így a régi csomagot a visszamenőleges kompatibilitás érdekében megtarthatja
    + rögzített automl hiba a RAW-magyarázatokkal a besorolási feladatra vonatkozóan a regresszió helyett alapértelmezetten a ExplanationClient-ből való letöltéskor
    + A `ScoringExplainer` támogatásának hozzáadása közvetlenül a `MimicWrapper` használatával hozható létre
  + **azureml – folyamat – mag**
    + Nagyobb teljesítmény a nagy adatcsatornák létrehozásakor
  + **azureml-Train-Core**
    + TensorFlow 2,0-támogatás hozzáadva a TensorFlow kalkulátorhoz
  + **azureml-Train-automl**
    + A szülő futtatása nem fog sikerülni, ha a telepítés iterációja meghiúsult, mivel a folyamat már gondoskodik róla.
    + Helyi Docker-és helyi Conda-támogatás hozzáadva a AutoML-kísérletekhez

## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez

Frissült az [Új munkaterület-portál](http://ml.azure.com) Experiment (kísérlet) lapja, így az adatszakértők több módon is megfigyelheti a kísérleteket. A következő funkciók közül választhat:
+ Kísérletezzen a metaadatokkal, így egyszerűen szűrheti és rendezheti a kísérletek listáját
+ Egyszerűsített és elvégezhető kísérlet részleteinek oldalai, amelyek lehetővé teszik a futtatások megjelenítését és összehasonlítását
+ Új kialakítás a részletek oldalain a képzések megismeréséhez és figyeléséhez

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK a Python v 1.0.65

  + **Új funkciók**
    + Válogatott környezetek lettek hozzáadva. Ezek a környezetek előre konfigurálva vannak könyvtárakkal a gyakori gépi tanulási feladatokhoz, és a gyorsabb végrehajtás érdekében Docker-rendszerképekként előre felépítve és gyorsítótárazva lettek. Alapértelmezés szerint a munkaterület "AzureML" előtagú környezetének listájában jelennek meg.
  
  + **azureml-Train-automl**
    + A ONNX-átalakítás támogatása az ADB és a HDI esetében

+ **Előzetes verziójú funkciók**  
  + **azureml-Train-automl**
    + Támogatott BERT és BiLSTM as text Képtulajdonság (csak előzetes verzió)
    + Az oszlop céljának és a transzformátor paramétereinek támogatott featurization testreszabása (csak előzetes verzió)
    + Támogatott nyers magyarázatok, ha a felhasználó a betanítás során engedélyezi a modell magyarázatát (csak előzetes verzió)
    + Próféta hozzáadva a idősor-előrejelzéshez betanítható folyamatként (csak előzetes verzió)
  
  + **azureml – datadrift**
    + Csomagok áthelyezve a azureml-datadrift-azureml-datadrift; a következő kiadásban a rendszer eltávolítja a összevont csomagot 

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + Bevezette a FeaturizationConfig a AutoMLConfig és a AutoMLBaseSettings
      + A Featurization oszlopának felülbírálása a megadott oszlop és szolgáltatás típusával
      + A transzformátor paramétereinek felülbírálása
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () szolgáltatáshoz
    + Próféta hozzáadva betanítható folyamatként (csak előzetes verzió)
    + A cél késések automatikus észlelésének támogatása, a gördülő ablak mérete és a maximális horizont. Ha a target_lags, a target_rolling_window_size vagy a max_horizon az "Auto" értékre van állítva, a rendszer a heurisztikus értékeket alkalmazza a megfelelő paraméterek értékének becslésére a betanítási adat alapján.
    + Rögzített előrejelzés abban az esetben, ha az adathalmaz egy gabona oszlopot tartalmaz, ez a gabona numerikus típusú, és a vonat és a tesztelési készlet közötti hézag van.
    + Kijavítva a duplikált indextel kapcsolatos hibaüzenet a távoli Futtatás előrejelzési feladatokban
    + Egy Guardrail hozzáadva annak ellenőrzését, hogy az adatkészlet kiegyensúlyozatlan vagy sem. Ha igen, a rendszer egy Guardrail üzenetet ír a konzolra.
  + **azureml – mag**
    + Lehetőség van az SAS URL-címének lekérésére a Storage-ban a Model objektumon keresztül. Pl.: Model. Get _sas_url ()
    + @No__t-0 bevezetése a beküldött futtatáshoz társított adatkészletek lekéréséhez
    + Vegyen fel egy TabularDataset a JSON-sorok fájljaiból a-0 API-@no__t. A TabularDataset JSON-sorok fájljaiban található táblázatos adatokat a dokumentációban https://aka.ms/azureml-data címen találja meg.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) hozzáadása a supported_vmsizes () függvényhez
    + További mezők lettek hozzáadva a list_nodes () függvényhez a Futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítéséhez.
    + Új mező megadásának lehetősége a fürt üzembe helyezése során – remotelogin_port_public_access, amely engedélyezhető vagy letiltható attól függően, hogy a fürt létrehozásakor nyitva vagy le szeretné-e hagyni az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy lezárta a portot attól függően, hogy egy VNet belül telepíti a fürtöt.
  + **azureml – magyarázat – modell**
    + Továbbfejlesztett dokumentáció a besorolási forgatókönyv magyarázatának eredményeiről.
    + A kiértékelési példákban az előre jelzett y értékek feltöltésére is lehetőség van. További hasznos vizualizációk feloldása.
    + A magyarázatot megadó tulajdonság hozzáadva a MimicWrapper-hez, amely lehetővé teszi az alapul szolgáló MimicExplainer beszerzését.
  + **azureml – folyamat – mag**
    + Hozzáadott jegyzetfüzet a modul, a ModuleVersion és a ModuleStep leírásához
  + **azureml – folyamat – lépések**
    + A RScriptStep hozzáadva az R-szkriptek a pénzmosás-folyamaton keresztüli futtatásához
    + A AzureBatchStep olyan rögzített metaadat-paramétereket elemez, amely a következő hibaüzenetet okozta: "a SubscriptionId paraméter nem lett megadva"
  + **azureml-Train-automl**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbeviteli formátumként
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () szolgáltatáshoz

  
## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK a Python v 1.0.62

+ **Új funkciók**
  + Bevezette a idősor tulajdonságot a TabularDataset. Ez a funkció lehetővé teszi, hogy a TabularDataset egyszerű időbélyeg-szűrést biztosítson, például egy adott időtartam vagy a legfrissebb adatmennyiség között. Ha többet szeretne megtudni erről a idősor-tulajdonságról a TabularDataset-on, tekintse meg az https://aka.ms/azureml-data dokumentációt, vagy https://aka.ms/azureml-tsd-notebook példát egy jegyzetfüzetre. 
  + TabularDataset-és FileDataset-alapú betanítás. Egy példa notebookra https://aka.ms/dataset-tutorial címen talál. 
  
  + **azureml-Train-Core**
    + Nccl-és gloo-támogatás hozzáadva a PyTorch-kalkulátorban
  
+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A "lag_length" és a LaggingTransformer AutoML-beállítás elavult.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
    + Módosította a fit_pipeline. a diagramot a Graph JSON létrehozásához és az összetevőkre való feltöltéshez. 
    + A gráfot a userrun alá tették a Cytoscape használatával.
  + **azureml – mag**
    + Tekintse át a kivételt az ADB-kódban, és végezze el a módosítást új hibakezelés esetén
    + Automatikus MSI-hitelesítés hozzáadva a notebook virtuális gépekhez.
    + Kijavítja a hibát, ha a hibás újrapróbálkozások miatt a sérült vagy üres modelleket fel lehet tölteni.
    + Kijavítva a hiba, ahol a `DataReference` név megváltozik, ha az `DataReference` mód megváltozik (például a `as_upload`, `as_download` vagy `as_mount`) meghívásakor.
    + A `FileDataset.mount` és a `FileDataset.download` esetében a `mount_point` és a `target_path` megadása nem kötelező.
    + Kivétel az időbélyegző-oszlop nem található, ha az idősoros kapcsolódó API-t a rendszer az időbélyegző oszlop kiosztása nélkül hívja meg, vagy a hozzárendelt időbélyeg-oszlopok el lesznek dobva.
    + Az idősoros oszlopokat olyan oszlophoz kell rendelni, amelynek a típusa dátum, ellenkező esetben a rendszer kivételt vár.
    + A "with_timestamp_columns" API-t hozzárendelő idő-sorozatszámok egyike sem lehet a (z) nem érték típusú, illetve durva időbélyeg-oszlop neve, ami törli a korábban hozzárendelt időbélyeg-oszlopokat
    + A kivétel akkor jelenik meg, ha a durva vagy a részletes időbélyeg-oszlop el van dobva, és jelzi, hogy az eldobást követően a felhasználó megszakította az időbélyegző oszlopot a ledobások listájában, vagy a nincs értékkel meghívja a with_time_stamp oszlopok
    + Kivétel keletkezik, ha a durva vagy a részletes időbélyeg-oszlop nem szerepel az oszlopok megtartása listán, és jelzi, hogy a felhasználó megtartja a tartást Az időbélyeg-oszlopok kiadásának értéke.
    + Egy regisztrált modell méretének naplózása hozzáadva.
  + **azureml – magyarázat – modell**
    + Rögzített figyelmeztetés kinyomtatva a konzolra, ha a "csomagolás" Python-csomag nincs telepítve: "a lightgbm támogatott verziójának régebbi verziója esetén frissítsen a 2.2.1-nél nagyobb verzióra"
    + Rögzített letöltési modell magyarázata a globális magyarázatok számos funkcióval
    + A rögzített utánozó-magyarázat hiányzó inicializálási példákat tartalmaz a kimeneti magyarázaton
    + Rögzített megváltoztathatatlan hiba történt a tulajdonságok beállításakor, ha két különböző típusú modellt használ a magyarázó ügyféllel való feltöltéshez
    + Felvettünk egy get_raw paramétert a magyarázathoz. magyarázza el, hogy az egyik pontozási elválasztó a mesterséges és a nyers értékeket is visszaadja.
  + **azureml-Train-automl**
    + Nyilvános API-k bevezetése a AutoML-ből a AutoML magyarázatának támogatásához – újabb módszer a AutoML-magyarázatok támogatásához a AutoML-featurization leválasztásával és az SDK-val integrált, a azureml magyarázata és a AutoML modellek.
    + Azureml eltávolítása – Alapértelmezések a távoli képzési környezetekben.
    + A FileCacheStore alapján módosította az alapértelmezett gyorsítótár-tároló helyét a AzureDatabricks-AzureFileCacheStore egy AutoML-re.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
  + **azureml-Train-Core**
    + Visszaállított source_directory_data_store-érvénytelenítés.
    + A azureml telepített csomag verzióinak felülbírálásának lehetősége. 
    + Docker-támogatás hozzáadva `environment_definition` paraméterhez a becslések-ben.
    + Egyszerűsített elosztott tanítási paraméterek a becslések-ben.
         ```py 
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer 
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez 
Az új webes felület lehetővé teszi az adatszakértők és adatmérnökök számára, hogy teljes körű gépi tanulási életciklust prepping, és megjelenítsék az adatok megjelenítését a modellek betanításához és üzembe helyezéséhez egyetlen helyen. 

![Azure Machine Learning munkaterület felhasználói felülete (előzetes verzió)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Főbb funkciók:**

Az új Azure Machine Learning felület használatával mostantól a következőket teheti:
+ Jegyzetfüzetek kezelése vagy Jupyter
+ [Automatizált ML-kísérletek futtatása](tutorial-first-experiment-automated-ml.md)
+ [Adatkészletek létrehozása helyi fájlokból, adattárokból, & webfájlokból](how-to-create-register-datasets.md)
+ Ismerkedjen meg & adatkészletek előkészítésével a modell létrehozásához
+ A modellek adateltolódásának monitorozása 
+ Legutóbbi erőforrások megtekintése az irányítópultról

A jelen kiadás időpontjában a következő böngészők támogatottak: Chrome, Firefox, Safari és Microsoft Edge preview.

**Ismert problémák:**

1. Frissítse a böngészőt, ha "hiba történt". Hiba történt az adathalmaz fájljainak betöltésekor, ha a központi telepítés folyamatban van.  

1. Nem lehet törölni vagy átnevezni a fájlt jegyzetfüzetekben és fájlokban. A nyilvános előzetes verzió során a Jupyter felhasználói felületét vagy a terminált használhatja a notebook virtuális gépen a frissítési fájl műveleteinek elvégzéséhez. Mivel ez egy csatlakoztatott hálózati fájlrendszer, a notebook virtuális gépen végzett módosítások azonnal megjelennek a notebook munkaterületen. 

1. SSH-t a notebook VM-be:
   1. Keresse meg a virtuális gép beállítása során létrehozott SSH-kulcsokat. Vagy keresse meg a kulcsokat az Azure ML Azure Portal > nyissa meg a számítási lapot, > keresse meg a jegyzetfüzet virtuális gépet a listában > nyissa meg a tulajdonságokat: másolja a kulcsokat a párbeszédpanelből.
   1. Importálja ezeket a nyilvános és titkos SSH-kulcsokat a helyi gépre.
   1. Az SSH-t a notebook VM-be használhatja. 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK a Python v 1.0.60

+ **Új funkciók**
  + Bevezetett FileDataset, amely egy vagy több fájlra hivatkozik az adattárban vagy a nyilvános URL-címeken. A fájlok formátuma bármilyen lehet. A FileDataset lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A FileDataset-ről a https://aka.ms/file-dataset címen tájékozódhat.
  + Hozzáadott folyamat YAML-támogatás a PythonScript lépéshez, ADla lépés, Databricks lépés, DataTransferStep és AzureBatch lépés

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A AutoArima mostantól az előzetes verzióra vonatkozó, javasolt folyamat.
    + Továbbfejlesztett hibajelentés az előrejelzéshez.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
    + A max_concurrent_iterations-ellenőrzések kevesebb, mint az ismétlések teljes száma.
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
  + **azureml – mag**
    + Adatkészlet bevezetése. beolvassa a _All (munkaterület), amely `TabularDataset` és `FileDataset` objektum szótárát adja vissza a regisztrációs nevük alapján. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + @No__t – 0 argumentumot vezessen be `Dataset.Tabular.from_delimited_files` és `Dataset.Tabular.from_parquet.files` értékre. Az egyes adatútvonalak partíciós adatait a rendszer a megadott formátum alapján oszlopokra bontja ki. a (z) {column_name} karakterlánc-oszlopot hoz létre, és a (z) {column_name: ÉÉÉÉ/HH/NN/HH/PP/SS} létrehoz datetime oszlopot, ahol az "éééé", a "hh", a "HH", a "PP" és az "SS A partition_format az első partíciós kulcs pozíciója alapján kell kezdődnie a fájl elérési útjának végéig. Például a következő elérési út miatt: ".. /USA/2019/01/01/data.csv, ahol a partíció ország és idő szerint van, a partition_format = '/{Country}/{PartitionDate: éééé/hh/nn}/Value. csv ' karakterláncot hoz létre az "ország" értékkel az "USA" értékkel és a "PartitionDate" értékkel "2019-01-01" értékű datetime oszloppal.
    + `to_csv_files` és `to_parquet_files` metódus lett hozzáadva a következőhöz: `TabularDataset`. Ezek a módszerek lehetővé teszik a `TabularDataset` és a `FileDataset` közötti átalakítást az adatfájlok megadott formátumban való átalakításával.
    + Automatikusan bejelentkezik az alaprendszerkép beállításjegyzékbe a Model. package () által generált Docker mentésekor.
    + a "gpu_support" már nem szükséges; A AzureML mostantól automatikusan észleli és használja az NVIDIA Docker-bővítményt, ha elérhető. Egy későbbi kiadásban el lesz távolítva.
    + Támogatás hozzáadva a PipelineDrafts létrehozásához, frissítéséhez és használatához.
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
    + A felhasználók a futtatási előzmények alapján tudják lekérdezni a metrikákat.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
  + **azureml – magyarázat – modell**
    + A feature_maps paraméter hozzáadva az új MimicWrapper, amely lehetővé teszi a felhasználók számára a nyers funkciók magyarázatának beolvasását.
    + Az adatkészlet-feltöltések mostantól alapértelmezés szerint ki vannak kapcsolva a magyarázat feltöltéséhez, és újra engedélyezhetők a upload_datasets = true értékkel.
    + A "is_law" paraméter hozzáadva a magyarázatok listájához és a letöltési függvényekhez.
    + A `get_raw_explanation(feature_maps)` metódust adja hozzá a globális és a helyi magyarázó objektumokhoz is.
    + Hozzáadott verzió-ellenőrzési lightgbm a nyomtatott figyelmeztetéssel, ha az alábbi támogatott verzió
    + Optimalizált memória használata
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
  + **azureml – folyamat – mag**
    + A PipelineDrafts létrehozásához, frissítéséhez és használatához hozzáadva támogatott a megváltoztathatatlan folyamat-definíciók fenntartása és interaktív módon történő futtatása
  + **azureml-Train-automl**
    + A szolgáltatás a GPU-kompatibilis pytorch v 1.1.0, a CUDA Toolkit 9,0, a pytorch-Transformers adott verzióinak telepítéséhez szükséges, amely a BERT/XLNet távoli Python futásidejű környezetében való engedélyezéséhez szükséges.
  + **azureml-Train-Core**
    + Néhány hiperparaméter-hely definíciós hibájának korai meghibásodása közvetlenül az SDK-ban a kiszolgálóoldali helyett.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.14
+ **Hibajavítások és javítások**
  + A ADLS/ADLSGen2 írásának engedélyezése a nyers elérési út és a hitelesítő adatok használatával.
  + Kijavított egy hibát, amely a `include_path=True` értéket okozta, hogy a `read_parquet` esetében nem működik.
  + Rögzített @no__t – 0 hiba történt a következő kivétel miatt: "Érvénytelen tulajdonságérték: hostSecret".
  + Kijavítva egy hiba, ahol a DBFS Spark módban nem olvashatók a fájlok.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK a Python v 1.0.57
+ **Új funkciók**
  + A AutomatedML által felhasználható @no__t – 0. Ha többet szeretne megtudni a `TabularDataset`-ról, látogasson el a https://aka.ms/azureml/howto/createdatasets webhelyre.
  
+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkék (y és y_valid) a pandák dataframe, de NumPy tömb formájában vannak megadva.
    + Frissített illesztőfelület `RawDataContext` létrehozásához, hogy csak az adatértékre és a `AutoMLBaseSettings` objektumra legyen szükség.
    +  Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **Azure-CLI-ml**
    + Most már frissítheti a Microsoft által generált és az ügyfél-tanúsítványhoz az AK-fürtökön üzembe helyezett pontozási végpont SSL-tanúsítványát is.
  + **azureml-automl-Core**
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Javított hibák naplózása a AutoML-ben; a rendszer mindig a naplófájlba írja a teljes hibaüzeneteket.
    + A AutoML frissítette a csomagjának rögzítését, hogy tartalmazza a `azureml-defaults`, `azureml-explain-model` és `azureml-dataprep` értéket. A AutoML nem fog figyelmeztetni a csomagok eltérésére (kivéve `azureml-train-automl` csomag esetében).
    + Javítva lett egy probléma a idősor, ahol az önéletrajz-felosztás nem egyenlő, mert a Raktárhelyek kiszámításának sikertelensége sikertelen.
    + Ha a többellenőrzéses képzéshez a teljes adatkészletre betanított modellek letöltése során nem sikerül a Ensemble-iteráció futtatása, a modell súlyozása és a szavazásban bekövetkező modellek között inkonzisztens volt. együttes.
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkék (y és y_valid) a pandák dataframe, de NumPy tömb formájában vannak megadva.
    + Kijavítva az előrejelzési feladatokkal kapcsolatos problémát, ha a bemeneti táblák logikai oszlopaiban sem történt a hiba.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **azureml – mag**
    + Kijavítva a blob_cache_timeout paraméter megrendelésével kapcsolatos probléma.
    + A külső illesztési és átalakítási kivételek rendszerhibákhoz lettek adva.
    + Key Vault-titkok támogatása távoli futtatásokhoz. Vegyen fel egy azureml. Core. kulcstartó. kulcstartó osztályt a munkaterülethez tartozó kulcstartó hozzáadásához, beolvasásához és listázásához. A támogatott műveletek a következők:
      + azureml. Core. Workspace. Workspace. Get _default_keyvault ()
      + azureml. Core. kulcstartó. kulcstartó. set _secret (név, érték)
      + azureml. Core. kulcstartó. kulcstartó. set _secrets (secrets_dict)
      + azureml. Core. kulcstartó. kulcstartó. Get _secret (név)
      + azureml. Core. kulcstartó. kulcstartó. Get _secrets (secrets_list)
      + azureml. Core. kulcstartó. kulcstartó. list_secrets ()
    + További módszerek az alapértelmezett kulcstartó beszerzéséhez és a titkos kulcsok lekéréséhez a távoli futtatás során:
      + azureml. Core. Workspace. Workspace. Get _default_keyvault ()
      + azureml. Core. Run. Run. Get _secret (név)
      + azureml. Core. Run. Run. Get _secrets (secrets_list)
    + További felülbírálási paraméterek lettek hozzáadva a Submit-HyperDrive CLI-parancshoz.
    + Az API-hívások megbízhatóságának növelése a gyakori kérések könyvtárbeli kivételekre való ismételt próbálkozások kiterjesztése.
    + A futtatások küldésének támogatása egy elküldött futtatásból.
    + A FileWatcher-ben rögzített lejáró SAS-token probléma, amely miatt a fájlok a kezdeti jogkivonat lejárta után le lettek töltve.
    + Támogatott a HTTP CSV/TSV fájlok importálása a DataSet Python SDK-ban.
    + A munkaterület. Setup () metódus elavult. A felhasználók számára megjelenő figyelmeztető üzenet a Create () vagy a Get ()/from_config () függvényt javasolja.
    + Hozzáadott környezet. adja hozzá a _private_pip_wheel ()-t, amely lehetővé teszi privát egyéni Python-csomagok (. WHL) feltöltését a munkaterületre, és biztonságosan használja őket a környezet összeállításához és megvalósulása érdekében.
    + Most már frissítheti a Microsoft által generált és az ügyfél-tanúsítványhoz az AK-fürtökön üzembe helyezett pontozási végpont SSL-tanúsítványát is.
  + **azureml – magyarázat – modell**
    + Paraméter hozzáadva a modell AZONOSÍTÓjának a feltöltéshez való hozzáadásához.
    + A rendszer hozzáadta @no__t – 0 címkézést a memóriában található magyarázatokhoz és a feltöltéshez.
    + Pytorch-támogatás és-tesztek hozzáadva a azureml-magyarázza-Model csomaghoz.
  + **azureml – opendatasets**
    + Az automatikus tesztelési környezet észlelése és naplózása.
    + Osztályok hozzáadásával az Egyesült államokbeli lakosságot a megye és a zip alapján szerezheti be.
  + **azureml – folyamat – mag**
    + A Label tulajdonság hozzáadva a bemeneti és a kimeneti port definícióhoz.
  + **azureml – telemetria**
    + Helytelen telemetria-konfiguráció lett kijavítva.
  + **azureml-Train-automl**
    + Kijavítva a hiba, ahol a telepítési hiba miatt nem sikerült bejelentkezni a "hibák" mezőbe a telepítési futtatáshoz, ezért a rendszer nem a szülő "errors" (hibák) alatt tárolja a hibát.
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják azokat a tesztelési készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
    + A AutoMLStep mostantól áthalad a automl config-n keresztül, hogy elkerülje az új konfigurációs paraméterek módosításainak vagy kiegészítéseinek esetleges problémáit.
    + A AutoML-adatGuardrail már nyilvános előzetes verzióban érhető el. A felhasználó az Guardrail jelentést (besorolási/regressziós feladatok esetén) fogja látni a képzés után, és az SDK API-n keresztül is elérhetővé teheti.
  + **azureml-Train-Core**
    + A Torch 1,2-támogatás hozzáadva a PyTorch kalkulátorhoz.
  + **azureml – widgetek**
    + Továbbfejlesztett összetéveszthető mátrix-diagramok a besorolási képzéshez.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.12
+ **Új funkciók**
  + A sztringek listája mostantól a `read_*` metódusok bemenetként adható át.

+ **Hibajavítások és javítások**
  + A Sparkban való futás során a `read_parquet` teljesítménye jelentősen javult.
  + Kijavítva a hiba, ahol a `column_type_builder` nem sikerült, ha egyetlen oszlop nem egyértelmű dátumformátum.

### <a name="azure-portal"></a>Azure Portal
+ **Előzetes verzió funkció**
  + A log és a output file streaming mostantól elérhető a futtatási részletek oldalain. A fájlok valós időben továbbítják a frissítéseket, amikor bekapcsolják az előnézeti kapcsolót.
  + A kvóta megadásának lehetősége a munkaterület szintjén előzetes verzióban érhető el. A AmlCompute-kvóták az előfizetés szintjén vannak lefoglalva, de mostantól lehetővé tesszük, hogy az adott kvótát a munkaterületek között, és a méltányos megosztáshoz és irányításhoz ossza ki. A munkaterület bal oldali navigációs sávján kattintson a **használat + kvóták** panelre, és válassza a **kvóták konfigurálása** fület. vegye figyelembe, hogy a kvóták a munkaterület szintjén való beállítása, mivel ez egy több munkaterület művelet.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK a Python v 1.0.55

+ **Új funkciók**
  + A jogkivonat-alapú hitelesítés mostantól támogatott az AK-on üzembe helyezett pontozási végponton kezdeményezett hívásokban. Továbbra is támogatni fogjuk az aktuális kulcs alapú hitelesítést, és a felhasználók egyszerre használhatják ezeket a hitelesítési mechanizmusokat.
  + Lehetőség a virtuális hálózat (VNet) mögött lévő blob Storage adattárként való regisztrálására.
  
+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + Kijavít egy hibát, ahol a CV-felosztások érvényesítési mérete kicsi, és a regressziós és az előrejelzési eredményhez rossz előre jelzett és igaz diagramokat eredményez.
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + A idősor rögzített hibái, ha az előfeldolgozás jelzője igaz.
    + Az előrejelzési adatellenőrzéssel kapcsolatos egyes hibaüzenetek további műveleteket végeznek.
    + Az adatkészletek ledobásával és/vagy lusta betöltésével a AutoML kevesebb memóriát használ, különösen a folyamatok között
  + **azureml – feltörzs – magyarázat – modell**
    + A model_task jelző hozzáadása a magyarázatokhoz, amely lehetővé teszi, hogy a felhasználó felülbírálja az alapértelmezett automatikus következtetési logikát a modell típusaként
    + Widget-változások: automatikusan települ a (z) és a (z), a (z) és a (z) nbextension telepítése/engedélyezése – támogatás magyarázata csupán globális fontosságú (például Permutative
    + Irányítópult-változások: Box-mintaterületek és hegedű-diagramok az összefoglalás oldal beeswarm mellett – sokkal gyorsabb a beeswarm-ábrázolás a "Top-k" csúszka módosítása – hasznos üzenet, amely leírja, hogy a Top-k hogyan számítja ki a diagramokat – hasznos testreszabható üzenetek a diagramok helyett, ha nincs megadva információ
  + **azureml – mag**
    + A Model. package () metódussal olyan Docker-lemezképeket és Dockerfiles hozhat létre, amelyek a modelleket és a függőségeiket beágyazzák.
    + Frissített helyi webszolgáltatások a környezeti objektumokat tartalmazó InferenceConfigs fogadásához.
    + Fixed Model. Register () érvénytelen modelleket állít elő, ha "." (az aktuális könyvtár esetében) a model_path paraméterként lesz átadva.
    + Adja hozzá a Run. submit_child, a funkció-tükrözések kísérlet. Submit parancsot, miközben megadja az elküldött gyermek futtatásának szülőjét.
    + A Model. register konfigurációs beállításainak támogatása a Run. register_model.
    + JAR-feladatok futtatása meglévő fürtön.
    + Most már támogatja a instance_pool_id és a cluster_log_dbfs_path paramétereket.
    + Egy környezeti objektum használatának támogatása a modell webszolgáltatásba történő telepítésekor. A környezeti objektum most már megadható a InferenceConfig objektum részeként.
    + Új régiókhoz tartozó appinsifht-leképezés hozzáadása – CentralUS-westus-northcentralus
    + Dokumentáció hozzáadva az összes adattár osztály összes attribútumához.
    + A blob_cache_timeout paraméter hozzáadva a következőhöz: `Datastore.register_azure_blob_container`.
    + Save_to_directory és load_from_directory metódusok lettek hozzáadva a azureml. Core. environment. Environmenthoz.
    + Hozzáadta az "az ml-környezet letöltése" és az "az ml-környezet regisztrálása" parancsokat a CLI-hez.
    + Hozzáadott környezet. adja hozzá a _private_pip_wheel metódust.
  + **azureml – magyarázat – modell**
    + Az adatkészlet nyomon követése az adatkészlet szolgáltatás (előzetes verzió) használatával magyarázatokkal bővült.
    + Csökkent az alapértelmezett köteg mérete, amikor a globális magyarázatot a 10k-ról 100-re.
    + A model_task jelzőt adtak hozzá a magyarázatokhoz, hogy a felhasználó felülbírálja a modell típusának alapértelmezett automatikus következtetési logikáját.
  + **azureml – mlflow**
    + Kijavítva a hiba a mlflow. azureml. build_image helyen, ahol a rendszer figyelmen kívül hagyja a beágyazott könyvtárakat.
  + **azureml – folyamat – lépések**
    + A JAR-feladatok meglévő Azure Databricks-fürtön való futtatásának lehetősége hozzáadva.
    + A instance_pool_id és a cluster_log_dbfs_path paraméterek hozzáadása a DatabricksStep lépéshez.
    + A DatabricksStep lépésben hozzáadta a folyamat paramétereinek támogatását.
  + **azureml-Train-automl**
    + Hozzáadott docstrings az Ensemble-hez kapcsolódó fájlokhoz.
    + A dokumentumok frissített dokumentációja a `max_cores_per_iteration` és a `max_concurrent_iterations` megfelelő nyelvéhez
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + A get_data eltávolítva a folyamat automlstep jegyzetfüzetből.
    + Megkezdődött a adatelőkészítés támogatása a automlstep-ben.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.10

+ **Új funkciók**
  + Most már kérheti az adott oszlopokra vonatkozó bizonyos ellenőrök (például hisztogram, pontdiagram stb.) futtatását is.
  + Egy integrálással argumentum hozzáadva a `append_columns` értékhez. Ha az értéke igaz, a rendszer a memóriába tölti be az adatmennyiséget, a végrehajtás pedig párhuzamosan fut. Ha a False (hamis), a végrehajtás streaming, de egyszálas lesz.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK a Python v 1.0.53

+ **Új funkciók**
  + Az automatizált Machine Learning mostantól támogatja a távoli számítási cél ONNX modelljeit
  + A Azure Machine Learning mostantól lehetővé teszi az előző futtatási, ellenőrzőpont-vagy modell-fájlok betanításának folytatását.
    + Megtudhatja, hogyan [használhatja a becslések-t egy korábbi futtatásból származó képzés folytatásához](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva); 
    + Annak engedélyezése, hogy a y_query olyan objektumtípus legyen, amely a none (s) értéket tartalmazza a BEGIN (#459519) értékben.
  + **Azure-CLI-ml**
    + A CLI-parancsok "modell telepítése" és "szolgáltatás frissítése" mostantól elfogadják a paramétereket, a konfigurációs fájlokat vagy a kettő kombinációját. A paraméterek elsőbbséget élveznek a fájlok attribútumaival szemben.
    + A modell leírása mostantól a regisztráció után is frissíthető
  + **azureml-automl-Core**
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + A AutoML becslések-ben használandó Nimbus ML becslések &-folyamatok támogatásának hozzáadása.
    + Az Ensemble kiválasztási eljárásában felmerült hiba, amely szükségtelenül bővült az eredményül kapott együttesnél, még akkor is, ha a pontszámok változatlanok maradnak.
    + Az előrejelzési feladatok elvégzése érdekében az egyes featurizations ismételt használatának engedélyezése. Ez felgyorsítja a telepítő futtatási idejét a költséges featurizations, például a késések és a n_cross_validations terén.
    + Probléma megoldása, ha az idő a Panda által támogatott időtartományon kívül esik. Most DataException, ha az idő kevesebb, mint PD. Timestamp. min vagy nagyobb, mint a PD. Timestamp. max
    + Az előrejelzés mostantól lehetővé teszi, hogy a vonatok és a tesztek különböző gyakorisággal legyenek igazítva. Például a "negyedéves kezdés januárban" és a "negyedévente kezdődően október" értékkel igazítható.
    + A "parameters" tulajdonság hozzá lett adva a TimeSeriesTransformer.
    + Távolítsa el a régi kivételi osztályokat.
    + Az előrejelzési feladatokban a `target_lags` paraméter mostantól egyetlen egész értéket, vagy egész számok listáját fogadja el. Ha az egész számot adta meg, csak egy késés lesz létrehozva. Ha egy lista van megadva, a rendszer a lemaradás egyedi értékeit veszi figyelembe. a target_lags = [1, 2, 2, 4] egy, 2 és 4 időszakból álló késéseket fog létrehozni.
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva);
    + @No__t – 0 esetében engedélyezze, hogy a y_query a none (s) értéket tartalmazó objektumtípus legyen a BEGIN (#459519).
    + várt értékek hozzáadása a automl kimenetéhez
  + **azureml – datadrift**
    +  Többek között a jegyzetfüzetek fejlesztése, beleértve a azureml-opendatasets való váltást, a azureml-opendatasets és a teljesítmény javítását az adatgazdagítás során
  + **azureml – feltörzs – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + szegmensek hozzáadása a képmagyarázatban a AzureML-feladatokhoz – a modell-előkészítési csomag
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + adja hozzá a batch_size, hogy utánozza a magyarázatot, ha a include_local = FALSE értéket közvetíti a kötegekben a DecisionTreeExplainableModel végrehajtási idejének javítása érdekében.
  + **azureml – featureengineering**
    + Javítás a set_featurizer_timeseries_params meghívásához (): a dict értékének módosítása és null beadása – jegyzetfüzet hozzáadása a idősor-Képtulajdonság
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
  + **azureml – mag**
    + A DBFS-adattárolók csatlakoztatásának lehetősége a AzureML CLI-ben 
    + Kijavítva a hiba az adattár feltöltésekor, ha a `target_path` megkezdődött, `/`
    + Rögzített deepcopy probléma a ServicePrincipalAuthentication-ben.
    + Hozzáadta az "az ml-környezet megjelenítése" és az "az ml környezet listázása" parancsot a parancssori felülethez.
    + A környezetek mostantól támogatják a base_dockerfile megadását egy már létrehozott base_image alternatívájaként.
    + A nem használt RunConfiguration-beállítás auto_prepare_environment lett megjelölve elavultként.
    + A modell leírása mostantól a regisztráció után is frissíthető
    + Hibajavítás: a modell-és rendszerkép-törlés mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + Kijavítva a hiba, amely üres időtartamot nyomtat a környezetekhez tartozó munkaterületek létrehozásakor előforduló központi telepítések esetén.
    + A továbbfejlesztett munkaterület a hibák kivételeit hozza létre. A felhasználók nem látják a munkaterület létrehozását. Nem található... " az üzenet helyett a tényleges létrehozási hiba jelenik meg.
    + Adja hozzá a jogkivonat-hitelesítés támogatását az AK webszolgáltatásokban. 
    + @No__t-0 metódus hozzáadása `Webservice` objektumhoz.
    + A CLI-támogatás hozzáadva a Machine learning-adatkészletek kezeléséhez.
    + `Datastore.register_azure_blob_container` most igény szerint egy `blob_cache_timeout` értéket (másodpercben), amely a blobfuse csatlakoztatási paramétereit konfigurálja a gyorsítótár lejáratának engedélyezéséhez ehhez az adattárhoz. Az alapértelmezett érték nem időtúllépés, azaz a Blobok beolvasása esetén a helyi gyorsítótárban marad, amíg a művelet be nem fejeződik. A legtöbb feladat inkább ezt a beállítást fogja előnyben részesíteni, de egyes feladatokhoz több adatra van szükség egy nagy adatkészletből, mint amennyi a csomópontjaihoz fér. Ezekhez a feladatokhoz a paraméter finomhangolása segít a sikeres műveletben. Ügyeljen a paraméter finomhangolására: a túl alacsony érték beállítása gyenge teljesítményt eredményezhet, mivel a korszakban használt adat lejár, mielőtt ismét használni lehetne. Ez azt jelenti, hogy az összes olvasás a blob Storage-ból (azaz a hálózatból) történik, nem pedig a helyi gyorsítótárból, amely negatív hatással van a betanítási időpontokra.
    + A modell leírása mostantól a regisztráció után is megfelelően frissíthető
    + A modell-és képtörlés mostantól további információkat tartalmaz a rajtuk függő felsőbb rétegbeli objektumokról, ami miatt a törlés meghiúsul
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
  + **azureml – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + hozzáadott alakzat lineáris bemutató burkoló, valamint egy másik szint a táblázatos magyarázathoz a lineáris modellek elmagyarázása érdekében
    + az elmagyarázza a modell függvénytárában, a include_local = false for ritka adatok bevitele esetén rögzített hibát.
    + Várt értékek hozzáadása a automl kimenetéhez
    + a rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva
    + adja hozzá a batch_size, hogy utánozza a magyarázatot, ha a include_local = FALSE értéket közvetíti a kötegekben a DecisionTreeExplainableModel végrehajtási idejének javítása érdekében.
    + a modell-magyarázó könyvtár, a rögzített tábla-magyarázatok, ahol a Panda dataframe input szükséges az előrejelzéshez
    + Kijavítva a hiba, ahol a `explanation.expected_values` időnként lebegőpontos értéket ad vissza, nem pedig egy lebegőpontos listát.
  + **azureml – mlflow**
    + Javítsa a mlflow teljesítményét. Állítsa be a _experiment (experiment_name)
    + A InteractiveLoginAuthentication mlflow-tracking_uri való használatának hibája
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
    + Javítsa a azureml-mlflow csomag dokumentációját
    + Javítási hiba, ahol a mlflow. log _artifacts ("my_dir") az összetevők mentését a "my_dir/<-összetevő – elérési utak >" a "<-összetevő – útvonalak >" helyett
  + **azureml – opendatasets**
    + Rögzítse a opendatasets a régi verzióra (< 0.14.0), mert a pyarrow az újonnan bevezetett memória-probléma miatt.
    +  Helyezze át a azureml-opendatasets-azureml-opendatasets. – Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek a pénzmosás-munkaterületen, és zökkenőmentesen használják a pénzmosás-adatkészlet képességeit – A NoaaIsdWeather-bővítési teljesítmény javítása a nem SPARK verzióban jelentős mértékben.
  + **azureml – folyamat – lépések**
    + A DBFS adattár mostantól támogatja a DatabricksStep bemeneteit és kimeneteit.
    + Frissített dokumentáció a Azure Batch lépéshez a bemenetek/kimenetek tekintetében.
    + A AzureBatchStep-ben a *delete_batch_job_after_finish* alapértelmezett értéke *true (igaz*) értékre módosult.
  + **azureml – telemetria**
    +  Helyezze át a azureml-opendatasets-azureml-opendatasets. – Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek a pénzmosás-munkaterületen, és zökkenőmentesen használják a pénzmosás-adatkészlet képességeit – A NoaaIsdWeather-bővítési teljesítmény javítása a nem SPARK verzióban jelentős mértékben.
  + **azureml-Train-automl**
    + Frissített dokumentáció a get_output, hogy tükrözze a tényleges visszatérési típust, és adjon meg további megjegyzéseket a kulcs tulajdonságainak lekéréséhez.
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + Várt értékek hozzáadása a automl kimenetéhez
  + **azureml-Train-Core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz
    + A nem használt RunConfiguration-beállítás auto_prepare_environment lett megjelölve elavultként.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.9

+ **Új funkciók**
  + A fájlok közvetlenül egy http-vagy HTTPS-URL-címről való olvasásának támogatása.

+ **Hibajavítások és javítások**
  + Továbbfejlesztett hibaüzenet, amikor egy távoli forrásból (amely jelenleg nem támogatott) származó parketta-adatkészletet próbál beolvasni.
  + Hiba javítva a parketta-fájlformátumba való íráskor a ADLS Gen 2 fájlban, és a ADLS Gen 2 tároló nevének frissítése az elérési úton.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuális felület
+ **Előzetes verziójú funkciók**
  + Az "R-szkript végrehajtása" modul hozzáadva a Visual Interface-ben.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK a Python v 1.0.48

+ **Új funkciók**
  + **azureml – opendatasets**
    + **azureml – a opendatasets** már elérhető **azureml-opendatasets**néven. A régi csomag továbbra is működhet, de javasoljuk, hogy a **azureml-opendatasets** a szélesebb körű képességek és tökéletesítések érdekében használja.
    + Ez az új csomag lehetővé teszi, hogy a nyitott adatkészleteket adatkészletként regisztrálja a pénzmosás-munkaterületen, és kihasználja az adatkészlet által kínált funkciók bármelyikét.
    + Emellett olyan meglévő képességeket is tartalmaz, mint például az Open-adatkészletek Panda/SPARK dataframes való használata, valamint egyes adathalmazok, például időjárási viszonyok elérésének helye.

+ **Előzetes verziójú funkciók**
    + A HyperDriveConfig mostantól elfogadhatja a folyamat objektumát paraméterként a hiperparaméter hangolásának támogatásához egy folyamat használatával.

+ **Hibajavítások és javítások**
  + **azureml-Train-automl**
    + Kijavítva a hiba az oszlopok típusának elvesztése után az átalakítás után.
    + Javítva a hibát, hogy a y_query olyan objektumtípus legyen, amely a none (ok) t tartalmazza az elején. 
    + Kijavította a problémát a Ensemble kiválasztási eljárásában, amely szükségtelenül megnőtte az eredményül kapott Ensemble-t, még akkor is, ha a pontszámok állandó
    + Kijavítva a whitelist_models és a blacklist_models beállításokkal kapcsolatos problémát a AutoMLStep-ben.
    + Kijavítottuk a problémát, amely megakadályozta az előfeldolgozás használatát, ha az AutoML az Azure ML-folyamatok kontextusában használták volna.
  + **azureml – opendatasets**
    + Áthelyezett azureml-opendatasets-azureml-opendatasets.
    + Engedélyezett nyitott adatkészlet-osztályok regisztrálása a pénzmosás-munkaterületre, és zökkenőmentesen hasznosíthatja a pénzmosás-adatkészletek képességeit.
    + A NoaaIsdWeather továbbfejlesztett teljesítményének növelése a nem SPARK verzióban jelentős mértékben.
  + **azureml – magyarázat – modell**
    + Frissített online dokumentáció az értelmező objektumokhoz.
    + A batch_size hozzáadva a magyarázatot, ha a include_local = FALSE értéket közvetíti a kötegekben a DecisionTreeExplainableModel végrehajtási idejének javítása érdekében.
    + Kijavítottuk a problémát, amelyben a `explanation.expected_values` időnként lebegőpontos értéket ad vissza, nem pedig az azt tartalmazó listát.
    + A rendszer a várt értékeket adta hozzá a automl kimenetéhez.
    + A rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva.
    + A batch_size hozzáadva a magyarázatot, ha a include_local = FALSE értéket közvetíti a kötegekben a globális magyarázatok beküldéséhez, hogy a DecisionTreeExplainableModel-modell végrehajtási idejét javítsa.
  + **azureml – mag**
    + Hozzá lett adva a DBFS-adattárolók csatolása az AzureML CLI-ben.
    + Az adattár feltöltésével kapcsolatos probléma kijavítva, ha a `target_path` `/` indításával létrejön egy üres mappa.
    + Két adatkészlet engedélyezett összehasonlítása.
    + A modell és a rendszerkép törlése mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
  + **azureml – mlflow**
    + A azureml. mlflow-t használó távoli futtatások továbbfejlesztett Erőforrás-kihasználtsága.
    + Javítottuk a azureml-mlflow csomag dokumentációját.
    + Kijavítva a probléma, amelyben a mlflow. log _artifacts ("my_dir") a "my_dir/lelet-elérési utak" alatt az "összetevő-elérési utak" helyett az összetevők mentését tenné.
  + **azureml – folyamat – mag**
    + Az összes folyamat lépésének hash_paths elavult, és a jövőben el lesz távolítva. A forráskönyvtár alapértelmezés szerint kivonattal van elkészítve (kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat)
    + A modul-és ModuleStep folyamatos fejlesztése a számítási típusokra vonatkozó specifikus modulok támogatásához, felkészülés a RunConfiguration-integrációra és további változtatásokra a használatuk feloldásához a folyamatokban.
  + **azureml – folyamat – lépések**
    + AzureBatchStep: továbbfejlesztett dokumentáció a bemenetek/kimenetek tekintetében.
    + AzureBatchStep: a delete_batch_job_after_finish alapértelmezett értéke TRUE (igaz) értékre módosult.
  + **azureml-Train-Core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
    + Elavult paraméterek `conda_dependencies_file_path` és `pip_requirements_file_path` a `conda_dependencies_file` és a `pip_requirements_file` mellett.
  + **azureml – opendatasets**
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.8

+ **Új funkciók**
 + A adatfolyam objektumok mostantól megadhatók, és rekordokat hozhatnak létre. Lásd: `Dataflow.to_record_iterator` dokumentációja.

+ **Hibajavítások és javítások**
 + Növelte a Adatelőkészítés SDK megbízhatóságát.
 + A pandák DataFrames jobb kezelését a nem karakterlánc típusú oszlop indexekkel.
 + Az adatkészletek `to_pandas_dataframe` teljesítményének növelése.
 + Kijavítva egy olyan hibát, amelyben az adathalmazok Spark-végrehajtása nem sikerült, ha több csomópontos környezetben futnak.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.7

Javítottunk egy olyan változást, amely javítja a teljesítményt, mivel a Azure Databrickst használó ügyfeleknél problémát okozott. Ha Azure Databricks problémába ütközött, az alábbi módszerek egyikével frissíthet a verzió 1.1.7:
1. Futtassa ezt a parancsfájlt a frissítéshez: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Hozza létre újra a fürtöt, amely telepíti a legújabb adat-előkészítési SDK-verziót.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK a Python v 1.0.45

+ **Új funkciók**
  + Döntési fa helyettesítő modell hozzáadása az azureml-magyarázza-Model csomagban található magyarázatok megjelenítéséhez
  + Lehetőség arra, hogy megadhatja a rendszerképekre telepítendő CUDA-verziót. A CUDA 9,0, 9,1 és 10,0 támogatása.
  + Az Azure ML betanítási alaplemezképekkel kapcsolatos információk mostantól elérhetők az [Azure ml containers GitHub-tárházban](https://github.com/Azure/AzureML-Containers) és a [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + CLI-támogatás hozzáadva a folyamat-ütemtervhez. További információért futtassa az "az ml pipeline-h" parancsot.
  + Egyéni Kubernetes névtér-paraméter lett hozzáadva az AK webszolgáltatások üzembe helyezési konfigurációjához és a CLI-hez.
  + Elavult hash_paths paraméter az összes folyamat lépéseihez
  + A Model. register mostantól támogatja a több egyedi fájl regisztrálását egyetlen modellként a `child_paths` paraméter használatával.
  
+ **Előzetes verziójú funkciók**
    + A pontozási magyarázatok mostantól opcionálisan menthetik a Conda és a pip-adatokat a megbízhatóbb szerializálás és deszerializálás céljából.
    + Hibajavítás az automatikus funkció-választóhoz.
    + Frissítve lett a mlflow. azureml. build_image az új API-val, az új implementáció által elérhető javított hibákkal.

+ **Hibajavítások és javítások**
  + A rendszer eltávolította a paramiko függőséget a azureml-Core-ból. Elavult figyelmeztetések jelennek meg az örökölt számítási cél csatolási módszereihez.
  + A Run. create_children teljesítményének javítása
  + A megmagyarázó bináris osztályozó használatával a rendszer kijavítja a valószínűségi sorrendet, ha a tanár valószínűsége az alakzat értékének méretezésére szolgál.
  + Továbbfejlesztett hibakezelés és üzenet az automatizált gépi tanuláshoz. 
  + Kijavítottuk az automatikus gépi tanulásra vonatkozó iterációs időtúllépési problémát.
  + Továbbfejlesztettük az automatikus gépi tanulás idősorozat-átalakítási teljesítményét.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.6

+ **Új funkciók**
  + A Top Values (`SummaryFunction.TOPVALUES`) és az alsó értékek (`SummaryFunction.BOTTOMVALUES`) összegző függvényei is hozzáadhatók.

+ **Hibajavítások és javítások**
  + Jelentősen javította `read_pandas_dataframe` teljesítményét.
  + Kijavítva egy olyan hibát, amely `get_profile()` értéket okoz a bináris fájlok adatfolyam mutató hibák esetén.
  + A telemetria-gyűjtemény programozott engedélyezését vagy letiltását lehetővé tévő @no__t – 0.
  + Módosította `get_profile()` viselkedését. A NaN értékeit a rendszer figyelmen kívül hagyja a min, a Mean, az STD és az Sum függvénynél, amely a pandák viselkedéséhez igazodik.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK a Python v 1.0.43

+ **Új funkciók**
  + A Azure Machine Learning mostantól biztosítja az első osztályú támogatást a népszerű gépi tanulási és adatelemzési keretrendszer Scikit – Learn. A [`SKLearn` kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)használatával a felhasználók egyszerűen betanítják és üzembe helyezhetik a Scikit-tanulási modelleket.
    + Ismerje meg, hogyan [futtathatja a hiperparaméter-hangolást a Scikit használatával – Ismerkedjen meg a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + További támogatás a ModuleStep létrehozásához a folyamatokban, valamint a modul-és ModuleVersion osztályok használatával az újrafelhasználható számítási egységek kezeléséhez.
  + Az ACI-webszolgáltatások mostantól támogatják az állandó scoring_uri a frissítéseken keresztül. A scoring_uri az IP-ről a teljes tartománynévre változik. A teljes tartománynévhez tartozó DNS-név címkéje konfigurálható úgy, hogy beállítja a dns_name_label a deploy_configuration. 
  + Automatikus gépi tanulás – új funkciók:
    + STL-Képtulajdonság az előrejelzéshez
    + A KMeans-fürtszolgáltatás engedélyezve van a szolgáltatások elsöprő számára
  + A AmlCompute-kvóták jóváhagyása csak gyorsabb volt! Most automatizáljuk a kvóta-kérelmek küszöbértéken belüli jóváhagyásának folyamatát. A kvóták működésével kapcsolatos további információkért tekintse meg a [kvóták kezelését](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)ismertető témakört.

+ **Előzetes verziójú funkciók**
    + Integráció a [MLflow](https://mlflow.org) 1.0.0 követésével azureml-MLflow-csomagon keresztül ([például jegyzetfüzetek](https://aka.ms/azureml-mlflow-examples)).
    + Jupyter-jegyzetfüzet küldése futtatásként. [API-referenciák dokumentációja](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Az [adatdrift-detektor](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) nyilvános előzetes verziója azureml-datadrift csomagon keresztül ([például jegyzetfüzetek](https://aka.ms/azureml-datadrift-example)). Az adateltolódás az egyik legfontosabb ok, ahol a modell pontossága az idő múlásával csökken. Ez akkor történik meg, ha az éles modellbe való kiszolgált adatok eltérnek a modell által betanított adatoktól. A pénzmosás-adatdrift detektor segítségével az ügyfél figyelheti az adateltolódást, és riasztást küld, ha a rendszer a drift észlelését észleli 

+ **Változtatások megszakítása**

+ **Hibajavítások és javítások**
  + A RunConfiguration betöltési és a mentési funkció támogatja a teljes elérési út megadását a korábbi működéshez szükséges teljes biztonsági mentéssel.
  + A ServicePrincipalAuthentication-ben hozzáadott gyorsítótárazási funkció alapértelmezés szerint ki van kapcsolva.
  + Több mintaterület naplózásának engedélyezése ugyanazzal a metrikai névvel.
  + A Model osztály mostantól megfelelően importálható a azureml. Core fájlból (`from azureml.core import Model`).
  + A folyamat lépéseiben a `hash_path` paraméter már elavult. Az új viselkedés a teljes forráskönyvtár kivonatolása, kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat.
  + A folyamat csomagjaiban a különböző `get_all` és `get_all_*` metódusok a `list` és a `list_*` helyett elavultak.
  + a azureml. Core. Get _run már nem szükséges osztályokat importálni, mielőtt visszaadná az eredeti Futtatás típusát.
  + Kijavított egy hibát, amelyben a webszolgáltatások frissítéseinek meghívása nem váltott ki frissítést.
  + Az AK-webszolgáltatások pontozási időtúllépésének 5ms és 300000ms között kell lennie. A pontozási kérelmek maximális megengedett scoring_timeout_ms 1 perc és 5 perc között van.
  + A LocalWebservice objektumok már `scoring_uri` és `swagger_uri` tulajdonságokkal rendelkeznek.
  + Áthelyezett kimenetek könyvtár létrehozása és kimenetek könyvtár feltöltése a felhasználói folyamatból. A futtatási előzmények SDK-t minden felhasználói folyamaton futtathatja. Ennek során meg kell oldania az elosztott képzések futtatásával kapcsolatos egyes szinkronizálási problémákat.
  + A felhasználói folyamat nevéből írt azureml-napló neve mostantól tartalmazza a folyamat nevét (csak elosztott képzés esetén) és a PID-t.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.5

+ **Hibajavítások és javítások**
  + A kétjegyű éves formátummal rendelkező, az érvényes évek tartománya frissült a Windows májusi kiadásának megfelelő értékekre. A tartomány 1930-2029 és 1950-2049 között lett módosítva.
  + Ha egy fájlt olvas be, és a `handleQuotedLineBreaks=True` beállítást állítja be, a `\r` új vonalként lesz kezelve.
  + Kijavított egy olyan hibát, amely a `read_pandas_dataframe` meghibásodását okozta bizonyos esetekben.
  + A `get_profile` teljesítményének növelése.
  + Javított hibaüzenetek.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning adat PREP SDK v 1.1.4

+ **Új funkciók**
  + Mostantól a következő kifejezés nyelvi függvények használatával kinyerheti és elemezheti a DateTime értékeket új oszlopokra.
    + `RegEx.extract_record()` kibontja a DateTime elemeket egy új oszlopba.
    + @no__t – 0 datetime objektumokat hoz létre a különböző datetime elemekről.
  + A `get_profile()` hívásakor láthatja, hogy a quantile oszlopai (EST.) szerint vannak megjelölve, hogy egyértelműen jelezze az értékek közelítését.
  + Mostantól a * * globbing is használhatja az Azure Blob Storage való olvasáskor.
    + például @no__t – 0

+ **Hibajavítások**
  + Kijavított egy olyan hibát, amely a Parquet-fájlok távoli forrásból (Azure Blob) való olvasásával kapcsolatos.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK a Python v 1.0.39
+ **Változások**
  + A konfigurációs auto_prepare_environment futtatása elavult, és az automatikus előkészítés válik az alapértelmezett értékre.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.3

+ **Új funkciók**
  + A PostgresSQL-adatbázisból való olvasás támogatása a read_postgresql meghívásával vagy adattár használatával.
    + Példák a útmutatókra:
      + [Adatfeldolgozási jegyzetfüzet](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Adattár-jegyzetfüzet](https://aka.ms/aml-data-prep-datastore-nb)

+ **Hibajavítások és javítások**
  + Az oszlop típusú konverzióval kapcsolatos hibák Javítva:
  + A most helyesen konvertál egy logikai vagy numerikus oszlopot egy logikai oszlopra.
  + Most nem sikerül, ha egy Date (dátum) típusú oszlopot kíván beállítani dátum típusként.
  + Továbbfejlesztett JoinType-típusok és kísérő dokumentáció. Két adatfolyamok való csatlakozáskor mostantól megadhatja az alábbi típusú csatlakozások egyikét:
    + NINCS, EGYEZÉS, BELSŐ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Továbbfejlesztett adattípus, amely több dátumformátum felismerésére használható.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

A Azure Portal mostantól a következőket teheti:
+ Automatizált ML-kísérletek létrehozása és futtatása 
+ Hozzon létre egy notebook virtuális gépet, hogy kipróbálja a minta Jupyter notebookokat vagy a sajátját.
+ Új authoring szakasz (előzetes verzió) a Machine Learning szolgáltatás munkaterületen, amely magában foglalja az automatizált Machine Learning, a vizuális felületet és a szolgáltatott notebookos virtuális gépeket
    + Modell automatikus létrehozása automatizált gépi tanulás használatával 
    + Drag and drop vizuális felület használata kísérletek futtatásához
    + Hozzon létre egy notebook virtuális gépet az adatelemzéshez, modellek létrehozásához és szolgáltatások telepítéséhez.
+ Élő diagram és metrika frissítése a jelentések futtatása és a részletek lapok futtatása
+ A naplófájlok, a kimenetek és a pillanatképek frissített fájlkezelője a futtatási részletek oldalain.
+ Új és továbbfejlesztett jelentés-létrehozási élmény a kísérletek lapon. 
+ Lehetőség van a config. JSON fájl letöltésére a Azure Machine Learning munkaterület áttekintés lapján.
+ Machine Learning szolgáltatás-munkaterület Azure Databricks munkaterületről való létrehozásának támogatása 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK a Python v 1.0.33
+ **Új funkciók**
  + A _munkaterület. Create_ metódus most elfogadja az alapértelmezett FÜRTKONFIGURÁCIÓ a processzor-és GPU-fürtökhöz.
  + Ha a munkaterület létrehozása sikertelen, a függő erőforrások megtisztítása megtörténik.
  + Az alapértelmezett Azure Container Registry SKU alapértékre lett állítva.
  + A Azure Container Registry lustán jön létre, ha szükséges a futtatáshoz vagy a rendszerkép létrehozásához.
  + A betanítási környezetek támogatása.

### <a name="notebook-virtual-machine"></a>Notebook virtuális gép 

A notebookos virtuális gépek biztonságos, nagyvállalati használatra kész üzemeltetési környezetként használhatók Jupyter jegyzetfüzetekhez, ahol gépi tanulási kísérleteket végezhet, webvégpontként telepíthet modelleket, és elvégezheti az Azure Machine Learning SDK által támogatott összes egyéb műveletet a Python használatával. Számos képességet biztosít:
+ Az előre [konfigurált jegyzetfüzetek gyors üzembe helyezése](tutorial-1st-experiment-sdk-setup.md)@no__t – a ügyfélcsoportból az Azure Machine learning SDK és a kapcsolódó csomagok legújabb verzióját tartalmazza.
+ A hozzáférést bizonyított technológiák, például HTTPS, Azure Active Directory hitelesítés és engedélyezés biztosítja.
+ A notebookok és kódok megbízható Felhőbeli tárolása a Azure Machine Learning-munkaterület blob Storage-fiókban. A notebook virtuális gépet biztonságosan törölheti a munka elvesztése nélkül.
+ Előtelepített minta-jegyzetfüzetek Azure Machine Learning funkciók megismeréséhez és kipróbálásához.
+ Az Azure-beli virtuális gépek teljes körű testreszabási képességei, bármilyen virtuálisgép-típus, bármely csomag és bármely illesztőprogram. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>A Azure Machine Learning SDK for Python v 1.0.33 megjelent.

+ Általánosan elérhető az Azure ML Hardware Accelerated Models a [FPGA](concept-accelerate-with-fpgas.md) .
  + Mostantól [a azureml-extra gyorsulás-models csomagot is használhatja](how-to-deploy-fpga-web-service.md) a következőhöz:
    + Egy támogatott Deep neurális hálózat (ResNet 50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG) súlyozásának betanítása
    + Az adatátviteli tanulás használata a támogatott DNN
    + A modell regisztrálása modellkezelés szolgáltatással és a modell tárolóba helyezése
    + A modell üzembe helyezése Azure-beli virtuális gépen egy Azure Kubernetes Service-(ak-) fürt FPGA
  + A tároló üzembe helyezése egy [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) kiszolgáló eszközön
  + Adja meg az adatait a gRPC-végponttal ezzel a [mintával](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált Machine Learning

+ A featurizers dinamikus hozzáadását lehetővé tevő szolgáltatások a teljesítmény optimalizálása érdekében. Új featurizers: munkabeágyazások, a bizonyítékok súlya, a cél kódolása, a szöveges cél kódolása, a fürt távolsága
+ Intelligens önéletrajz a betanítási/érvényes osztások automatikus ML-ben való kezeléséhez
+ Néhány memória-optimalizálási módosítás és futásidejű teljesítmény javítása
+ Teljesítmény javítása a modellben – magyarázat
+ ONNX-modell átalakítása helyi futtatáshoz
+ Almintavételezési támogatás hozzáadva
+ Intelligens Leállítás, ha nincs megadva kilépési feltétel
+ Halmozott együttesek

+ Idősorozat-előrejelzés
  + Új prediktív előrejelzési függvény   
  + Mostantól használhatja az idősorozat-adatelemzési időszakon belüli többit is
  + Az idősorozat-elmaradás konfigurálásához hozzáadott új funkciók 
  + Új funkciókkal bővült a működés közbeni ablak összesített funkcióinak támogatása
  + Új üdülés észlelése és Képtulajdonság, ha az országkód definiálva van a kísérleti beállításokban

+ Azure Databricks
  + Az idősorozat-előrejelzés és a modell explainabilty/értelmező képességének engedélyezése
  + Mostantól megszakíthatja és folytathatja a (folytatás) automatizált ML-kísérletek elvégzését
  + Többprocesszoros feldolgozás támogatása

### <a name="mlops"></a>MLOps
+ **Helyi üzembe helyezési & hibakeresése pontozási tárolók esetén**<br/> Most már helyileg is üzembe helyezhet egy ML-modellt, és gyorsan megismételheti a pontozási fájlt és a függőségeket, hogy a várt módon viselkedjenek.

+ **Bevezetett InferenceConfig & Model. Deploy ()**<br/> A modell üzembe helyezése mostantól támogatja a forrás mappa megadását egy bejegyzési parancsfájllal, ugyanazokat a RunConfig.  Emellett a modell üzembe helyezését egyetlen parancsra is egyszerűsítettük.

+ **Git-hivatkozások követése**<br/> Az ügyfelek egy ideig alapszintű git-integrációs képességeket igényelnek, mivel ez segít a teljes körű naplózás fenntartásában. Az Azure ML-ben a git-hez kapcsolódó metaadatok (tárház, véglegesítés, tiszta állapot) tekintetében a főbb entitások nyomon követését valósították meg. Az SDK és a CLI automatikusan összegyűjti ezeket az információkat.

+ **Modell profilkészítés & érvényesítési szolgáltatás**<br/> Az ügyfelek gyakran kifogásolják a következtetési szolgáltatáshoz kapcsolódó számítási kapacitás mértékét. A Model profilkészítési szolgáltatás segítségével az ügyfél minta bemeneteket biztosíthat, és 16 különböző CPU-/memória-konfigurációban dolgozunk fel, hogy a telepítés optimális méretezése megtörténjen.

+ **Saját alaprendszerkép használata a következtetéshez**<br/> Egy másik gyakori panasz volt a nehézség a kísérletezéstől az ismételt megosztási függőségekre való áttérés során. Az új alaprendszerkép-megosztási képességgel most már felhasználhatja a kísérletezési alapképeket, a függőségeket és az összes, a következtetést. Ennek fel kell gyorsítania az üzemelő példányokat, és csökkentenie kell a belső és a külső hurok közötti hézagot.

+ **Továbbfejlesztett hencegő séma-létrehozási élmény**<br/> A korábbi hencegés-létrehozási módszer volt a hiba, és nem lehet automatizálni. Új, beágyazott módon hozhat létre a kisegítő sémákat bármely Python-függvényből a dekoratőrs használatával. Nyílt forráskódú ez a kód, és a séma generálási protokollja nem párosul az Azure ML platformmal.

+ **A Azure ML CLI általánosan elérhető (GA)**<br/> A modellek mostantól egyetlen CLI-paranccsal is üzembe helyezhetők. Általános vásárlói visszajelzésünk szerint a Jupyter-jegyzetfüzetből senki sem telepít ML modellt. A [**CLI-hivatkozás dokumentációja**](https://aka.ms/azmlcli) frissítve lett.


## <a name="2019-04-22"></a>2019-04-22

A Azure Machine Learning SDK for Python v 1.0.30 megjelent.

A [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) bevezette a közzétett folyamat új verziójának hozzáadását, miközben megtartja ugyanazt a végpontot.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.2

Megjegyzés: az adatprep Python SDK többé nem telepíti `numpy` és `pandas` csomagot. Lásd: a [telepítési utasítások frissítése](https://aka.ms/aml-data-prep-installation).

+ **Új funkciók**
  + Mostantól a pivot átalakítót is használhatja.
    + Útmutató: [pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + Mostantól reguláris kifejezéseket is használhat natív függvényekben.
    + Példák:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Mostantól használhatja a `to_upper` @ no__t-1and `to_lower` @ no__t-3functions kifejezést kifejezés nyelvén.
  + Most már megtekintheti az egyes oszlopok egyedi értékeinek számát egy adatprofilban.
  + A gyakran használt olvasói lépések némelyikében a `infer_column_types` argumentumot is átadhatja. Ha `True` értékre van állítva, az adat-előkészítési művelet megkísérli az oszlopok típusának észlelését és automatikus átalakítását.
    + a `inference_arguments` már elavult.
  + Most meghívhatja a `Dataflow.shape` értéket.

+ **Hibajavítások és javítások**
  + `keep_columns` @ no__t-1now egy további opcionális argumentumot is elfogad @no__t – 2, amely ellenőrzi, hogy a `keep_columns` eredménye tartalmaz-e oszlopokat.
  + Az összes olvasó lépés (amely egy fájlból olvas) mostantól egy további opcionális argumentumot is elfogad @no__t – 0.
  + A Panda dataframe és az adatprofilok beolvasásának jobb teljesítménye.
  + Kijavítva egy olyan hibát, amelyben egy adatfolyam egyetlen lépésének szeletelése egyetlen indexszel meghiúsult.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Most már újra elküldheti egy meglévő parancsfájl futtatását egy meglévő távoli számítási fürtön. 
  + Mostantól egy közzétett folyamatot is futtathat új paraméterekkel a folyamatok lapon. 
  + A Run details mostantól támogatja az új pillanatkép-fájl megjelenítőjét. Egy adott Futtatás elküldésekor megtekintheti a könyvtár pillanatképét. A Futtatás elindításához le is töltheti a beküldött jegyzetfüzetet.
  + Mostantól megszakíthatja a szülő futtatását a Azure Portalból.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK a Python v 1.0.23

+ **Új funkciók**
  + A Azure Machine Learning SDK mostantól támogatja a Python 3,7-es verzióit.
  + Azure Machine Learning DNN becslések már beépített, többverziós támogatást biztosít. Például `TensorFlow` @ no__t-1estimator most elfogad egy `framework_version` paramétert, és a felhasználók megadhatják a következő verziót: "1,10" vagy "1,12". Az aktuális SDK-kiadás által támogatott verziók listáját a kívánt Framework osztályban (például `TensorFlow.get_supported_versions()`) hívja meg `get_supported_versions()` értékre.
  A legújabb SDK-kiadás által támogatott verziók listáját a [DNN kalkulátor dokumentációjában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)találja.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.1

+ **Új funkciók**
  + A read_ * átalakítók használatával több adattár/DataPath/DataReference-forrás is olvasható.
  + A következő műveleteket végezheti el az oszlopokon egy új oszlop létrehozásához: osztás, padló, adatmegosztás, teljesítmény, hossz.
  + Az adat-előkészítés mostantól az Azure ML diagnosztikai csomag része, és alapértelmezés szerint naplózza a diagnosztikai adatokat.
    + A kikapcsoláshoz állítsa a környezeti változót True (igaz) értékre: DISABLE_DPREP_LOGGER

+ **Hibajavítások és javítások**
  + Továbbfejlesztett kód dokumentáció a gyakran használt osztályokhoz és függvényekhez.
  + Kijavítva egy olyan hibát a auto_read_file, amely nem tudta beolvasni az Excel-fájlokat.
  + Hozzáadott lehetőség a mappa felülírásához a read_pandas_dataframe.
  + A dotnetcore2 függőségek telepítésének jobb teljesítménye, valamint a Fedora 27/28 és az Ubuntu 1804 támogatása.
  + Továbbfejlesztettük az Azure-Blobok olvasásának teljesítményét.
  + Az oszlop típusú észlelés mostantól támogatja a Long típusú oszlopokat.
  + Kijavítva egy hiba, amelyben néhány dátumérték időbélyegként jelenik meg a Python datetime-objektumok helyett.
  + Kijavítva egy olyan hibát, amelyben egyes típusok száma nem egész számként, hanem párosként jelenik meg.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK a Python v 1.0.21

+ **Új funkciók**
  + A *azureml. Core. Run. create_children* metódus lehetővé teszi több gyermek kis késleltetésű létrehozását egyetlen hívással.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.0

+ **Változtatások megszakítása**
  + Az adat-előkészítő csomag fogalma elavult, és már nem támogatott. Ahelyett, hogy egy csomagban több adatfolyamok is megmaradjon, külön adatfolyamok maradhat.
    + Útmutató: [adatfolyamok-jegyzetfüzet megnyitása és mentése](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Új funkciók**
  + Az adat-előkészítő mostantól felismerheti az adott szemantikai típusnak megfelelő oszlopokat, és ennek megfelelően feloszthatja őket. A jelenleg támogatott STypes a következők: e-mail-cím, földrajzi koordináták (szélesség & hosszúság), IPv4-és IPv6-címek, USA-beli telefonszámok és US zip-kódok.
    + Útmutató: [szemantikai típusok jegyzetfüzet](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Az adat-előkészítő mostantól a következő műveleteket támogatja két numerikus oszlopból származó eredő oszlop létrehozásához: kivonás, szorzás, osztás és maradék.
  + A adatfolyam `verify_has_data()` meghívásával ellenőrizze, hogy a adatfolyam-e a végrehajtott rekordok létrehozásakor.

+ **Hibajavítások és javítások**
  + Mostantól megadhatja, hogy hány raktárhelyet használjon a hisztogram a numerikus oszlopok profiljaihoz.
  + A `read_pandas_dataframe` átalakításhoz most a DataFrame karakterlánc vagy bájt típusú oszlopnevek szükségesek.
  + Javítva lett egy hiba a `fill_nulls` átalakításban, ahol az értékek nem lettek megfelelően kitöltve, ha az oszlop hiányzik.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK a Python v 1.0.18

 + **Változások**
   + Az azureml-tensorboard csomag helyettesíti a azureml-tensorboard.
   + Ebben a kiadásban beállíthat egy felhasználói fiókot a felügyelt számítási fürtön (amlcompute), miközben létrehozta. Ezt a tulajdonságok a létesítési konfigurációban való átadásával teheti meg. További részleteket az [SDK-dokumentációban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remotelogin-port-public-access--notspecified--)talál.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.17

+ **Új funkciók**
  + A mostantól támogatja két numerikus oszlop hozzáadását egy eredményül kapott oszlop létrehozásához a kifejezés nyelvének használatával.

+ **Hibajavítások és javítások**
  + Javítottuk a random_split dokumentációját és paraméterének ellenőrzését.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.16

+ **Hibajavítás**
  + Kijavított egy egyszerű szolgáltatás hitelesítési problémáját, amelyet egy API-változás okozott.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK a Python v 1.0.17

+ **Új funkciók**

  + A Azure Machine Learning mostantól első osztályú támogatást biztosít a népszerű DNN-keretrendszer láncolásához. A [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztályú felhasználók egyszerűen betanítják és üzembe helyezhetik a Láncos modelleket.
    + Ismerje meg, hogyan [futtathatja az elosztott képzést a ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Ismerje meg, hogyan [futtathat hiperparaméter-hangolást a chainer használatával a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning a folyamatok hozzáadásának képessége, amely az adattár módosításain alapuló futtatást indít el. A folyamat [ütemezett jegyzetfüzete](https://aka.ms/pl-schedule) frissült a szolgáltatás megjelenítéséhez.

+ **Hibajavítások és javítások**
  + A source_directory_data_store tulajdonság egy kívánt adattárra (például blob-tárolóra) való beállításához a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)-hez megadott [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) -re vonatkozó támogatási Azure Machine Learningi folyamatokat adunk hozzá. Alapértelmezés szerint az Azure file Store-t használja a háttérként szolgáló adattárként, amely szabályozási problémákba ütközik, amikor nagy számú lépést hajt végre egyszerre.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új fogd és vidd táblázat szerkesztői élmény a jelentésekhez. A felhasználók a megfelelő oszlopokat a tábla azon területére húzhatja, ahol megjelenik a táblázat előnézete. Az oszlopok átrendezhető.
  + Új naplófájlok megjelenítője
  + A műveletek lapon található kísérletekre, számításokra, modellekre, lemezképekre és központi telepítésekre mutató hivatkozások

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.15

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja a adatfolyam származó fájlok adatfolyamként való írását. A Emellett lehetővé teszi a fájlok stream-neveinek módosítását új fájlnevek létrehozásához.
    + Útmutató: [a file Streams notebook használata](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Hibajavítások és javítások**
  + Nagyobb teljesítmény a t-Digest nagy adatkészleteken.
  + Az adat-előkészítő mostantól támogatja az adatok DataPath való olvasását.
  + Egy gyors kódolás a logikai és a numerikus oszlopokon is működik.
  + Egyéb egyéb hibajavítások.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK a Python v 1.0.15

+ **Új funkciók**
  + Azure Machine Learning folyamatokat hozzáadott AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook) és időalapú ütemezési funkció ([notebook](https://aka.ms/pl-schedule)).
  +  A DataTranferStep frissítve lett az Azure SQL Server és az Azure Database for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)) használatához.

+ **Változások**
  + A `PublishedPipeline.get` helyett elavult @no__t – 0.
  + A `Schedule.get` helyett elavult @no__t – 0.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.12

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja az Azure SQL Database-adatbázisokból való olvasást az adattár használatával.
 
+ **Változások**
  + Javítottuk a nagy mennyiségű adathoz tartozó bizonyos műveletek memóriájának teljesítményét.
  + @no__t – 0 most meg kell adni `temp_folder` értéket.
  + A (z) `ColumnProfile` `name` tulajdonsága elavult – használja a `column_name` helyet.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK a Python v 1.0.10

+ **Módosítások**: 
  + Az Azure ML SDK-nak már nincs Azure-CLI-csomagja függőségként. Pontosabban, az Azure-CLI-Core és az Azure-CLI-profil függőségei el lettek távolítva a azureml-Core-ból. A felhasználók a következő változásokat érintik:
    + Ha az "az login", majd az azureml-SDK használatával végzi az SDK-t, a böngésző vagy az eszköz kódjának naplózása még egyszer megtörténik. Nem használja az "az login" által létrehozott hitelesítő adatok állapotát.
    + Az Azure CLI-hitelesítéshez, például az "az login" használata esetén használja az _azureml. Core. Authentication. AzureCliAuthentication_ osztályt. Azure CLI-hitelesítés esetén a _pip az Azure-CLI telepítését_ a Python-környezetben, ahol a azureml-SDK telepítve van.
    + Ha az automatizáláshoz az "az login" szolgáltatást használja, javasoljuk, hogy az _azureml. Core. Authentication. ServicePrincipalAuthentication_ osztályt használja, mivel a azureml-SDK nem fogja használni az Azure CLI által létrehozott hitelesítő adatokat. 

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.8

+ **Hibajavítások**
  + Javítottuk az adatprofilok beolvasásának teljesítményét.
  + A hibajelentéssel kapcsolatos rögzített kisebb hibák.
  
### <a name="azure-portal-new-features"></a>Azure Portal: új funkciók
+ A jelentések új, húzással kapcsolatos felhasználói felülete. A felhasználók a megfelelő típusú oszlopokat vagy attribútumokat a diagram területére húzhatja, ahol a rendszer automatikusan kiválasztja a megfelelő diagramtípust a felhasználó számára az adatok típusától függően. A felhasználók módosíthatják a diagram típusát más alkalmazható típusokra, illetve további attribútumokat adhatnak hozzá.

    Támogatott diagramok típusai:
    - Vonalas diagram
    - Hisztogram
    - Halmozott sávdiagram
    - Box-ábra
    - Pontdiagram
    - Buborék ábrázolása
+ A portál mostantól dinamikusan generál jelentéseket a kísérletekhez. Amikor egy felhasználó elküld egy kísérletet, a rendszer automatikusan létrehoz egy jelentést a naplózott metrikákkal és gráfokkal, hogy az összehasonlítás lehetővé váljon a különböző futtatások között. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK a Python v 1.0.8

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.7

+ **Új funkciók**
  + Az adattár fejlesztése (dokumentálva az [adattár](https://aka.ms/aml-data-prep-datastore-nb)útmutatójában)
    + Lehetőség van az Azure-fájlmegosztás és a ADLS-adattárolók beolvasására és írására.
    + Az adattárolók használatakor az adatelőkészítés mostantól támogatja az egyszerű szolgáltatásnév hitelesítés használatát az interaktív hitelesítés helyett.
    + A wasb és a wasbs URL-címek támogatása.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.6

+ **Hibajavítások**
  + Rögzített hiba a nyilvánosan olvasható Azure Blob-tárolók beolvasásával a Sparkban

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK a Python v 1.0.6
+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.4

+ **Új funkciók**
  + a `to_bool` függvény mostantól lehetővé teszi, hogy a nem egyező értékeket a program a hibák értékére konvertálja. Ez az új alapértelmezett eltérés a `to_bool` és a `set_column_types` esetében, míg az előző alapértelmezett viselkedés a nem egyező értékek hamis értékre konvertálása.
  + @No__t-0 hívásakor új lehetőség van a null/hiányzó értékek értelmezésére a numerikus oszlopokban a NaN értékkel.
  + Bizonyos kifejezések visszatérési típusának további ellenőrzése, hogy a konzisztencia és a hibák korának legyenek.
  + A `parse_json` meghívásával egy oszlop értékeit JSON-objektumokként elemezheti, és kibonthatja őket több oszlopra.

+ **Hibajavítások**
  + Kijavított egy hibát, amely összeomlott `set_column_types` a Python 3.5.2-ben.
  + Kijavítva egy hiba, amely összeomlott az adattárhoz való csatlakozáskor egy pénzmosás-rendszerkép használatával.

+ **Frissítések**
  * [Például jegyzetfüzetek](https://aka.ms/aml-data-prep-notebooks) az első lépések oktatóanyagok, esettanulmányok és útmutatók.

## <a name="2018-12-04-general-availability"></a>2018-12-04: általánosan elérhető

A Azure Machine Learning már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning számítás
Ebben a kiadásban bejelentjük az új felügyelt számítási élményt a [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute)feladatokon keresztül. Ez a számítási cél a Azure Machine Learning Azure Batch AI számítását váltja fel. 

Ez a számítási cél:
+ A modell betanítására és a Batch-következtetésre/pontozásra szolgál.
+ Egy-a több csomópontos számítás
+ A fürt kezelése és a feladatütemezés a felhasználó számára
+ Alapértelmezés szerint az autoskálázás
+ A processzor-és a GPU-erőforrások támogatása 
+ Alacsony prioritású virtuális gépek használatát teszi lehetővé csökkentett áron

Azure Machine Learning a számításokat a Pythonban, Azure Portal vagy a parancssori felület használatával lehet létrehozni. Ezt a munkaterület régiójában kell létrehozni, és nem csatolhatók más munkaterületekhez. Ez a számítási cél egy Docker-tárolót használ a futtatásához, és a függőségek csomagjaival replikálja ugyanazt a környezetet az összes csomóponton.

> [!Warning]
> Javasoljuk, hogy hozzon létre egy új munkaterületet Azure Machine Learning számítás használatához. Távoli esély van arra, hogy a felhasználók egy meglévő munkaterületről próbálnak létrehozni Azure Machine Learning számítást, és ez hibát jelez. A munkaterületen lévő meglévő számítások továbbra is nem érintik a munkát.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK a Python v 1.0.2-hez
+ **Változtatások megszakítása**
  + Ezzel a kiadással eltávolítjuk a virtuális gépek Azure Machine Learningból való létrehozásának támogatását. Továbbra is csatlakoztathat meglévő Felhőbeli virtuális gépet vagy távoli helyszíni kiszolgálót. 
  + Emellett a BatchAI támogatását is eltávolítja, amelyek mindegyikét Azure Machine Learning számításon keresztül kell támogatni.

+ **Új**
  + Gépi tanulási folyamatokhoz:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissített**
  + Gépi tanulási folyamatokhoz:
    + A [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) most elfogadja a runconfig
    + A [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) mostantól egy SQL-adatforrásból másolható
    + Az SDK-ban a közzétett folyamatok futtatásához szükséges ütemtervek létrehozásához és frissítéséhez tartozó funkciók ütemezhetők.

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning adat-előkészítési SDK v 0.5.2
+ **Változtatások megszakítása** 
  * a `SummaryFunction.N` a következőre lett átnevezve: `SummaryFunction.Count`.
  
+ **Hibajavítások**
  * A legújabb pénzmosás-jogkivonatot használja a távoli futtatásokban lévő adattárolók beolvasásához és az azokból való íráshoz. Korábban, ha a pénzmosás-futtatási jogkivonat frissült a Pythonban, az adatelőkészítési futtatókörnyezet nem frissül a frissített pénzmosás-futtatási jogkivonattal.
  * További világosabb hibaüzenetek
  * a to_spark_dataframe () többé nem fog összeomlani, ha a Spark `Kryo` szerializálást használ
  * Az értékek száma felügyelő mostantól több mint 1000 egyedi értéket jeleníthet meg
  * A véletlenszerű felosztás már nem sikerül, ha az eredeti adatfolyam nem rendelkezik névvel  

+ **További információ**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentumok és jegyzetfüzetek
+ ML-folyamatok
  + Új és frissített jegyzetfüzetek a folyamatok, a kötegek hatóköre és a stílus-átadási példákkal való ismerkedéshez: https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md)
  + Útmutató a [Batch-előrejelzések folyamatokkal történő futtatásához](how-to-run-batch-predictions.md)
+ Számítási cél Azure Machine Learning
  + A [minta jegyzetfüzetek](https://aka.ms/aml-notebooks) mostantól frissülnek az új felügyelt számítás használatára.
  + [További tudnivalók a számítási feladatokról](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure Portal: új funkciók
+ [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute) típusok létrehozása és kezelése a portálon.
+ A kvóta használatának figyelése és a Azure Machine Learning számításhoz szükséges [kvóta igénylése](how-to-manage-quotas.md) .
+ Azure Machine Learning számítási fürt állapotának valós idejű megtekintése.
+ A virtuális hálózatok támogatása Azure Machine Learning számítási és Azure Kubernetes-szolgáltatás létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatokat a meglévő paraméterekkel.
+ Új [automatizált gépi tanulási diagramok](how-to-understand-automated-ml.md) a besorolási modellekhez (lift, nyereség, kalibrálás, funkció fontossági diagramja modell-magyarázattal) és regressziós modellekkel (maradványok és funkciók fontossági diagramja a modell magyarázatával). 
+ A folyamatok megtekinthetők Azure Portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK a Python v 0.1.80

+ **Változtatások megszakítása** 
  * a *azureml. Train. Widgets* névtér át lett helyezve a *azureml. Widgets*-re.
  * *azureml. Core. számítás. a AmlCompute* a következő osztályokat elavult: *azureml. Core. számítás. BatchAICompute* és *azureml. Core. számítás. DSVMCompute*. A későbbi kiadásokban az utóbbi osztály el lesz távolítva. A AmlCompute osztálynak egyszerűbb definíciója van, és egyszerűen csak egy vm_size és a max_nodes van szüksége, és a fürt automatikusan a 0 értékről a max_nodes, amikor a rendszer elküld egy feladatot. A [mintául szolgáló jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lettek ezzel az információval, és használati példákat kell megadni. Reméljük, hogy ezt az egyszerűsítést és a sok izgalmas funkciót egy későbbi kiadásban szeretné megtekinteni!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning adat-előkészítési SDK v 0.5.1 

További információ az adat-előkészítő SDK-ról a [dokumentációs dokumentumok](https://aka.ms/data-prep-sdk)beolvasásával.
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI-t a Adatelőkészítés-csomagok végrehajtásához és egy adatkészlet vagy adatfolyam adatprofiljának megtekintéséhez.
   * Újratervezett SetColumnType API a használhatóság javítása érdekében
   * Átnevezve smart_read_file – auto_read_file
   * Most már tartalmazza az adatprofilban található ferde és csúcsosságát
   * Minta rétegzett mintavételezéssel
   * A CSV-fájlokat tartalmazó Zip-fájlokból is olvasható
   * Feloszthatja az adathalmazokat a Wise véletlenszerű felosztással (például test-Train készletekbe)
   * Az összes oszlop adattípusát lekérheti egy adatfolyam vagy egy adatprofilból a `.dtypes` meghívásával
   * Lekérheti a sorok darabszámát egy adatfolyam vagy egy adatprofilból a `.row_count` meghívásával

+ **Hibajavítások**
   * Rögzített hosszú – dupla átalakítás 
   * Rögzített érvényesítés az oszlop hozzáadása után 
   * Kijavított egy problémát a FuzzyGrouping, amelyben bizonyos esetekben nem észlelhetők a csoportok
   * Rögzített rendezési függvény a többoszlopos rendezési sorrend figyelembevételéhez
   * A rögzített és/vagy kifejezések hasonlóak a `pandas` által kezeltek számára
   * Rögzített olvasás a dbfs útvonalról
   * A hibaüzenetek jobban megérthetők 
   * Most már nem fog sikerülni a távoli számítási célhoz a pénzmosás-token használatával való olvasáskor
   * A Linux DSVM már nem sikerül
   * Most már nem összeomlik, ha a nem sztring típusú értékek karakterlánc-predikátumokban vannak
   * A most már kezeli az állítási hibákat, ha a adatfolyam megfelelően működnek
   * A mostantól támogatja a dbutils csatlakoztatott tárolási tárolóhelyeket Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
A Azure Machine Learning Azure Portal a következő frissítésekkel rendelkezik:
  * Új **folyamatok** lap a közzétett folyamatokhoz.
  * Meglévő HDInsight-fürt számítási célként való csatolásának támogatása.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK a Python v 0.1.74

+ **Változtatások megszakítása** 
  * \* A munkaterület. a compute_targets, az adattárolók, a kísérletek, a képek, a modellek és a *webszolgáltatások* a metódusok helyett tulajdonságok. Például cserélje le a *munkaterület. compute_targets ()* *munkaterületre. compute_targets*.
  * *Futtassa a parancsot. Get _context* elavulttá válik *. Get _submitted_run*. Az utóbbi metódus el lesz távolítva a következő kiadásokban.
  * A *PipelineData* osztály most a datastore_name helyett paraméterként egy adattár-objektumot vár. Hasonlóképpen, a *folyamat* a default_datastore_name helyett a default_datastore is elfogadja.

+ **Új funkciók**
  * A Azure Machine Learning-folyamatok [minta notebookja](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) már MPI-lépéseket használ.
  * A Jupyter-jegyzetfüzetek RunDetails widgetje frissül a folyamat vizualizációjának megjelenítéséhez.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning adat-előkészítési SDK v 0.4.0 
 
+ **Új funkciók**
  * Az adatprofilba felvett típusok száma 
  * Az értékek száma és a hisztogram mostantól elérhető
  * További százalékos érték az adatprofilban
  * A medián az összegzésben érhető el
  * A Python 3,7 mostantól támogatott
  * Ha olyan adatfolyam ment, amely adattárolókat tartalmaz egy Adatelőkészítés-csomagba, az adattár adatai a Adatelőkészítés-csomag részeként maradnak meg
  * Az adattárba való írás mostantól támogatott 
        
+ **Hiba javítva**
  * a 64 bites, előjel nélküli egész túlcsordulások mostantól megfelelően kezelhetők Linux rendszeren
  * A smart_read egyszerű szöveges fájljainak kijavított helytelen szöveges címkéje
  * A karakterlánc-oszlop típusa most megjelenik a metrikák nézetben
  * A types Count most úgy van rögzítve, hogy megjelenítse az egyes FieldType leképezett ValueKinds az egyes felhasználók helyett
  * A Write_to_csv már nem sikerül, ha az elérési út karakterláncként van megadva
  * A Replace utasítás használata esetén a "keresés" üres érték nem fog működni. 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK a Python v 0.1.68

+ **Új funkciók**
  * Több-bérlős támogatás új munkaterület létrehozásakor.

+ **Kijavított hibák**
  * A webszolgáltatás telepítésekor már nem kell rögzítenie a pynacl-függvénytár verzióját.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning adat-előkészítési SDK v 0.3.0

+ **Új funkciók**
  * Hozzáadott metódus transform_partition_with_file (script_path), amely lehetővé teszi a felhasználók számára a végrehajtandó Python-fájl elérési útjának továbbítását

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK a Python v 0.1.65
A [0.1.65 verzió](https://pypi.org/project/azureml-sdk/0.1.65) új funkciókat, több dokumentációt, hibajavítást és több [minta jegyzetfüzetet](https://aka.ms/aml-notebooks)tartalmaz.

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

+ **Változtatások megszakítása**
  * Munkaterület. kísérletek, munkaterület. modellek, munkaterület. compute_targets, munkaterület. images, munkaterület. Web _services visszatérési szótár, korábban visszaadott lista. Lásd: [azureml. Core. Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-dokumentáció.

  * Az automatizált Machine Learning az elsődleges mérőszámokból származó normalizált középértékes hibát eltávolította.

+ **HyperDrive**
  * Különböző HyperDrive-hibajavítások a Bayes-as verzióhoz, teljesítménybeli javítások a metrikák beszerzése érdekében. 
  * Tensorflow 1,10 verziófrissítés 1,9 
  * Docker-rendszerkép optimalizálása a hideg indításhoz. 
  * A feladatok mostantól akkor is helyes állapotot jelentenek, ha a nullától eltérő hibakóddal kilépnek. 
  * RunConfig az SDK-ban. 
  * A HyperDrive futtatási objektuma támogatja a megszakítást a normál futtatáshoz hasonlóan: nem kell paramétereket átadni. 
  * A widgetek az elosztott futtatások és a HyperDrive futtatására szolgáló legördülő értékek állapotának fenntartására szolgáló funkciókat biztosítanak. 
  * A TensorBoard és más naplófájlok támogatják a paraméter-kiszolgálót. 
  * Az Intel (R) MPI támogatása a szolgáltatás oldalán. 
  * Hibajavítás az elosztott Futtatás javításához a BatchAI-ben történő ellenőrzés során. 
  * A Context Manager most azonosítja az elsődleges példányt. 

+ **Azure Portali élmény**
  * a log_table () és a log_row () támogatottak a futtatási részletekben. 
  * Diagramok automatikus létrehozása a táblázatok és sorok számára 1, 2 vagy 3 numerikus oszlop és egy nem kötelezően megadandó oszlop esetében.

+ **Automatizált Machine Learning**
  * Javított hibakezelés és dokumentáció 
  * A futtatási tulajdonságok lekérési teljesítményével kapcsolatos hibák elhárítása. 
  * Rögzített folytatási hiba történt. 
  * Rögzített ensembling-iterációs problémák.
  * Rögzített betanítási hiba a MAC operációs rendszeren.
  * Az egyéni ellenőrzési forgatókönyvben lévő PR/ROC görbe leegyszerűsítése a makróban.
  * Eltávolított további index-logikát.
  * Eltávolított szűrő a get_output API-ból.

+ **Folyamatok**
  * A () metódust hozzáadta egy folyamathoz, amely közvetlenül a végrehajtás megkövetelése nélkül teszi közzé a folyamatot.   
  * A rendszer hozzáadta a PipelineRun. Get _pipeline_runs () metódust a közzétett folyamatból generált folyamat-futtatások beolvasásához.

+ **Projekt agyhullám**
  * Frissített támogatás a FPGA-on elérhető új AI-modellekhez.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning adat-előkészítési SDK v 0.2.0
A [0.2.0 verziója](https://pypi.org/project/azureml-dataprep/0.2.0/) a következő funkciókat és hibajavításokat tartalmazza:

+ **Új funkciók**
  * Egy gyors kódolás támogatása
  * A quantile átalakítás támogatása
   
+ **Hiba javítva:**
  * Bármely Tornado-verzióval működik, nincs szükség a tornádó verziójának visszalépésére
  * Az értékek száma az összes értéknél, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Azure Machine Learning új, frissített kiadása: További információ erről a kiadásról: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Következő lépések

Olvassa el az [Azure Machine Learning](../service/overview-what-is-azure-ml.md) áttekintését.
