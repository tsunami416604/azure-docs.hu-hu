---
title: A kiadás újdonságai?
titleSuffix: Azure Machine Learning service
description: Ismerje meg az Azure Machine Learning szolgáltatást a legújabb frissítéseket.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 73f4aeb77124c21a07771ab080b88a56231e50da
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185715"
---
# <a name="azure-machine-learning-service-release-notes"></a>Az Azure Machine Learning szolgáltatás kibocsátási megjegyzései

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás kiadások. 

## <a name="2018-12-04-general-availability"></a>2018-12-04: Általános rendelkezésre állás

Az Azure Machine Learning szolgáltatás már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Ebben a kiadásban bejelentjük, hogy egy új felügyelt számítási élményt [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). A számítási képzés és a Batch következtetési is használható, egy - a több - node számítási, és a fürt felügyeleti does és a felhasználó feladatütemezésre. Ez alapértelmezés szerint automatikusan leskálázódik támogatja a Processzor-és GPU, és alacsony prioritású virtuális gépek használatával alacsonyabb költségek mellett lehetővé teszi. Az Azure Machine Learning Batch AI-számítási lecseréli azt.
  
Az Azure Machine Learning Compute a Python, az Azure portal vagy a parancssori felület használatával hozható létre. A munkaterület a régióban kell létrehozni, és bármilyen egyéb munkaterülethez nem csatolható. A számítási egy Docker-tárolót használ a futtatáskor, és a függőségek ugyanabban a környezetben replikálni az összes csomópont-csomagot.

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
  * Amikor Spark használ Kryo szerializálási to_spark_dataframe() már nem összeomlik.
  * Értékek száma vizsgáló most már 1000-nél több egyedi értékeket jeleníti meg
  * Véletlenszerű Split már nem sikertelen lesz, ha az eredeti adatfolyam nem rendelkezik egy nevet  

### <a name="docs-and-notebooks"></a>Docs és jegyzetfüzetek
+ Gépi Tanulási folyamatok
  + Ismerkedés a folyamatok, kötegelt hatókörének és stílus az új és frissített notebookok példák át: https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md)
  + Ismerje meg, hogyan [batch előrejelzéseket folyamatok használatával futtassa](how-to-run-batch-predictions.md)
+ Az Azure Machine Learning compute
  + [Mintafüzetek] (https://aka.ms/aml-notebooks) most frissülnek, hogy az új felügyelt számítási használja.
  + [Ismerje meg a számítási](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ Létrehozásához és kezeléséhez [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) típusok a portálon.
+ Fiókkvóta-használat figyeléséhez és [kérelmi kvótát](how-to-manage-quotas.md) az Azure Machine Learning Compute.
+ Valós idejű Azure Machine Learning COMPUTE számítási fürt állapotának megtekintése.
+ Virtuálishálózat-támogatást az Azure Machine Learning COMPUTE számítási és az Azure Kubernetes Service létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatok meglévő paraméterekkel.
+ Új [machine learning diagramok automatikus](how-to-track-experiments.md#auto) képbesorolási modellek (lift, nyereség, hitelesítési, funkció fontosság diagramot modell explainability) és regressziós modellek (például és a szolgáltatás fontosság diagram modellel explainability). 
+ A folyamatok tekinthetnek meg az Azure Portalon




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Az Azure Machine Learning SDK for Python v0.1.80

+ **Használhatatlanná tévő változásai** 
  * *azureml.train.widgets* névtér át lett helyezve *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* a következő osztályok - helyteleníti *azureml.core.compute.BatchAICompute* és *azureml.core.compute.DSVMCompute*. Az utóbbi osztály törlődni fog a következő kiadásokban. A AmlCompute osztály mostantól könnyebben definícióval rendelkezik egyszerűen egy vm_size és a max_nodes van szüksége, és automatikus méretezése a max_nodes 0-tól a fürthöz, amikor a rendszer elküld egy feladatot. A [mintafüzetek] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lett-e ezzel az információval, és kell példákkal szemlélteti, hogyan használható ez a. Reméljük, mint például az egyszerűsítés és a egy későbbi kiadásban lesz több izgalmas funkciók számos!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.1 

További információ a Data Prep SDK olvasásával [docs hivatkozhat](https://aka.ms/data-prep-sdk).
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI hajtsa végre az Adatelőkészítés-csomagok és az adatkészlet vagy adatfolyam adatok profiljának megtekintése
   * Újratervezett SetColumnType API használhatóság javítása érdekében
   * Átnevezett smart_read_file auto_read_file
   * Mostantól tartalmazza a Adatprofil döntés és értékek
   * A rétegzett mintavételi is minta
   * CSV-fájlokat tartalmazó zip-fájlokat is olvashat
   * Is felosztása row-wise véletlenszerű Split-adatkészletek (pl. teszt – train-készlet)
   * Az összes oszlop adattípusok vagy érheti el egy adatfolyam-adatprofil .dtypes meghívásával
   * A sorok számát vagy érheti el egy adatfolyam-adatprofil .row_count meghívásával

+ **Hibajavítások**
   * Mennyi ideig dupla átalakítás rögzített 
   * Rögzített vyhodnocení után bármely oszlop hozzáadása 
   * FuzzyGrouping, ahol azt szeretné nem csoportok egyes esetekben képes észlelni kijavítva
   * Több oszlopot rendezési sorrend figyelembe veszi a rögzített rendezési funkció
   * Rögzített méretű és/vagy kifejezések hogyan Pandas kezeli őket hasonlónak kell lennie
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
  * *Workspace.compute_targets, adattárolók, kísérletek, képek, modellek* és *problémák megoldásához segítséget* tulajdonságok módszer helyett. Helyettesítse be például *Workspace.compute_targets()* a *Workspace.compute_targets*.
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
  * 64 bites, előjel nélküli egész számokat túlcsordul most kezeli megfelelően Linux rendszeren
  * Egyszerű szöveges fájlok smart_read rögzített helytelen felirat
  * Karakterlánc oszloptípus most már megjelenik-e metrikák megtekintése
  * Száma már rögzített egyesével helyett egyetlen FieldType leképezve ValueKinds megjelenítése
  * Write_to_csv már nem sikertelen lesz, amikor az elérési út egy karakterláncként van megadva.
  * Cserélje le használatakor "keresése" üresen hagyja már nem meghiúsul 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Az Azure Machine Learning SDK for Python v0.1.68

+ **Új funkciók**
  * Több bérlős támogatással új munkaterület létrehozása során.

+ **Javított hibák**
  * A pynacl könyvtár verzió már nem rögzíthetők a webszolgáltatáshoz való telepítésekor.

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
  * Feladat a most jelentés megfelelő akkor is, ha a hiba kilépnek-as állapotkódot más, mint 0. 
  * RunConfig attribútum érvényesítése, az SDK-t. 
  * Futtatás HyperDrive objektum Futtatás szabályos hasonló Mégse támogatja: nincs szükség adja át a paramétereket. 
  * Widget fejlesztései legördülő értékeit az elosztott futtatási és HyperDrive futtatások állapotának fenntartásához. 
  * Paraméterkiszolgáló TensorBoard és a többi napló-fájloknak támogatniuk rögzíteni. 
  * Intel(R) MPI szolgáltatás oldalán támogatja. 
  * Bugfix a finomhangolással az elosztott futtatási javítás BatchAI az érvényesítés során. 
  * Környezet Manager mostantól azonosítja az elsődleges példány. 

+ **Azure portal felületének**
  * Futtatás részletei log_table() és log_row() támogatottak. 
  * Automatikusan hozzon létre diagramokat a táblák és sorok 1,2 vagy 3 numerikus oszlopok és a egy választható kategorikus oszlop.

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
  * Hozzáadott metódus PipelineRun.get_pipeline_runs() beolvasni a folyamat fut, amely közzétett folyamat jött létre.

+ **Project Brainwave**
  * FPGA-kban elérhető új AI modelleket frissített támogatása.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Az Azure Machine Learning adat-előkészítési SDK v0.2.0
[Verzió 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) tartalmazza a következő szolgáltatásokat és bugfixes:

+ **Új funkciók**
  * Egy gyakori kódolási támogatása
  * Ki osztóérték átalakító támogatása
   
+ **Hiba kijavítva:**
  * Bármely tornádó verziójával működik nincs szükség alacsonyabb szolgáltatásszintre váltásához az tornádó verzió
  * Értékek száma az összes értéket, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Egy új, teljes mértékben frissítés az Azure Machine Learning a kiadásban: További információ az ebben a kiadásban: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Régebbi megjegyzések: Szeptembertől 2017 – 2018. június
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

Ebben a kiadásban az Azure Machine Learning segítségével:
+ Szabadkézi lemezképek ResNet-50, kvantált verziójával betanításához besorolás alapján ezeket a szolgáltatásokat, és [egy FPGA, az Azure-ban, hogy a modell rendszerbe állítása](../service/how-to-deploy-fpga-web-service.md) ultramagas közel valós idejű következtetési számára.

+ Gyorsan hozhat létre és rendkívül pontos gépi tanulási és deep learning-modellek használatával [egyéni Azure Machine Learning-csomagok](../desktop-workbench/reference-python-package-overview.md) a következő tartományok:
  + [Számítógépes látástechnológia](../desktop-workbench/how-to-build-deploy-image-classification-models.md)
  + [Szövegelemzés](../desktop-workbench/how-to-build-deploy-text-classification-models.md)
  + [Előrejelzések](../desktop-workbench/how-to-build-deploy-forecast-models.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Verziószám**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Számos, a következő frissítéseket a visszajelzés közvetlenül eredményként történik. Adjon Újévi fogadalmunk!

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
    - A használt entrypoints is módosítható.
    - Legfelső szintű szűrők hozzáadható és módosított ![szűrők hozzáadása](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Diagramok és -statisztikák hozzáadnak vagy módosítanak (és fogd és vidd áthelyeztünk).
    ![Új diagram létrehozása](media/azure-machine-learning-release-notes/configure-charts.png)

  - Futtatási előzmények jelentések CRUD-MŰVELETEKKEL
  - Az összes meglévő futtatási előzmények listanézetet configs kiszolgálóoldali jelentésekhez, amely úgy működik, mint a folyamatok fut a kiválasztott belépési pontok áthelyezni.

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
  - Vegye figyelembe, hogy az alaplemezkép Ubuntu-e, és nem módosítható.
  - A példában szereplő parancs: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



### <a name="2018-01-sprint-3"></a>2018-01-es (sprint 3) 
**Verziószám**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

A frissítések és a sprint fejlesztései az alábbiak. A frissítések sok esetben jönnek létre, a felhasználói visszajelzések közvetlen következménye. 


Az alábbiakban olyan részletes frissítések az Azure Machine Learning a sprint a minden összetevő területen.

- A hitelesítési stack frissítéseinek arra kényszeríti az indításkor bejelentkezési és a fiók kiválasztása

#### <a name="workbench"></a>Workbench
- Lehetővé teszi az alkalmazást a Programok telepítése/eltávolítása
- A hitelesítési stack frissítéseinek arra kényszeríti az indításkor bejelentkezési és a fiók kiválasztása
- A Windows továbbfejlesztett egyszeri bejelentkezés (SSO)
- Felhasználók tartoznak több bérlő más hitelesítő adatokkal jelentkezhetnek be a Workbench lesz

#### <a name="ui"></a>FELHASZNÁLÓI FELÜLET
- Általános fejlesztések és hibajavítások

#### <a name="notebooks"></a>Notebookok
- Általános fejlesztések és hibajavítások

#### <a name="data-preparation"></a>Adatok előkészítése 
- Továbbfejlesztett automatikus javaslatok példa alapján átalakítást végrehajtása közben
- Továbbfejlesztett algoritmus minta gyakorisága vizsgáló
- Képes titkosítottan küldeni a mintaadatok és visszajelzés példa alapján átalakítást végrehajtása közben ![küldési visszajelzési hivatkozás a képe származtatott oszlop átalakító](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Spark-modul fejlesztései
- Scala Pyspark váltja fel
- A Time Series vizsgáló az adatok nem alkalmazható bezárásához rögzített meggátoló 
- A leállás ideje a HDI Adatelőkészítés-végrehajtási kijavítva

#### <a name="model-management-cli-updates"></a>Modell felügyeleti parancssori felület frissítése 
  - Az előfizetés tulajdonjogának már nem szükséges erőforrások kiépítése. Közreműködői hozzáférés az erőforráscsoporthoz az üzembe helyezési környezetet elegendő lesz.
  - Az ingyenes előfizetések beállítása engedélyezett helyi környezetben 

### <a name="2017-12-sprint-2-qfe"></a>2017-12 (sprint 2 QFE) 
**Verziószám**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ez az a QFE (gyorsjavítás mérnöki) kiadott, egy kisebb kiadás. Ez számos telemetriát problémákat, és segít a termékcsoport segít jobban megérteni a termék használatának. A Tudásbázis meg jövőbeli erőfeszítések jobb felhasználói élmény termék be. 

Emellett van két fontos frissítések:

- Kijavítva a hiba a az adatelőkészítés, amely ebben az esetben a time series vizsgáló fognak megjelenni az adatelőkészítési csomagok.
- A parancssori eszköz már nincs szüksége a Machine Learning Compute ACS-fürtök kiépítése az Azure-előfizetés tulajdonosa legyen. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Verziószám**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Üdvözli az Azure Machine Learning a harmadik frissítést. Ez a frissítés a workbench alkalmazás a parancssori felület (CLI) és a háttér-szolgáltatásaihoz tartalmaz fejlesztéseket. Köszönjük, hogy nagyon küldése a smiles és frowns. Számos, a következő frissítéseket a visszajelzés közvetlenül eredményként történik. 

**Jelentős új funkciók**
- [Az SQL Server és az Azure SQL DB adatforrásként támogatása](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 
- [A Spark használatával MMLSpark GPU-támogatással rendelkező deep Learning](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Az összes AML-tároló kompatibilisek az Azure IoT Edge-eszközök (nincs szükség további lépésekre) telepítésekor](https://aka.ms/aml-iot-edge-blog)
- Regisztrált modell-lista és a részletek tekint érhető el az Azure Portalon
- Elérése SSH-kulcs alapú felül felhasználónév/jelszó-alapú hitelesítést használ a számítási célokhoz. 
- Az adatok új minta gyakorisága vizsgáló előkészítési felhasználói élményt. 

**Frissítések részletes** részletes frissítések minden egyes összetevő terület, az Azure Machine Learning a sprint a listában.

#### <a name="installer"></a>Telepítő
- Telepítő önkiszolgáló frissítés is, így hibák elhárítása, amelyek javításokat és új funkciók is támogatott, újratelepítése, felhasználó nélküli

#### <a name="workbench-authentication"></a>Workbench-hitelesítés
- Több hitelesítési rendszere javításait tartalmazza. Tudassa velünk, ha nem szűnik meg a bejelentkezési problémák.
- Felhasználói felület módosításokat, hogy egyszerűbben Proxykezelő beállítások megkereséséhez.

#### <a name="workbench"></a>Workbench
- Fájl csak olvasható nézet most már rendelkezik a kis kék háttér
- Áthelyezett a Szerkesztés gombra a jobb oldalon, hogy könnyebben felfedezhetővé teheti.
- "dsource", "dprep" és "ipynb" fájlformátumok most megjeleníthetők a nyers szöveg formátumban
- A workbench most már rendelkezik egy új szerkesztési funkciót, amely végigvezeti a felhasználókat felé külső Ide-k használatával a szkriptek szerkesztése és a Workbench használata csak olyan fájltípus esetében, amelyek egy gazdag szerkesztési funkciót (például adatforrások, adatelőkészítési csomagok notebookok) szerkesztése
- Betölti az munkaterületeket és projekteket, amely hozzáfér a felhasználó már jelentősen gyorsabb

#### <a name="data-preparation"></a>Adatok előkészítése 
- A minta gyakorisága vizsgáló minták megtekintése a sztring egy adott oszlopban. Ezek a minták használata esetén az adatok szűrheti is. Ez jeleníti meg meg az értékek száma vizsgáló hasonló. A különbség az, hogy a minta gyakorisága az adatok egyedi mintákat, nem pedig az érintett egyedi adatok számát jeleníti meg. És minden sor egy bizonyos minta igazodó leskálázása is szűrheti.

![Minta gyakorisága vizsgáló a termékszám képe](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Teljesítménnyel kapcsolatos fejlesztések, tekintse át az "oszlopok származtatása példa" átalakításában szerepel a szélsőséges esetek ajánlása közben

- [Az SQL Server és az Azure SQL DB adatforrásként támogatása](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 

![Egy új SQL server-adatforrás létrehozásának képe](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- "Egyetlen pillantással" nézete fejlécsorának és oszlopszámának engedélyezve

![Sor oszlopszám: egy türelmi képe](media/azure-machine-learning-release-notes/row-col-count.png)

- Adatelőkészítés engedélyezve van az összes számítási környezetek
- SQL Server-adatbázist használó adatforrások engedélyezve vannak az összes számítási környezetek
- Adat-előkészítési rács oszlopok adattípus szerint szűrhetők
- Kijavítva a hiba több oszlop alakításával a mai napig
- Kijavítva a hiba, hogy a felhasználó kiválaszthatunk kimeneti oszlop származtatott oszlop szerint példában forrásként, ha a felhasználó módosította a speciális módban kimeneti oszlop neve.

#### <a name="job-execution"></a>Feladat végrehajtása
Mostantól létrehozhat és hozzáférési remotedocker vagy -fürt típusú számítási célt következő lépések az SSH-alapú hitelesítés használatával:
- A CLI az alábbi paranccsal számítási célt csatolása

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (vagy---azureml-ssh-kulcs használata) lehetőséget, a parancsban Megadja, hogy hozhat létre és használhat SSH-kulcsot.

- Az Azure ML Workbench hozzon létre egy nyilvános kulcsot, és a kimeneti, amely a konzolon. Jelentkezzen be a számítási célt a felhasználónevet, és a nyilvános kulcs ~/.ssh/authorized_keys fájl hozzáfűzése.

- Készítse elő a számítási célnak, és használhatja a végrehajtáshoz, és az Azure ML Workbench ezt a kulcsot fogja használni a hitelesítéshez.  

Számítási célnak létrehozásával kapcsolatos további információkért lásd: [konfigurálása az Azure Machine Learning-kísérletezés szolgáltatás](../desktop-workbench/experimentation-service-configuration.md)

#### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- Támogatás hozzáadva az [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)
- Hozzáadott `az ml datasource create` parancs engedélyezi a adatforrásfájl létrehozása a parancssorból

#### <a name="model-management-and-operationalization"></a>Modellkezelési és Operacionalizálás
- [Az összes AML-tároló kompatibilisek az Azure IoT Edge-eszközökön, ha üzembe helyezte azt (nincs szükség további lépésekre)](https://aka.ms/aml-iot-edge-blog) 
- A o16n parancssori felületen hibaüzenetek fejlesztései
- A modell felügyeleti portál UX hibajavítások  
- Konzisztens betűvel kis-és a részletek lapon modell felügyeleti attribútumok
- Valós idejű pontozási hívások időkorlátja 60 másodperc értékre
- Regisztrált modell listája és a részletes nézet érhető el az Azure Portalon

![a portál modell részletei](media/azure-machine-learning-release-notes/model-list.jpg)

![a portál modell áttekintése](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

#### <a name="mmlspark"></a>MMLSpark
- Spark és a mélytanulási [GPU-támogatással](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Resource Manager-sablonokkal könnyedén erőforrások üzembe helyezésének támogatása
- A SparklyR ökoszisztéma támogatása
- [AZTK integráció](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

#### <a name="sample-projects"></a>Minta-projektek
- [IRIS](https://github.com/Azure/MachineLearningSamples-Iris) és [MMLSpark](https://github.com/Azure/mmlspark) minták az új Azure ML-SDK-verzió frissítése

#### <a name="breaking-changes"></a>Kompatibilitástörő változások
- Előléptetett a `--type` kapcsoló a `az ml computetarget attach` az egy alárendelt parancs. 

    - `az ml computetarget attach --type remotedocker` most `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` most `az ml computetarget attach cluster`

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ebben a kiadásban biztonsági, stabilitás és a workbench alkalmazás, a parancssori felület és a háttér-szolgáltatásaikhoz réteg Karbantarthatóság kapcsolatos fejlesztéseket végeztünk. Köszönjük, hogy az igazán elküldi nekünk smiles és frowns. Számos, a frissítések alatt jönnek létre a visszajelzés közvetlenül eredményként. Újévi fogadalmunk!

#### <a name="notable-new-features"></a>Jelentős új funkciók
- Az Azure gépi tanulás már elérhető két új Azure-régióban: **Nyugat-Európa** és **Délkelet-Ázsia**. Az előző régiói csatlakoznak **USA keleti RÉGIÓJA 2**, **USA nyugati középső Régiója**, és **Kelet-Ausztrália**, régiók száma összesen és üzembe helyezett öt.
- Lehetővé tettük a Python kódját szintaxiskiemelést könnyebb elolvashassák és szerkeszthessék a Python-forráskódja a Workbench alkalmazásban. 
- Most már el is indíthatja a kedvenc ide Környezetet közvetlenül a fájl helyett a teljes projektből.  A fájl megnyitása a Workbench, végül kattintson a "Szerkesztés" az aktuális fájl és a projekt elindítja az IDE (jelenleg a VS Code és a PyCharm támogatottak).  A Workbench szövegszerkesztőben a fájl szerkesztése a Szerkesztés gomb melletti nyílra is kattinthat.  Fájlok írásvédettek, amíg nem kattint a Szerkesztés, véletlen módosításának megakadályozása.
- A népszerű ábrázolási library `matplotlib` 2.1.0-ás mostantól tartalmazza a szükséges, a Workbench alkalmazás.
- 2.0-t az adat-előkészítő motor tudjuk frissíteni a .NET Core-verzió. Ez eltávolítja a követelmény openssl brew-telepítés során az alkalmazás telepítése macOS rendszeren. Azt is előkészíti az adatok több izgalmas előkészítő szolgáltatások származnak, a közeljövőben. 
- Engedélyeztük verzióspecifikus alkalmazás kezdőlapja, így relevánsabb kibocsátási megjegyzések lekérése, és frissítse a jelenlegi Alkalmazásverzió alapján utasításokat.
- Ha a helyi felhasználó nevét, egy szóközt, az alkalmazás most már sikeresen telepíthető. 

#### <a name="detailed-updates"></a>Részletes frissítések
Alább az Azure Machine Learning a sprint minden egyes összetevő terület részletes frissítések listája.

##### <a name="installer"></a>Telepítő
- Telepítő most törli azokat a telepítési könyvtár, az alkalmazás régebbi verziójával készült.
- Kijavítva a hiba, amely visszavezet telepítő első 100 %-a macOS High Sierra megakad.
- Most már rendelkezésre áll a felhasználót, hogy tekintse át a telepítési naplókat, abban az esetben telepítése nem sikerül a telepítő könyvtár közvetlen hivatkozás.
- Most már működik a felhasználók számára, hogy van-e a felhasználó neve a hely telepítéséhez.

##### <a name="workbench-authentication"></a>Workbench-hitelesítés
- A Proxy Manager hitelesítés támogatása.
- Bejelentkezés most sikeres, ha a felhasználó egy tűzfal mögött található. 
- Ha felhasználó Kísérletezési fiók több Azure-régióban, és a egy adott régióban történik a nem érhető el, az alkalmazás már nem lefagy.
- Hitelesítés nem fejeződött be, és a hitelesítési párbeszédpanelen továbbra is látható, amikor alkalmazást már nem megpróbálja betölteni munkaterület a helyi gyorsítótárból.

##### <a name="workbench-app"></a>Workbench alkalmazás
- Python-kód Szintaxiselemek kiemelése szövegszerkesztőben engedélyezve van.
- A Szerkesztés gomb a szövegszerkesztőben lehetővé teszi, hogy a fájl szerkesztése vagy egy IDE-ben (a VS Code és a PyCharm támogatottak) vagy a beépített szövegszerkesztőben.
- Szövegszerkesztőben alapértelmezés szerint csak olvasható módban van. 
- Az aktuális fájl után mentett, és ezért már nem inkonzisztencia gomb visual állapota most módosításainak mentése le van tiltva.
- Menti a Workbench _összes_ nem mentett fájlokat, egy Futtatás kezdeményezésekor.
- Workbench megjegyzi a legutoljára használt munkaterületet a helyi gépen, így automatikusan megnyílik.
- Csak egyetlen példány Workbench most engedélyezett. Korábban több példány sikerült elindítani okozó problémák Ha ugyanazon a projekten.
- Átnevezett fájl menü "Megnyitott projekt...", "Hozzáadása meglévő mappát, projekt..." 
- Váltás a lap már sokkal gyorsabb.
- Súgóhivatkozások az IDE konfigurálása párbeszédpanel kerülnek.
- A visszajelzési űrlapon most megjegyzi a legutóbb megadott címre.
- Smiles és frowns űrlap szövegterület már nagyobb méretű, így elküldheti nekünk további visszajelzésekre! 
- A `--owner` súgószöveg váltson `az ml workspace create` a hiba kijavításáig.
- Hozzáadtunk egy "Névjegy" párbeszédpanelen érdekében felhasználói könnyű megtekintése és másolása az alkalmazás verziószáma.
- A Súgó menü kerül egy "Javasoljon szolgáltatást" elemet.
- Kísérletezési fiók neve már látható az alkalmazás címében sáv, az alkalmazás neve "Azure Machine Learning Workbench" megelőző.
- Verzió-specifikus alkalmazás kezdőlapja jelenik meg az észlelt alkalmazás verziója mostantól alapján.

##### <a name="data-preparation"></a>Adatok előkészítése 
- Külső webhely már nem tölthetők be a térkép vizsgáló potenciális biztonsági problémák elkerülése érdekében.
- Hisztogram és értékek száma vizsgálók most már rendelkezik a grafikon a Logaritmikus skála megjelenítését.
- Ha egy számítás folyamatban, sávjában most látható, hogy a "kiszámítása" állapotot jelezze különböző szín.
- Oszlop metrikák már látható a kategorikus érték oszlopok statisztikáit.
- Az utolsó karakter az adatforrás neve már nem a rendszer csonkolja.
- Adat-előkészítési csomag most már nyitva marad lapokat, ami észrevehető teljesítménynövekedést váltáskor.
- Az adatforrás, adatokat és metrika nézet közötti váltáskor most már az oszlopok sorrendje már változik.
- Érvénytelen megnyitása `.dprep` vagy `.dsource` fájl már nem okoz Workbench összeomolhat.
- Adat-előkészítési csomagot is használ relatív elérési utat a kimeneti _csv-fájlba írni_ átalakítása.
- _Oszlop megtartása_ átalakítás most már lehetővé teszi a felhasználó szerkesztésekor további oszlopokat is hozzáadhat.
- _Cserélje le a_ menü most ténylegesen elindítja _értéket cserélje le_ párbeszédpanel bezárásához.
- _Cserélje le az értéket_ átalakítása funkciók mostantól értesítő hiba helyett a várt módon.
- Az adatfájlokat a projektmappára, lehetővé téve az környezetben történő futtatásra a csomag helyi abszolút elérési úttal rendelkező arra az adatfájlra kívül hivatkozó adat-előkészítési csomag mostantól abszolút elérési utat használja.
- _A teljes fájl_ , a mintavételezési stratégia mostantól akkor támogatott, ha az Azure-blobot adatforrásként.
- Python-kódokat (adat-előkészítési csomag) generált most már a CR és a LF, hogy ez rövid Windows végzi.
- _Metrikák kiválasztása_ legördülő most elrejtése tulajdonság, az adatnézet történő váltáskor.
- Workbench mostantól a folyamat parquet-fájlok akkor is, ha a Python-futtatókörnyezet használ. Korábban csak a Spark is használható, ha a parquet-fájlok feldolgozása. 
- Az oszlop értékeinek szűrése _dátum_ adattípus többé nem okoz előkészítő motor összeomolhat.
- Metrika nézet mostantól figyelembe veszi a mintavételezési stratégia frissítéseket.
- Távoli feladatok most mintavétel megfelelően működik.

##### <a name="job-execution"></a>Feladat végrehajtása
- Argumentum már elérhető a futtatási előzményrekordként.
- A feladatok kezdődjön CLI most már megjelenik-e futtatási előzmények feladat panel automatikusan.
- Feladat panelen most már megjelenik az Azure AD-bérlőhöz hozzáadni vendégfelhasználókat által létrehozott feladatot.
- Feladat panel megszakítja, és stabilabbá törlési műveletek.
- Amikor a Futtatás gombra kattint, hibaüzenet jelenik meg most akkor aktiválódik, ha a konfigurációs fájlok formátuma hibás.
- Megszakítást alkalmazás már nem zavarja a feladatok a parancssori felület kezdődjön.
- Kezdődjön feladatok CLI most továbbra is egy órányi végrehajtása után is el standard kimenő felosztás.
- Ha az adat-előkészítési csomag futtatása sikertelen, a Python-/ PySpark jobb hibaüzenetek jelennek meg.
- `az ml experiment clean` most már megtisztítja a Docker-rendszerképeket, valamint a távoli virtuális gépen.
- `az ml experiment clean` most már megfelelően működik a helyi cél macOS rendszeren.
- Hibaüzenetek a helyi vagy távoli Docker célzó futtatásakor törölve lettek, és könnyebben olvasható.
- Jobb hibaüzenet jelenik meg, amikor a HDInsight fürt fő csomópontjának neve nem megfelelő formátumú, amikor egy végrehajtási célként csatlakoztatott.
- Jobb hibaüzenet jelenik meg, amikor a titkos kulcs nem található a hitelesítő adatok szolgáltatásban. 
- MMLSpark könyvtár támogatja az Apache Spark 2.2-es verzióra frissíti.
- MMLSpark között már elérhető a tulajdonos kódolási átalakító (háló kódolás) orvosi dokumentumok.
- `matplotlib` 2.1.0-ás most szállításra ki a box, a Workbench.

##### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook neve Keresés most már megfelelően működik a notebookok nézetben.
- Most törölheti a notebookok nézetben jegyzetfüzet.
- Új Magic Quadrant `%upload_artifact` bekerül a fájlok feltöltése előállított, a futtatási előzmények adattárba jegyzetfüzet-végrehajtási környezetben található.
- A Notebook feladat állapota könnyebb hibakeresés most illesztett rendszermag hibái.
- Jupyter server most már megfelelően leállítja a felhasználó bejelentkezésekor ki az alkalmazásból.

##### <a name="azure-portal"></a>Azure Portal
- Kísérletezési fiók és a Modellkezelési fiókot most már két új Azure-régióban hozható létre: Nyugat-Európa és Délkelet-Ázsia.
- Modell felügyeleti fiók DevTest terv most csak akkor használható amikor kell létrehozni az előfizetést az elsőt. 
- Az Azure Portalon látható Súgó hivatkozásra frissül, hogy a megfelelő dokumentációs oldalon mutasson.
- A Leírás mező Docker rendszerkép részleteit megjelenítő oldalon törlődik, mivel nem alkalmazható.
- Webes szolgáltatás részletek lapon többek között az appinsights által biztosított és automatikus méretezési beállítások részletei kerülnek.
- Modell kezelése lap most Ez a beállítás akkor is, ha a cookie-k le vannak tiltva a böngészőben. 

##### <a name="operationalization"></a>Üzembe helyezés
- "Pontszám", amelynek neve a webszolgáltatás már nem.
- Felhasználó most csak az Azure-erőforráscsoport vagy előfizetés közreműködői hozzáféréssel rendelkező egy üzembehelyezési környezetet hozhat létre. A teljes előfizetés tulajdonosi hozzáféréssel már nincs rá szükség.
- Most CLI operacionalizálás élvez lapon automatikus kiegészítését Linux rendszeren.
- Kép konstrukció szolgáltatás már támogatja az épület rendszerképek az Azure IoT-szolgáltatások és eszközök.

##### <a name="sample-projects"></a>Minta-projektek
- [_Írisz osztályozása_ ](../desktop-workbench/tutorial-classifying-iris-part-1.md) mintaprojektet:
    - `iris_pyspark.py` új `iris_spark.py`.
    - `iris_score.py` új `score_iris.py`.
    - `iris.dprep` és `iris.dsource` frissülnek, hogy a legújabb adatok előkészítő motor frissítéseket tükrözik.
    - `iris.ipynb` Notebook dolgozhat a HDInsight-fürt módosul.
    - Futtatási előzmények be van kapcsolva a `iris.ipynb` Notebook cella.
- [_Fejlett Adatelőkészítés Kerékpármegosztási adatok használatával_ ](../desktop-workbench/tutorial-bikeshare-dataprep.md) rögzített "Kezelni hiba érték" lépés mintaprojektet.
- [_A felnőtt népszámlálási adatok MMLSpark_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) mintaprojektet `docker.runconfig` JSON-ból a frissített YAML formátumú.
- [_Elosztott Hiperparaméter finomhangolása_ ](../desktop-workbench/scenario-distributed-tuning-of-hyperparameters.md) mintaprojektet`docker.runconfig` JSON-ból a frissített YAML formátumú.
- Új mintaprojektet [ _kép besorolása a CNTK használatával_](../desktop-workbench/scenario-image-classification-using-cntk.md).


### <a name="2017-10-sprint-0"></a>2017-10-es (sprint 0) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([a verzió megkereséséhez](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Üdvözli az Azure Machine Learning Workbench a kezdeti nyilvános előzetes verziója a Microsoft Ignite 2017 konferencián követő első frissítés. Ebben a kiadásban a fő frissítések állnak, megbízhatóságát és stabilizált javítja.  A kritikus problémákra azt a következők:

#### <a name="new-features"></a>Új funkciók
- Mostantól támogatott a macOS High Sierra

#### <a name="bug-fixes"></a>Hibajavítások
##### <a name="workbench-experience"></a>Workbench-élmény
- Húzza és betett egy fájlt Workbench okai annak, ha összeomlik a Workbenchben.
- A terminálablakban a VS Code szerkesztőben Workbench nem ismeri fel, az integrált fejlesztői Környezetig konfigurált _az ml_ parancsokat.

##### <a name="workbench-authentication"></a>Workbench-hitelesítés
Frissítések jelentett különböző bejelentkezési és hitelesítési hibák javítása érdekében több tettük.
- Hitelesítési ablak tartja a popping felfelé, különösen ha az internetkapcsolat nem stabil.
- Hitelesítési jogkivonat lejárati megbízhatósága felmerülő problémákat.
- Bizonyos esetekben hitelesítési ablak kétszer.
- Fő Workbench-ablak a "hitelesítés" jelenik meg, amikor a hitelesítési folyamat befejeződött, és már a megjelenő párbeszédpanelen továbbra is megjeleníti.
- Ha nincs internetkapcsolat, a hitelesítési párbeszédablak jelenik meg, egy üres képernyőt.

##### <a name="data-preparation"></a>Adatok előkészítése 
- Ha egy adott érték vannak leszűrve, hibák és a hiányzó értékek is ki van szűrve.
- A mintavételezési stratégia módosítása eltávolítja az ezt követő meglévő összekapcsolási műveletek.
- Hiányzó értékét átalakítás nem NaN veszi figyelembe.
- Dátum típusú következtetésekhez kivételt jelez, amikor null értéket észlelt.

##### <a name="job-execution"></a>Feladat végrehajtása
- Nem érkezik a nem egyértelmű hibaüzenet jelenik meg, ha a feladat végrehajtása nem sikerül projekt mappa feltöltése, mert túllépte a maximális mérete.
- Ha a felhasználó Python-szkript módosítja a munkakönyvtárat, kimenetek mappák írt fájlok nem követi nyomon. 
- Ha az aktív Azure-előfizetés az aktuális projekt tartozik, eltérő, a feladat küldése eredmények 403-as hibát.
- Ha Docker nem található, nem egyértelmű hibaüzenetet ad vissza, ha felhasználó megpróbálja használni a Docker-végrehajtási célként.
- .runconfig fájl nem menti automatikusan, amikor a felhasználó rákattint _futtatása_ gombra.

##### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook server nem tud elindulni, ha a felhasználó használja az egyes bejelentkezési típusok.
- A felhasználó számára látható rögzít a notebook server hibaüzenetek nem jelent.

##### <a name="azure-portal"></a>Azure Portal
- Modellkezelési panelen egy fekete mezőt megjeleníteni kívánt kiválasztása az Azure Portal a sötét téma okoz.

##### <a name="operationalization"></a>Üzembe helyezés
- Egy új Docker-rendszerképet, beépített véletlenszerű névvel újbóli felhasználása a jegyzékfájl frissítése egy webszolgáltatás okoz.
- Webkiszolgáló naplói nem lehet beolvasni a Kubernetes-fürtöt.
- Félrevezető hibaüzenet van nyomtatva, amikor a felhasználó megpróbál hozzon létre egy Modellkezelési fiókot vagy egy ML Compute-fiókot, és engedélyekkel kapcsolatos probléma lép fel.

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Machine Learning](../service/overview-what-is-azure-ml.md) áttekintését.
