---
title: Mi az a ML-folyamat?
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, milyen előnyökkel jár a gépi tanulási folyamatok létrehozása a Pythonhoz készült Azure Machine Learning SDK használatával. A gépi tanulási (ML) folyamatokat az adatszakértők használják a gépi tanulási munkafolyamatok létrehozásához, optimalizálásához és kezeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/04/2019
ms.openlocfilehash: f1b061e92ce0650da4d5b95643eb6e9df917e3b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671548"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok a Azure Machine Learning folyamatok?

Azure Machine Learning folyamatok lehetővé teszik munkafolyamatok létrehozását a gépi tanulási projektekben. Ezek a munkafolyamatok számos előnnyel rendelkeznek: 

+ Egyszerűség
+ Sebesség
+ Ismételhetőség
+ Rugalmasság
+ Verziószámozás és nyomon követés
+ Modularitás 
+ Minőségbiztosítás
+ Cost Control

Ezek az előnyök jelentősek lesznek, amint a gépi tanulási projekt a tiszta feltáráson és az iteráción túl is halad. Még az egyszerű egylépéses folyamatok is hasznosak lehetnek. A gépi tanulási projektek gyakran összetett állapotban vannak, és az is lehetséges, hogy az egyetlen munkafolyamat pontos megvalósítását egy triviális folyamat teszi lehetővé.

Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md).

![Gépi tanulási folyamatok Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-adatcsatorna-technológiát használok?

Az Azure-felhő számos más folyamatot is biztosít, amelyek mindegyike más célra szolgál. A következő táblázat felsorolja a különböző folyamatokat, és azt, hogy mire szolgálnak:

| Folyamat | Művelet | Kanonikus cső |
| ---- | ---- | ---- |
| Azure Machine Learning folyamatok | Az újrafelhasználható gépi tanulási munkafolyamatokat definiálja, amelyek sablonként használhatók a gépi tanulási forgatókönyvekhez. | Adat> modell |
| [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | A feladatok végrehajtásához szükséges adatáthelyezési,-átalakítási és-ellenőrzési tevékenységek csoportjai.  | Adat>i adatszolgáltatások |
| [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Az alkalmazás folyamatos integrációja és továbbítása bármely platformra/felhőbe  | Code-> alkalmazás/szolgáltatás |

## <a name="what-can-azure-ml-pipelines-do"></a>Mit tehet az Azure ML-folyamatok?

Az Azure Machine Learning folyamat egy teljes gépi tanulási feladat egymástól független végrehajtható munkafolyamata. Az altevékenységek a folyamat lépéseinek sorozata alá vannak ágyazva. Egy Azure Machine Learning folyamat olyan egyszerű lehet, amely egy Python-szkriptet hív meg, _így bármit elvégezhet._ A folyamatoknak a gépi tanulási feladatokra _kell_ összpontosítaniuk, például:

+ Adatelőkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a munging és az átalakítást, a normalizálás és az előkészítést
+ Betanítási konfiguráció, beleértve a parameterizing argumentumokat, a filepaths és a naplózási/jelentéskészítési konfigurációkat
+ Hatékonyan és ismételhetően betaníthatja és érvényesítheti a konkrét adatrészhalmazokat, a különböző hardveres számítási erőforrásokat, az elosztott feldolgozást és az előrehaladás figyelését.
+ Üzembe helyezés, beleértve a verziószámozást, a skálázást, a létesítést és a hozzáférés-vezérlést 

A független lépések lehetővé teszik, hogy több adatszakértő egyszerre működjön ugyanazon a folyamaton, és ne legyenek túlterhelő számítási erőforrások. A külön lépések azt is megkönnyítik, hogy az egyes lépésekhez különböző számítási típusokat/méreteket lehessen használni.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újra futtatandó lépésekre ugrik, például egy frissített tanítási parancsfájlt. Az újrafuttatást nem igénylő lépéseket a rendszer kihagyja. Ugyanez az elemzés a lépés végrehajtásához használt változatlan parancsfájlokra is vonatkozik. Ez a funkció segít elkerülni a költséges és időigényes lépéseket, például az adatfeldolgozást és-átalakítást, ha a mögöttes adat nem módosult.

A Azure Machine Learning számos eszközkészletet és keretrendszert használhat, például PyTorch vagy TensorFlow a folyamat egyes lépéseihez. Az Azure koordinálja az Ön által használt különböző [számítási célokat](concept-azure-machine-learning-architecture.md) , így a köztes adatok megoszthatók az alárendelt számítási célokkal.

A [folyamat-kísérletek metrikáit](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) közvetlenül Azure Portal vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat újrafuttatását bármely platformról vagy veremből.

Röviden, a gépi tanulási életciklus összes összetett feladata segíthet a folyamatokban. A többi Azure-folyamat technológiája saját erősségekkel rendelkezik, például [Azure Data Factory folyamatokat](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) az adatkezeléshez és az [Azure-folyamatokhoz](https://azure.microsoft.com/services/devops/pipelines/) a folyamatos integráció és üzembe helyezés érdekében. Ha azonban a hangsúly a gépi tanulás, a munkafolyamatok igényei szerint Azure Machine Learning folyamatok valószínűleg a legjobb választás. 

## <a name="what-are-azure-ml-pipelines"></a>Mik azok az Azure ML-folyamatok?

Az Azure ML-folyamat egy teljes logikai munkafolyamatot hajt végre, amely a lépések rendezett sorrendjét adja meg. Mindegyik lépés egy diszkrét feldolgozási művelet. A folyamatok egy Azure Machine Learning [kísérlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)kontextusában futnak.

Egy ML-projekt nagyon korai szakaszában egy Jupyter notebook-vagy Python-szkripttel rendelkezhet, amely az Azure munkaterület és az erőforrás-konfiguráció összes műveletét, az adat-előkészítést, a konfigurációt, a képzést és az érvényesítést végzi. De ahogy a függvények és az osztályok gyorsan válnak a kód egyetlen kötelező blokkjában, a ML-munkafolyamatok gyorsan válnak a monolitikus notebookok vagy szkriptek számára. 

A modularizing ML-feladatok esetében a folyamatok támogatják a számítógép tudományos feladatait, hogy egy összetevőnek "do (only)" (csak) egy dolgot kell használnia. " A modularitás egyértelműen elengedhetetlen a projekt sikerességéhez a csapatok programozása során, de még akkor is, ha egyedül dolgozik, még egy kis ML-projekt külön feladatokat is magában foglal, amelyek mindegyike megfelelő mennyiségű összetettséggel rendelkezik. A feladatok közé tartoznak a munkaterület konfigurációja és az adatokhoz való hozzáférés, az adatok előkészítése, a modell definíciója és a konfiguráció, valamint az üzembe helyezés. Míg egy vagy több feladat kimenetei egy másikba kerülnek, az egyik tevékenység pontos megvalósítási adatai, a legjobb esetben a következőben nem relevánsak. A legrosszabb esetben az egyik feladat számítási állapota hibát okozhat egy másikban. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztémához olyan eszközök tartoznak, amelyek az erőforrás-, a könyvtár-és a fordítási függőségeket hangolják össze. Ezek az eszközök általában a fájlok időbélyegét használják a függőségek kiszámításához. Egy fájl módosításakor a rendszer csak az IT-t és a hozzá tartozó függőket frissíti (letöltött, újrafordított vagy csomagolt). Az Azure ML-folyamatok nagy mértékben kiterjesztik ezt a koncepciót. A hagyományos Build-eszközökhöz hasonlóan a folyamatok is kiszámítják a lépések közötti függőségeket, és csak a szükséges újraszámításokat hajtják végre. 

Az Azure ML-folyamatok függőségi elemzése bonyolultabb, mint az egyszerű időbélyegek. Előfordulhat, hogy minden lépés más hardver-és szoftver-környezetben fut. Az adatelőkészítés lehet időigényes folyamat, de nem kell hatékony GPU-val futtatni a hardveren, bizonyos lépésekhez szükség lehet az operációs rendszer-specifikus szoftverekre, érdemes lehet elosztott képzést használni, és így tovább. Az erőforrások optimalizálásának költségmegtakarítása jelentős lehet, de a hardveres és a szoftveres erőforrások különböző változásainak manuális átkódolása is lehetséges. Még nehezebbé teheti az összes lépést anélkül, hogy a lépések között elvégezte a hibát. 

A folyamatok megoldja ezt a problémát. Azure Machine Learning automatikusan összehangolja a folyamat lépései közötti összes függőséget. Ez a beállítás magában foglalhatja a Docker-rendszerképek rögzítését, a számítási erőforrások csatlakoztatását és leválasztását, valamint az adatok konzisztens és automatikus módon történő áthelyezését.

### <a name="reusing-results"></a>Eredmények újrafelhasználása

Emellett előfordulhat, hogy a lépés kimenetét újra fel kell használni. Ha az újbóli használat lehetőséget választja, és nincsenek az újraszámítást kiváltó felsőbb rétegbeli függőségek, a folyamat szolgáltatás a lépés eredményeinek gyorsítótárazott verzióját fogja használni. Az ilyen újrafelhasználás jelentősen csökkentheti a fejlesztési időt. Ha összetett adat-előkészítési feladattal rendelkezik, valószínűleg gyakrabban újra kell futtatnia, mint amennyire feltétlenül szükség van. A folyamatok lecsillapítják Önt, hogy ha szükséges, a lépés futtatása sikertelen lesz.

A rendszer az összes függőségi elemzést, összehangolást és aktiválást Azure Machine Learning kezeli, amikor egy [folyamat](https://docs.microsoft.com/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) objektumot hoz létre, átadja egy `Experiment`, és meghívja a `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Az érintett lépések koordinálása

`Pipeline` objektum létrehozásakor és futtatásakor a következő magas szintű lépések történnek:

+ Az egyes lépésekhez a szolgáltatás a következő követelményeket számítja ki:
    + Hardveres számítási erőforrások
    + Operációs rendszer erőforrásai (Docker-rendszerkép (ek))
    + Szoftverek erőforrásai (Conda/virtualenv-függőségek)
    + Adatbemenetek 
+ A szolgáltatás meghatározza a lépések közötti függőségeket, ami dinamikus végrehajtási gráfot eredményez.
+ A végrehajtási gráf egyes csomópontjai a következőket futtatják:
    + A szolgáltatás konfigurálja a szükséges hardver-és szoftver-környezetet (például meglévő erőforrások újrafelhasználása)
    + A lépés futtatása, naplózási és figyelési információk biztosítása a `Experiment` objektumhoz
    + Ha a lépés befejeződik, a kimenetei a következő lépéshez és/vagy a Storage-ba írt bemenetként készülnek.
    + A már nem szükséges erőforrások véglegesítése és leválasztása

![Folyamat lépései](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Folyamatok fejlesztése a Python SDK-val

A [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-ban a folyamat egy Python-objektum, amely a `azureml.pipeline.core` modulban van definiálva. A [folyamat](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objektum egy vagy több [PipelineStep](https://docs.microsoft.com/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) -objektum rendezett sorrendjét tartalmazza. A `PipelineStep` osztály absztrakt, és a tényleges lépések alosztályok lesznek, például [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)vagy [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) osztály a folyamatok között megosztható lépések újrafelhasználható szakaszát tartalmazza. A `Pipeline` egy `Experiment`részeként fut.

Egy Azure Machine Learning munkaterülethez egy Azure ML-folyamat van társítva, és egy folyamat lépés társítva van egy, az adott munkaterületen belül elérhető számítási célhoz. További információ: Azure Machine Learning- [munkaterületek létrehozása és kezelése a Azure Portal](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) , illetve [Mik a Azure Machine learning számítási céljai?](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target)

Azure Machine Learning a számítási cél az a környezet, amelyben egy ML-fázis történik. A szoftveres környezet lehet távoli virtuális gép, Azure Machine Learning számítás, Azure Databricks, Azure Batch stb. A hardveres környezet nagy mértékben is változhat, a GPU-támogatástól, a memóriától, a tárterülettől és így tovább. Megadhatja az egyes lépésekhez tartozó számítási célt, amely részletesen szabályozza a költségeket. A projekt adott műveletéhez, adatmennyiségéhez és teljesítményéhez szükséges több vagy kevésbé hatékony erőforrásokat is használhat. 

## <a name="building-pipelines-with-the-designer"></a>Folyamatok fejlesztése a tervezővel

A vizuális tervezési felületet előnyben részesített fejlesztők a Azure Machine Learning Designer használatával hozhatnak létre folyamatokat. Ehhez az eszközhöz a munkaterület kezdőlapján található **tervező** kijelölésen keresztül férhet hozzá.  A Designer lehetővé teszi, hogy a lépéseket a tervezési felületre húzza. A gyors fejlesztés érdekében a meglévő modulokat a ML-feladatok spektrumán keresztül használhatja; a meglévő modulok az adatok átalakítását és az algoritmus kiválasztását is lefedik az üzembe helyezéshez. Vagy létrehozhat egy teljesen egyéni folyamatot a Python-szkriptekben definiált saját lépések kombinálásával.

A folyamatok vizuális megtervezése során láthatók a lépések bemenetei és kimenetei. Az adatkapcsolatok húzásával gyorsan megismerheti és módosíthatja a folyamat adatfolyam.
 
![Azure Machine Learning Designer – példa](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>A végrehajtási gráf ismertetése

A folyamat lépései más lépésekkel is függőségekkel rendelkezhetnek. Az Azure ML-adatcsatorna szolgáltatás ezen függőségek elemzésével és összehangolásával dolgozik. Az eredményül kapott "végrehajtási gráf" csomópontjai a feldolgozási lépések. Az egyes lépések magukban foglalhatják a hardverek és szoftverek adott kombinációjának létrehozását vagy újbóli felhasználását, a gyorsítótárazott eredmények újrafelhasználását stb. A szolgáltatás előkészítési és optimalizálási folyamata jelentősen felgyorsíthatja az ML-fázist, és csökkentheti a költségeket. 

Mivel a lépések egymástól függetlenül futnak, a bemeneti és kimeneti adatokat tároló objektumokat külsőleg kell definiálni. Ez a [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), a [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)és a társított osztályok szerepe. Ezek az adatobjektumok egy [adattár](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) -objektumhoz vannak társítva, amely magában foglalja a tárolási konfigurációját. A `PipelineStep` alaposztályt mindig egy `name` karakterlánccal hozza létre, a `inputs`listáját és a `outputs`listáját. Általában a `arguments` listáját is tartalmazza, és gyakran a `resource_inputs`listáját fogja tartalmazni. Az alosztályok általában további argumentumokkal is rendelkezhetnek (például `PythonScriptStep` a futtatandó parancsfájl fájlnevét és elérési útját igényli). 

A végrehajtási gráf aciklikus, de a folyamatok ismétlődő ütemterv szerint futtathatók, és futtathatnak olyan Python-parancsfájlokat, amelyek állapotinformációkat írhatnak a fájlrendszerbe, így összetett profilok hozhatók létre. Ha úgy tervezi a folyamatot, hogy bizonyos lépések párhuzamosan vagy aszinkron módon fussanak, Azure Machine Learning transzparens módon kezeli a függőségi elemzést és a ventilátorok és ventilátorok koordinálását. Általában nem kell foglalkoznia a végrehajtási gráf részleteivel, de a [folyamat. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) attribútumán keresztül érhető el. 


### <a name="a-simple-python-pipeline"></a>Egy egyszerű Python-folyamat

Ez a kódrészlet az alapszintű `Pipeline`létrehozásához és futtatásához szükséges objektumokat és hívásokat mutatja:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

A kódrészlet általános Azure Machine Learning objektumokat, egy `Workspace`, egy `Datastore`t, egy [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)és egy `Experiment`t indít el. Ezután a kód létrehozza a `input_data` és `output_data`tárolására szolgáló objektumokat. A tömb `steps` egyetlen elemet tartalmaz, egy `PythonScriptStep`, amely az adatobjektumokat fogja használni, és a `compute_target`fut. Ezt követően a kód maga a `Pipeline` objektumot hozza létre, a munkaterületen és a Steps tömbben átadva. A `experiment.submit(pipeline)` hívása megkezdi az Azure ML-folyamat futtatását. A `wait_for_completion()` blokkok hívása, amíg a folyamat be nem fejeződik. 

## <a name="best-practices-when-using-pipelines"></a>Ajánlott eljárások a folyamatok használatakor

Amint láthatja, az Azure ML-folyamat létrehozása valamivel összetettebb, mint egy parancsfájl elindítása. A folyamatokhoz néhány Python-objektum konfigurálása és létrehozása szükséges. 

Bizonyos helyzetekben, amelyek egy folyamat használatát sugallják:

* Csapat-környezetekben: az ML-feladatok több független lépésre oszthatók, így a fejlesztők egymástól függetlenül dolgozhatnak és bővíthetik a programjaikat. 

* Ha a vagy a közelében üzemelő példányban van: leszegezi a konfigurációt, és az ütemezett és eseményvezérelt műveletek használatával marad az adatok módosítása.

* Egy ML-projekt korai szakaszában, vagy önállóan működik: a buildek automatizálásához használjon folyamatokat. Ha már elkezdte a konfiguráció és a számítási állapot újbóli létrehozását az új ötlet megvalósítása előtt, akkor ez egy olyan jel, amelyet fontolóra vehet egy folyamat használatával a munkafolyamat automatizálásához. 

Könnyűvé válik a gyorsítótárazott eredmények újrafelhasználása, a számítási költségek részletes szabályozása és az elkülönítés feldolgozása, de a folyamatok költségei is megtalálhatók. Néhány példa a következőkre:

* A folyamatokat kizárólag különálló módon használhatja. A Python beépített funkciói, objektumai és moduljai hosszú utat biztosítanak a programozott állapot elkerüléséhez! A folyamat lépései sokkal drágábbak, mint a függvények hívása.

* Nagy mennyiségű kapcsolódás a folyamat lépései között. Ha egy függő lépést gyakran kell módosítani az előző lépés kimenetének módosítására, akkor valószínű, hogy a különálló lépések jelenleg több, mint egy előnyben részesülnek. Egy másik nyom, hogy a lépések túl vannak párosítva, olyan lépés argumentumai, amelyek nem adat, hanem a feldolgozás vezérlésére szolgáló jelzők. 

* A számítási erőforrások idő előtti optimalizálása. Előfordulhat például, hogy az adatok előkészítésének több szakasza is van, és az egyik gyakran "ó, itt az a hely, ahol használhatok egy `MpiStep` párhuzamos programozáshoz, de itt egy olyan hely, ahol egy kevésbé hatékony számítási céllal használhatok `PythonScriptStep` , "és így tovább. És lehet, hogy hosszú távon olyan részletes lépéseket hoz létre, mint például az, ami érdemes lehet, különösen, ha a gyorsítótárazott eredményeket nem mindig az újraszámítással szeretné használni. A folyamatok azonban nem helyettesíthetik a `multiprocessing` modult. 

Amíg egy projekt nagy vagy közel kerül a központi telepítéshez, a folyamatoknak durvábbnak kell lenniük, nem pedig a részletesen. Ha úgy gondolja, hogy a ML-projekt a _szakaszokat_ és a folyamatokat is felhasználva teljes munkafolyamatot biztosít, amely egy adott fázison halad át, akkor a megfelelő elérési úton van. 

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Kulcs előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;-futtatások**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. A rendelkezésre álló számítási erőforrások hatékony felhasználása a különböző számítási célokon, például a HDInsight, a GPU-adatelemzési virtuális gépeken és a Databricks futó egyes folyamatok lépéseinek futtatásával.|
|**Újrafelhasználhatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követés és verziószámozás**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
| **Modularitás** | Az érintett területek elkülönítése és a változások elkülönítése lehetővé teszi, hogy a szoftverek gyorsabb ütemben, magasabb színvonalú minőségben fejlődjenek. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="next-steps"></a>További lépések

Az Azure ML-folyamatok egy hatékony létesítmény, amely megkezdi az értékek megvalósítását a korai fejlesztési fázisokban. Az érték növekszik, ahogy a csapat és a projekt növekszik. Ez a cikk azt ismerteti, hogyan vannak megadva a folyamatok a Azure Machine Learning Python SDK-val és az Azure-ban. Megtalálta az alapszintű forráskódot, és bevezette az elérhető `PipelineStep` osztályokba. Érdemes lehet az Azure ML-adatfolyamatok és az Azure-t futtató folyamatok használatára. 


+ Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md).

+ Megtudhatja, hogyan [futtathat batch-előrejelzéseket nagy mennyiségű adattal](tutorial-pipeline-batch-scoring-classification.md ).

+ Tekintse meg az SDK-dokumentációt a [pipeline Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a [folyamat lépéseihez](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Próbálja ki például a [Azure Machine learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter notebookokat. Útmutató [jegyzetfüzetek futtatásához a szolgáltatás megismeréséhez](samples-notebooks.md).
