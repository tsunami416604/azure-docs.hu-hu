---
title: A kiadás újdonságai?
titleSuffix: Azure Machine Learning service
description: Ismerje meg a legújabb frissítéseket az Azure Machine Learning szolgáltatás és a machine learning és az adat-előkészítési Python SDK-k.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742762"
---
# <a name="azure-machine-learning-service-release-notes"></a>Az Azure Machine Learning szolgáltatás kibocsátási megjegyzései

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás kiadások. 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Az Azure Machine Learning SDK for Python v1.0.6

+ **SDK-referenciadokumentumok**: https://aka.ms/aml-sdk

+ **Hibajavítások**: Ez a kiadás többnyire tartalmaz kisebb hibajavítások

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Az Azure Machine Learning adat-előkészítési SDK v1.0.4

+ **SDK-referenciadokumentumok**: https://aka.ms/data-prep-sdk

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
  + [Mintafüzetek] (https://aka.ms/aml-notebooks) most frissülnek, hogy az új felügyelt számítási használja.
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
  * *azureml.core.compute.AmlCompute* a következő osztályok - helyteleníti *azureml.core.compute.BatchAICompute* és *azureml.core.compute.DSVMCompute*. Az utóbbi osztály törlődni fog a következő kiadásokban. A AmlCompute osztály mostantól könnyebben definícióval rendelkezik egyszerűen egy vm_size és a max_nodes van szüksége, és automatikus méretezése a max_nodes 0-tól a fürthöz, amikor a rendszer elküld egy feladatot. A [mintafüzetek] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lett-e ezzel az információval, és adjon meg használati példák. Reméljük, mint például az egyszerűsítés és a egy későbbi kiadásban lesz több izgalmas funkciók számos!

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

## <a name="older-notes-sept-2017---jun-2018"></a>Régebbi megjegyzések: Szeptembertől 2017 – 2018. június
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

Ebben a kiadásban az Azure Machine Learning segítségével:
+ Szabadkézi lemezképek ResNet-50, kvantált verziójával betanításához besorolás alapján ezeket a szolgáltatásokat, és [egy FPGA, az Azure-ban, hogy a modell rendszerbe állítása](../service/how-to-deploy-fpga-web-service.md) ultramagas közel valós idejű következtetési számára.

+ Gyorsan hozhat létre és rendkívül pontos gépi tanulási és deep learning-modellek használatával [egyéni Azure Machine Learning-csomagok](../desktop-workbench/reference-python-package-overview.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Verziószám**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Számos, a következő frissítéseket a visszajelzés közvetlenül eredményként történik. Újévi fogadalmunk!

**Jelentős új szolgáltatásaival és módosításaival**

- A szkriptek távoli Ubuntu virtuális gépeken futó natív módon a saját környezetében mellett távoli docker-támogatás a végrehajtás alapján.
- A Workbench alkalmazásban új környezet felület lehetővé teszi, hogy hozhat létre a számítási célokhoz, és futtassa a CLI-alapú élményen mellett konfigurációk.
![Környezetek lap](media/azure-machine-learning-release-notes/environment-page.png)
- Futtatási előzmények testre szabható jelentéseket ![korábbi jelentések új Futtatás képe](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Részletes frissítések**

Az alábbiakban olyan részletes frissítések az Azure Machine Learning a sprint a minden összetevő területen.

#### <a name="workbench-ui"></a>A Workbench felhasználói felület
- Futtatási előzmények testre szabható jelentéseket
  - Továbbfejlesztett diagram konfigurációját, a futtatási előzmények jelentésekhez
    - A használt belépési pontok is módosítható.
    - Legfelső szintű szűrők hozzáadható és módosított ![szűrők hozzáadása](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Diagramok és -statisztikák hozzáadnak vagy módosítanak (és fogd és vidd áthelyeztünk).
    ![Új diagram létrehozása](media/azure-machine-learning-release-notes/configure-charts.png)

  - Futtatási előzmények jelentések CRUD-MŰVELETEKKEL
  - Áthelyezett minden meglévő futtatási előzmények listanézetet konfigurációs fájlokat a kiszolgálóoldali jelentést, amely úgy működik, mint a kiválasztott bejegyzéshez hozzárendelni kívánt pontokról futtatások folyamatok.

- Környezetek lap
  - Egyszerűen adja hozzá az új számítási célnak, és futtassa a konfigurációs fájlokat a projekthez ![új számítási célt](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Felügyelje és frissítse a konfigurációs fájlokat, egy egyszerű, az űrlap-alapú felhasználói felület használatával
  - A végrehajtási környezet előkészítése az új gomb

- Teljesítménnyel kapcsolatos fejlesztések a listához, mivel az oldalsáv

#### <a name="data-preparation"></a>Adatok előkészítése 
- Az Azure Machine Learning Workbench most már lehetővé teszi, rákereshet egy oszlop használatával egy ismert oszlop neve.


#### <a name="experimentation"></a>Kísérletezés
- Az Azure Machine Learning Workbench most már támogatja a parancsprogramok natív módon fut a saját python- vagy pyspark környezetében. Ez a funkció a felhasználó hoz létre, és felügyeli a saját környezetet, a távoli virtuális gépen, és azok a parancsprogramok futtatásához az adott cél Azure Machine Learning Workbench használata. Lásd: [konfigurálása az Azure Machine Learning-kísérletezés szolgáltatás](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Modellkezelés
- Az üzembe helyezett tárolók testreszabása támogatása: lehetővé teszi, hogy a tároló rendszerképét testreszabása azáltal, hogy külső kódtárak használatával az apt-get paranccsal, és így tovább telepítési. Már nem korlátozott pip telepíthető könyvtárakhoz. Tekintse meg a [dokumentáció](../desktop-workbench/model-management-custom-container.md) további információ.
  - Használja a `--docker-file myDockerStepsFilename` jelzőt és a fájl nevét a jegyzékfájl, kép vagy szolgáltatás-létrehozási parancsokat.
  - Az alap rendszerképet Ubuntu, és nem módosítható.
  - A példában szereplő parancs: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01-es (sprint 3) 
**Verziószám**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

A frissítések és a sprint fejlesztései az alábbiak. A frissítések sok esetben jönnek létre, a felhasználói visszajelzések közvetlen következménye. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Verziószám**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ebben a kiadásban a harmadik frissítés az Azure Machine Learning. Ez a frissítés a workbench alkalmazás a parancssori felület (CLI) és a háttér-szolgáltatásaihoz tartalmaz fejlesztéseket. Köszönjük, hogy nagyon küldése a smiles és frowns. Számos, a következő frissítéseket a visszajelzés közvetlenül eredményként történik. 

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ebben a kiadásban biztonsági, stabilitás és a workbench alkalmazás, a parancssori felület és a háttér-szolgáltatásaikhoz réteg Karbantarthatóság kapcsolatos fejlesztéseket végeztünk. 

### <a name="2017-10-sprint-0"></a>2017-10-es (sprint 0) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ebben a kiadásban az az első frissítés az Azure Machine Learning Workbench a következő a kezdeti nyilvános előzetes verziója a Microsoft Ignite 2017 konferencián. Ebben a kiadásban a fő frissítések állnak, megbízhatóságát és stabilizált javítja. 

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](../service/overview-what-is-azure-ml.md) áttekintését.
