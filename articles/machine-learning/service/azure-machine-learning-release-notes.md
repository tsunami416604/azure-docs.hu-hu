---
title: A kiadás újdonságai?
titleSuffix: Azure Machine Learning service
description: Ismerje meg a legújabb frissítéseket az Azure Machine Learning szolgáltatás és a machine learning és az adat-előkészítési Python SDK-k.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 2/25/2019
ms.custom: seodec18
ms.openlocfilehash: d72676d7eaad539fc6e023bc96ccbb16f0958a7a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311372"
---
# <a name="azure-machine-learning-service-release-notes"></a>Az Azure Machine Learning szolgáltatás kibocsátási megjegyzései

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás kiadások.  Az SDK teljes leírását látogasson el a referenciadokumentumai:
+ Az Azure Machine Learning [ **fő SDK a Pythonhoz**](https://aka.ms/aml-sdk)
+ Az Azure Machine Learning [ **adat-előkészítési SDK**](https://aka.ms/data-prep-sdk)

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.16

+ **Hibajavítás**
  + Egy egyszerű szolgáltatást, amely okozta hitelesítési probléma kijavítva egy API-módosítás alapján.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Az Azure Machine Learning SDK for Python v1.0.17

+ **Új funkciók**

  + Az Azure Machine Learning SDK már támogatja a [ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) estimator osztály képzés és üzembe helyezéséhez Chainer egyéni kódot használó folyamatot.
  + Az Azure Machine Learning-folyamatokat Folyamatfuttatás adattárolója módosítások alapján lehetővé teszi az eseményindító hozzáadása. A folyamat [ütemezés notebook](https://aka.ms/pl-schedule) frissül, és ez a funkció bemutatására.
  
+ **Hibajavítások és kapcsolatos fejlesztések**
  + Hozzáadtuk a támogatást az Azure Machine Learning-folyamatokat a source_directory_data_store tulajdonság egy kívánt adattár (például egy blob storage) állítja a [RunConfigurations](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , amely megadott a [ PythonScriptStep](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Alapértelmezés szerint az lépéseket az Azure fájltároló használjuk a biztonsági adattár, amely megadtuk szabályozási problémák lépések nagy számú párhuzamosan végrehajtásakor.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
    + Új fogd és vidd tábla szerkesztő élmény a jelentések. Felhasználók oszlop húzhat a tábla területre, ahol a táblázat előnézetét megjelenik a jól. Az oszlopok alakítható át.
    + Új naplókat megjelenítő
    + Hivatkozások való kísérletezéshez fut, számítási, modelleket, lemezképek és a tevékenységek lapról központi telepítések

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.15

+ **Új funkciók**
  + Adat-előkészítési mostantól támogatja a írása egy adatfolyam érkező adatfolyamok fájlt. Emellett a fájlnevek stream hozhat létre új fájlnevek módosítására.
    + Útmutató: [Működik az fájl Streamek notebook](https://aka.ms/aml-data-prep-file-stream-nb)
 
+ **Hibajavítások és kapcsolatos fejlesztések**
  + A nagy méretű adatkészleteket a t-kivonatoló javított teljesítménye.
  + Data Prep mostantól támogatja a DataPath olvasási adatait.
  + Most már a logikai és a numerikus oszlopok egy gyakori kódolási működik.
  + Más egyéb hibajavításokat tartalmaz.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Az Azure Machine Learning SDK for Python v1.0.15

+ **Új funkciók**
  + Az Azure Machine Learning-folyamatokat hozzáadott AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep ([notebook](https://aka.ms/pl-hyperdrive)) és az időpont alapú ütemezés funkcióit ([notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep frissítve, hogy működik az Azure SQL Server és az Azure database for postgresql-hez ([notebook](https://aka.ms/pl-data-trans)).

+ **Módosítások**
  + Elavult `PublishedPipeline.get_published_pipeline` érvénytelenítve `PublishedPipeline.get`.
  + Elavult `Schedule.get_schedule` érvénytelenítve `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.12

+ **Új funkciók**
  + Data Prep mostantól támogatja egy Azure SQL database adattárolója használatával olvasásakor.
 
+ **Módosítások**
  + Nagy mennyiségű adat bizonyos műveletek teljesítményét memória jelentősen javult.
  + `read_pandas_dataframe()` Mostantól csak `temp_folder` adni.
  + A `name` tulajdonsága `ColumnProfile` lett használata elavult - `column_name` helyette.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Az Azure Machine Learning SDK for Python v1.0.10

+ **Módosítások**: 
  + Az Azure Machine Learning SDK már nem rendelkezik azure-cli csomag függőségként. Pontosabban az azure-cli-core és az azure-cli-profil függőségek azureml-core lettek távolítva. A felhasználót érintő változások a következők:
    + Ha "az login" végrehajtása, és ezután azureml-sdk-val, az SDK elvégzi a böngészőben vagy a kód eszközbejelentkezés még egyszer. "Az login" által létrehozott hitelesítő adatok munkalehetőségek nem használ.
    + Azure CLI-vel hitelesítést, például "az login"kifejezést, használja a _azureml.core.authentication.AzureCliAuthentication_ osztály. Az Azure CLI-hitelesítést, hajtsa végre _pip-telepítést az azure-cli_ az azureml-SDK-t telepítette a Python-környezetet.
    + Ha "az login" egyszerű szolgáltatás használatával az automation, azt javasoljuk, _azureml.core.authentication.ServicePrincipalAuthentication_ osztályt, azureml-sdk nem fogja használni az azure CLI által létrehozott hitelesítő adatok állapota. 

+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.8

+ **Hibajavítások**
  + Jelentősen javult az első adatok profilok teljesítményét.
  + Hiba történt a jelentéskészítéssel kapcsolatos rögzített kisebb hibák.
  
### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ Új áthúzása diagramkészítési élmény a jelentések. Felhasználók is húzza az oszlop vagy attribútum a jól a diagramterület, ahol a rendszer automatikusan kiválasztja a felhasználó az adatok típusa alapján a megfelelő diagramtípus. A felhasználók módosítsa a típusát, a többi alkalmazható vagy adjon hozzá további attribútumokat.

    Diagram típusokat támogatja:
    - Vonaldiagram
    - Hisztogram
    - Halmozott sáv diagram
    - Box-diagram
    - Pontdiagram
    - Buborék diagram
+ A portál dinamikusan hoz létre a kísérletek a jelentésekre. Amikor egy felhasználó beküld egy futtatásra annak kísérlet, egy jelentés automatikusan jön létre, a naplózott mérőszámok és gráfok különböző frissítési kísérletei összehasonlító engedélyezéséhez. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Az Azure Machine Learning SDK for Python v1.0.8

+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.7

+ **Új funkciók**
  + Adattároló fejlesztései (dokumentált [adattárolója útmutatóval-to-útmutató](https://aka.ms/aml-data-prep-datastore-nb))
    + Vertikális felskálázás az Azure-fájlmegosztás és ADLS-adattárainak írnak és onnan olvasnak új képessége.
    + Adattárolók használatakor Data Prep mostantól támogatja a egyszerű szolgáltatásnév hitelesítése interaktív hitelesítés helyett.
    + Támogatás hozzáadva a wasb és a wasbs URL-címeket.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.6

+ **Hibajavítások**
  + Nyilvános olvasható Azure Blob-tárolók Spark olvasásakor hiba javítva

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Az Azure Machine Learning SDK for Python v1.0.6
+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.4

+ **Új funkciók**
  + `to_bool` függvény mostantól lehetővé teszi, hogy a nem egyező értékek hibaértékek kell konvertálni. Ez lesz az új alapértelmezett eltérés viselkedés `to_bool` és `set_column_types`, mivel eltérő értékek konvertálása hamis volt az előző alapértelmezett viselkedést.
  + Hívásakor `to_pandas_dataframe`, egy új lehetőség a numerikus oszlopok null/hiányzó értékek értelmezése NaN.
  + Új képessége ellenőrzéséhez írja be a konzisztencia biztosítása, és sikertelen korai némely kifejezés visszatérési típusa.
  + Most már hívása `parse_json` elemezheti a JSON-objektumként egy oszlop értékét, és bontsa ki őket, több oszlopba.

+ **Hibajavítások**
  + Kijavítva a hiba, melyeken előfordult összeomlás `set_column_types` a Python 3.5.2-es verzióját.
  + Kijavítva a hiba, melyeken előfordult összeomlás, Datastore-AML-rendszerkép használatával való kapcsolódáskor.

+ **Frissítések**
  * [Példa notebookok](https://aka.ms/aml-data-prep-notebooks) az első lépések – oktatóanyagok, ügyféleset-tanulmányok és gyakorlati ismertetőket.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Általános rendelkezésre állás

Az Azure Machine Learning szolgáltatás már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Ebben a kiadásban bejelentjük, hogy egy új felügyelt számítási környezet révén a [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). A számítási célnak az Azure Machine Learning az Azure Batch AI-számítási váltja fel. 

A számítási célnak:
+ Képzés és a batch következtetési modell használható
+ Van egy - a több - node számítási
+ A fürt felügyeleti does és a felhasználó feladatütemezésre
+ Alapértelmezés szerint automatikusan Leskálázódik
+ Processzor-és GPU támogatása 
+ Alacsonyabb költségek mellett lehetővé teszi, hogy alacsony prioritású virtuális gépek használata

Az Azure Machine Learning Compute a Python, az Azure portal vagy a parancssori felület használatával hozható létre. A munkaterület a régióban kell létrehozni, és bármilyen egyéb munkaterülethez nem csatolható. A számítási célnak egy Docker-tárolót használ a futtatáskor, és a függőségek ugyanabban a környezetben replikálni az összes csomópont-csomagot.

> [!Warning]
> Azt javasoljuk, hogy használata az Azure Machine Learning Compute új munkaterület létrehozása. Nincs távoli előfordulhat, hogy a felhasználók próbál létrehozni egy meglévő munkaterületet az Azure Machine Learning COMPUTE számítási hiba láthatják. A munkaterületen található meglévő számítási továbbra is változatlanul működik.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Az Azure Machine Learning SDK for Python v1.0.2
+ **Használhatatlanná tévő változásai**
  + Ettől a kiadástól kezdve megszüntetjük a virtuális gép létrehozása az Azure Machine Learning támogatása. Továbbra is csatolhat a virtuális gép meglévő felhő vagy egy távoli helyszíni kiszolgáló. 
  + Emellett megszüntetjük BatchAI, mindegyike támogatnia kell az Azure Machine Learning Compute révén most támogatása.

+ **Új**
  + A machine learning-folyamatokat:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissítve**
  + A machine learning-folyamatokat:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) már elfogad runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) most másolja és a egy SQL-adatforrás
    + Az SDK-t létrehozni és frissíteni a közzétett folyamatok futó ütemezések funkció ütemezéséhez

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.2
+ **Használhatatlanná tévő változásai** 
  * `SummaryFunction.N` új nevet kapott `SummaryFunction.Count`.
  
+ **Hibajavítások**
  * Legújabb AML futtatása jogkivonat használata a távoli Futtatás adattárainak való írásakor vagy olvasásakor a. Korábban az AML futtatása Token frissül a Python, ha az Adatelőkészítés-modul nem frissül a frissített AML futtatása jogkivonat a.
  * További világosabb hibaüzenetek
  * to_spark_dataframe() már nem összeomlik, ha Spark használ `Kryo` szerializáció
  * Értékek száma vizsgáló most már 1000-nél több egyedi értékeket jeleníti meg
  * Véletlenszerű Split már nem sikertelen lesz, ha az eredeti adatfolyam nem rendelkezik egy nevet  

+ **További információ**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs és jegyzetfüzetek
+ Gépi Tanulási folyamatok
  + Ismerkedés a folyamatok, kötegelt hatókörének és stílus az új és frissített notebookok példák át: https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md)
  + Ismerje meg, hogyan [batch előrejelzéseket folyamatok használatával futtassa](how-to-run-batch-predictions.md)
+ Az Azure Machine Learning számítási célnak
  + [Notebookok minta](https://aka.ms/aml-notebooks) most frissülnek, hogy az új felügyelt számítási használja.
  + [Ismerje meg a számítási](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ Létrehozásához és kezeléséhez [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) típusok a portálon.
+ Fiókkvóta-használat figyeléséhez és [kérelmi kvótát](how-to-manage-quotas.md) az Azure Machine Learning Compute.
+ Az Azure Machine Learning COMPUTE számítási fürt állapotának megtekintése valós időben.
+ Virtuálishálózat-támogatást az Azure Machine Learning COMPUTE számítási és az Azure Kubernetes Service létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatok meglévő paraméterekkel.
+ Új [machine learning diagramok automatikus](how-to-track-experiments.md#auto) képbesorolási modellek (lift, nyereség, hitelesítési, funkció fontosság diagramot modell explainability) és regressziós modellek (például és a szolgáltatás fontosság diagram modellel explainability). 
+ A folyamatok tekinthetnek meg az Azure Portalon




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Az Azure Machine Learning SDK for Python v0.1.80

+ **Használhatatlanná tévő változásai** 
  * *azureml.train.widgets* névtér át lett helyezve *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* a következő osztályok - helyteleníti *azureml.core.compute.BatchAICompute* és *azureml.core.compute.DSVMCompute*. Az utóbbi osztály törlődni fog a következő kiadásokban. A AmlCompute osztály mostantól könnyebben definícióval rendelkezik egyszerűen egy vm_size és a max_nodes van szüksége, és automatikus méretezése a max_nodes 0-tól a fürthöz, amikor a rendszer elküld egy feladatot. A [notebookok minta](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lett-e ezzel az információval, és adjon meg használati példák. Reméljük, mint például az egyszerűsítés és a egy későbbi kiadásban lesz több izgalmas funkciók számos!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.1 

További információ a Data Prep SDK olvasásával [docs hivatkozhat](https://aka.ms/data-prep-sdk).
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI hajtsa végre az Adatelőkészítés-csomagok és az adatkészlet vagy adatfolyam adatok profiljának megtekintése
   * Újratervezett SetColumnType API használhatóság javítása érdekében
   * Átnevezett smart_read_file auto_read_file
   * Mostantól tartalmazza a Adatprofil döntés és értékek
   * A rétegzett mintavételi is minta
   * CSV-fájlokat tartalmazó zip-fájlokat is olvashat
   * Is felosztása row-wise véletlenszerű Split-adatkészletek (például tesztelési – train-készlet)
   * Az összes oszlop adattípusok vagy érheti el egy adatfolyam-adatprofil meghívásával `.dtypes`
   * A sorok számát vagy érheti el egy adatfolyam-adatprofil meghívásával `.row_count`

+ **Hibajavítások**
   * Mennyi ideig dupla átalakítás rögzített 
   * Rögzített vyhodnocení után bármely oszlop hozzáadása 
   * FuzzyGrouping, ahol azt szeretné nem csoportok egyes esetekben képes észlelni kijavítva
   * Több oszlopot rendezési sorrend figyelembe veszi a rögzített rendezési funkció
   * Rögzített, illetve hogyan hasonlónak kell lennie a kifejezések `pandas` kezeli őket
   * Rögzített olvasásakor dbfs elérési útja
   * Hibaüzenetek a könnyebb érthetőség érdekében 
   * Most már nem akkor, ha az AML-jogkivonat használatával távoli számítási cél olvasó
   * Most már nem meghiúsul, a Linux-dsvm-hez
   * Most már nem összeomlik, ha nem sztring típusú értékek a következők az olyan predikátumokban karakterlánc
   * Most már kezeli a helyességi feltétel hibákat, amikor adatfolyamot megfelelően kell-e sikertelen
   * Most már támogatja az Azure databricks szolgáltatásban dbutils csatlakoztatott tárolási helyek

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
Az Azure Portalon az Azure Machine Learning szolgáltatás rendelkezik a következő frissítéseket:
  * Egy új **folyamatok** lapján közzétett folyamatokhoz.
  * Támogatás hozzáadva a csatolása egy meglévő HDInsight-fürt egy számítási célnak.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Az Azure Machine Learning SDK for Python v0.1.74

+ **Használhatatlanná tévő változásai** 
  * * Workspace.compute_targets, adattárolók, kísérletek, képek, modellek és *problémák megoldásához segítséget* tulajdonságok módszer helyett. Helyettesítse be például *Workspace.compute_targets()* a *Workspace.compute_targets*.
  * *Run.get_context* helyteleníti *Run.get_submitted_run*. Az utóbbi módszer a következő kiadásokban törlődni fog.
  * *PipelineData* osztály most vár egy adattár-objektumot datastore_name egy paraméter helyett. Ehhez hasonlóan *folyamat* default_datastore_name helyett default_datastore fogad el.

+ **Új funkciók**
  * Az Azure Machine Learning-folyamatokat [minta notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) MPI lépéseket használja.
  * A Jupyter-notebookokhoz RunDetails widget megjelenítéséhez a vizualizációt, a folyamat frissül.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Az Azure Machine Learning adat-előkészítési SDK v0.4.0 
 
+ **Új funkciók**
  * Adatprofil hozzáadott száma 
  * Értékek száma és a hisztogram már elérhető
  * További. percentilisei az Adatprofil
  * A középérték érhető el összegzés
  * Mostantól támogatott Python 3.7.
  * Amikor menti egy adatfolyam, amely tartalmazza az adattárolók Adatelőkészítés csomaghoz, az adattár-információk maradnak az Adatelőkészítés-csomag részeként
  * Mostantól támogatott az adattárhoz való írás 
        
+ **Hiba kijavítva**
  * 64 bites, előjel nélküli egész túlcsordul most kezeli megfelelően Linux rendszeren
  * Egyszerű szöveges fájlok smart_read rögzített helytelen felirat
  * Karakterlánc oszloptípus most már megjelenik-e metrikák megtekintése
  * Száma már rögzített egyesével helyett egyetlen FieldType leképezve ValueKinds megjelenítése
  * Write_to_csv már nem sikertelen lesz, amikor az elérési út egy karakterláncként van megadva.
  * Cserélje le használatakor "keresése" üresen hagyja már nem meghiúsul 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Az Azure Machine Learning SDK for Python v0.1.68

+ **Új funkciók**
  * Több-bérlős támogatással új munkaterület létrehozása során.

+ **Javított hibák**
  * Már nincs szüksége a pynacl könyvtár verzió rögzítése a webszolgáltatás üzembe helyezésekor.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Az Azure Machine Learning adat-előkészítési SDK v0.3.0

+ **Új funkciók**
  * Hozzáadott metódus transform_partition_with_file(script_path), amely lehetővé teszi a felhasználók számára, hogy az elérési útját egy Python-fájlt, végrehajtására

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Az Azure Machine Learning SDK for Python v0.1.65
[Verzió 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) új funkciók, további, hibajavításokat tartalmaz, és tartalmaz további [notebookok minta](https://aka.ms/aml-notebooks).

Lásd: [kapcsolatos ismert problémák listája](resource-known-issues.md) ismert hibák és a lehetséges megoldások megismeréséhez.

+ **Használhatatlanná tévő változásai**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services visszatérési szótár, korábban visszaadott listában. Lásd: [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API dokumentációja.

  * Automatizált Machine Learning Normalizált négyzetes hiba eltávolítja az elsődleges metrikákat.

+ **HyperDrive**
  * Különböző HyperDrive hibajavítások a Bayes, a javított teljesítménye, metrikák hívások beolvasása. 
  * 1.9 Tensorflow 1.10 verzióról 
  * Docker-rendszerkép optimalizálást a hidegindítás. 
  * Feladatok megfelelő állapota most jelentést, még akkor is, ha azok kilépési hibakód: 0 kivételével. 
  * RunConfig attribútum érvényesítése, az SDK-t. 
  * Futtatás HyperDrive objektum Futtatás szabályos hasonló Mégse támogatja: nincs szükség adja át a paramétereket. 
  * Widget fejlesztései legördülő értékeit az elosztott futtatási és HyperDrive futtatások állapotának fenntartásához. 
  * Paraméterkiszolgáló TensorBoard és a többi napló-fájloknak támogatniuk rögzíteni. 
  * Intel(R) MPI szolgáltatás oldalán támogatja. 
  * Bugfix a finomhangolással az elosztott futtatási javítás BatchAI az érvényesítés során. 
  * Környezet Manager mostantól azonosítja az elsődleges példány. 

+ **Azure portal felületének**
  * Futtatás részletei log_table() és log_row() támogatottak. 
  * 1, 2 vagy 3 numerikus oszlopok és a egy nem kötelező kategorikus oszlop hozza létre automatikusan a táblák és sorok diagramjait.

+ **Automatizált Machine Learning**
  * Továbbfejlesztett hiba- és dokumentáció 
  * Rögzített futtatási tulajdonság lekérése teljesítménybeli problémák. 
  * Rögzített futtatási a probléma továbbra is. 
  * Rögzített ensembling iteráció problémákat.
  * Rögzített méretű betanítási módosítási hiba a MAC OS.
  * Egyszerűsítés makró átlagos Pull-kérelem/ROC-görbe egyéni ellenőrzési forgatókönyvben.
  * Eltávolítja a felesleges index logikát.
  * Szűrő távolítva get_output API-t.

+ **Folyamatok**
  * A metódus egy folyamatot, közvetlenül közzé anélkül, hogy az első futtatás végrehajtási Pipeline.publish() hozzá.   
  * A folyamatfuttatások egy közzétett folyamatot létrehozott beolvasni PipelineRun.get_pipeline_runs() metódus hozzá.

+ **Project Brainwave**
  * FPGA-kban elérhető új AI modelleket frissített támogatása.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Az Azure Machine Learning adat-előkészítési SDK v0.2.0
[Verzió 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) alábbi funkcióit és hibajavításait tartalmazza:

+ **Új funkciók**
  * Egy gyakori kódolási támogatása
  * Ki osztóérték átalakító támogatása
   
+ **Hiba kijavítva:**
  * Bármely tornádó verziójával működik nincs szükség alacsonyabb szolgáltatásszintre váltásához az tornádó verzió
  * Értékek száma az összes értéket, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Egy új, frissített verzióját az Azure Machine Learning: További információ az ebben a kiadásban: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>További lépések

Olvassa el a áttekintése [Azure Machine Learning szolgáltatás](../service/overview-what-is-azure-ml.md).
