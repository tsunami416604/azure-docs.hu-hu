---
title: Mik azok a Azure Machine Learning folyamatok?
description: Ismerje meg, hogy a Machine learning (ML) folyamatai hogyan segítenek a gépi tanulási munkafolyamatok létrehozásában, optimalizálásában és felügyeletében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: tracking-python
ms.openlocfilehash: 7d34b54349a681061f80798a19ce65d2266f589d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559471"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok a Azure Machine Learning folyamatok?

Ebből a cikkből megtudhatja, hogyan segíti a Azure Machine Learning folyamatokat a gépi tanulási munkafolyamatok létrehozásához, optimalizálásához és kezeléséhez. Ezek a munkafolyamatok számos előnnyel rendelkeznek: 

+ Egyszerűség kedvéért
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

| Forgatókönyv | Elsődleges persona | Azure-ajánlat | OSS-ajánlat | Kanonikus cső | Erősségeit | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modell-előkészítés (gépi tanulás) | Adatelemző | Azure Machine Learning folyamatok | Kubeflow-folyamatok | Adat> modell | Eloszlás, gyorsítótárazás, kód – első, újrafelhasználás | 
| Adat-előkészítés (adat-előkészítés) | Adatszakértő | [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache légáram | Adat>i adatszolgáltatások | Erősen beírt mozgás. Adatközpontú tevékenységek. |
| Kód & alkalmazás-előkészítés (CI/CD) | Alkalmazás fejlesztője/Ops | [Azure DevOps-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model – > alkalmazás/szolgáltatás | A legtöbb nyitott és rugalmas tevékenység támogatása, jóváhagyási várólisták, fázisok és kapuzás | 

## <a name="what-can-azure-ml-pipelines-do"></a>Mit tehet az Azure ML-folyamatok?

Az Azure Machine Learning folyamat egy teljes gépi tanulási feladat egymástól független végrehajtható munkafolyamata. Az alfeladatok lépések sorozataként vannak beágyazva a folyamatba. Egy Azure Machine Learning folyamat olyan egyszerű lehet, amely egy Python-szkriptet hív meg, _így bármit elvégezhet._ A folyamatoknak a gépi tanulási feladatokra _kell_ összpontosítaniuk, például:

+ Adat-előkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a kezelést és az átalakítást, a normalizálást és az előkészítést
+ Betanítás konfigurálása, beleértve az argumentumok, a fájlok elérési útvonalai és a naplózási/jelentéskészítési konfigurációk paraméterezését
+ Hatékony és ismételt betanítás és ellenőrzés. A hatékonyság az adott adathalmazok, a különböző hardveres számítási erőforrások, az elosztott feldolgozás és a folyamat figyelésének megadásával lehetséges.
+ Üzembe helyezés, beleértve a verziószámozást, a méretezést, a kiépítést és a hozzáférés-vezérlést 

A független lépések lehetővé teszik, hogy több adatszakértő egyszerre működjön ugyanazon a folyamaton, és ne legyenek túlterhelő számítási erőforrások. A külön lépések azt is megkönnyítik, hogy az egyes lépésekhez különböző számítási típusokat/méreteket lehessen használni.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újra futtatandó lépésekre ugrik, például egy frissített tanítási parancsfájlt. Az újrafuttatást nem igénylő lépéseket a rendszer kihagyja. Ugyanez az elemzés a lépés végrehajtásához használt változatlan parancsfájlokra is vonatkozik. Ez a funkció segít elkerülni a költséges és időigényes lépéseket, például az adatfeldolgozást és-átalakítást, ha a mögöttes adat nem módosult.

A Azure Machine Learning számos eszközkészletet és keretrendszert használhat, például PyTorch vagy TensorFlow a folyamat egyes lépéseihez. Az Azure koordinálja az Ön által használt különböző [számítási célokat](concept-azure-machine-learning-architecture.md) , így a köztes adatok megoszthatók az alárendelt számítási célokkal.

A [folyamat-kísérletek metrikáit](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) közvetlenül Azure Portal vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat újrafuttatását bármely platformról vagy veremből.

Röviden, a gépi tanulási életciklus összes összetett feladata segíthet a folyamatokban. Más Azure-adatcsatorna-technológiák saját erősségei vannak. Az adatkezelést és az [Azure-folyamatokat](https://azure.microsoft.com/services/devops/pipelines/) tartalmazó [Azure Data Factory folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a megfelelő eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ha azonban a hangsúly a gépi tanulás, a munkafolyamatok igényei szerint Azure Machine Learning folyamatok valószínűleg a legjobb választás. 

## <a name="what-are-azure-ml-pipelines"></a>Mik azok az Azure ML-folyamatok?

Az Azure ML-folyamat egy teljes logikai munkafolyamatot hajt végre, amely a lépések rendezett sorrendjét adja meg. Mindegyik lépés egy diszkrét feldolgozási művelet. A folyamatok egy Azure Machine Learning [kísérlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)kontextusában futnak.

Egy ML-projekt korai szakaszaiban egy Jupyter notebook-vagy Python-szkripttel rendelkezhet, amely az Azure munkaterület és az erőforrás-konfiguráció összes műveletét, az adatelőkészítést, a konfigurációt, a képzést és az érvényesítést végzi. De ahogy a függvények és az osztályok gyorsan válnak a kód egyetlen kötelező blokkjában, a ML-munkafolyamatok gyorsan válnak a monolitikus notebookok vagy szkriptek számára. 

A modularizing ML-feladatok esetében a folyamatok támogatják a számítógép tudományos feladatait, hogy egy összetevőnek "do (only)" (csak) egy dolgot kell használnia. " A modularitás egyértelműen elengedhetetlen a projekt sikerességéhez a csapatok programozása során, de még akkor is, ha egyedül dolgozik, még egy kis ML-projekt külön feladatokat is magában foglal, amelyek mindegyike megfelelő mennyiségű összetettséggel rendelkezik. A feladatok közé tartoznak a munkaterület konfigurációja és az adatokhoz való hozzáférés, az adatok előkészítése, a modell definíciója és a konfiguráció, valamint az üzembe helyezés. Míg egy vagy több feladat kimenetei egy másikba kerülnek, az egyik tevékenység pontos megvalósítási adatai, a legjobb esetben a következőben nem relevánsak. A legrosszabb esetben az egyik feladat számítási állapota hibát okozhat egy másikban. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztémához olyan eszközök tartoznak, amelyek az erőforrás-, a könyvtár-és a fordítási függőségeket hangolják össze. Ezek az eszközök általában a fájlok időbélyegét használják a függőségek kiszámításához. Egy fájl módosításakor a rendszer csak az IT-t és a hozzá tartozó függőket frissíti (letöltött, újrafordított vagy csomagolt). Az Azure ML-folyamatok nagy mértékben kiterjesztik ezt a koncepciót. A hagyományos Build-eszközökhöz hasonlóan a folyamatok is kiszámítják a lépések közötti függőségeket, és csak a szükséges újraszámításokat hajtják végre. 

Az Azure ML-folyamatok függőségi elemzése bonyolultabb, mint az egyszerű időbélyegek. Előfordulhat, hogy minden lépés más hardver-és szoftver-környezetben fut. Az adatelőkészítés lehet időigényes folyamat, de nem kell hatékony GPU-val futtatni a hardveren, bizonyos lépésekhez szükség lehet az operációs rendszer-specifikus szoftverekre, érdemes lehet elosztott képzést használni, és így tovább. Az erőforrások optimalizálásának költségmegtakarítása jelentős lehet, de a hardveres és a szoftveres erőforrások különböző változásainak manuális átkódolása is lehetséges. Még nehezebbé teheti az összes lépést anélkül, hogy a lépések között elvégezte a hibát. 

A folyamatok megoldja ezt a problémát. Azure Machine Learning automatikusan összehangolja a folyamat lépései közötti összes függőséget. Ez a beállítás magában foglalhatja a Docker-rendszerképek rögzítését, a számítási erőforrások csatlakoztatását és leválasztását, valamint az adatok konzisztens és automatikus módon történő áthelyezését.

### <a name="reusing-results"></a>Eredmények újrafelhasználása

Emellett előfordulhat, hogy a lépés kimenetét újra fel kell használni. Ha az újbóli használat lehetőséget választja, és nincsenek az újraszámítást kiváltó felsőbb rétegbeli függőségek, a folyamat szolgáltatás a lépés eredményeinek gyorsítótárazott verzióját fogja használni. Az ilyen újrafelhasználás jelentősen csökkentheti a fejlesztési időt. Ha összetett adat-előkészítési feladattal rendelkezik, valószínűleg gyakrabban újra kell futtatnia, mint amennyire feltétlenül szükség van. A folyamatok lecsillapítják Önt, hogy ha szükséges, a lépés futtatása sikertelen lesz.

A rendszer az összes függőségi elemzést, összehangolást és aktiválást Azure Machine Learning kezeli, amikor egy [folyamat](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) -objektumot hoz létre, átadja azt egy `Experiment` , és meghívja `submit()` . 

### <a name="coordinating-the-steps-involved"></a>Az érintett lépések koordinálása

Egy objektum létrehozásakor és futtatásakor `Pipeline` a következő magas szintű lépések történnek:

+ Az egyes lépésekhez a szolgáltatás a következő követelményeket számítja ki:
    + Hardveres számítási erőforrások
    + Operációs rendszer erőforrásai (Docker-rendszerkép (ek))
    + Szoftverek erőforrásai (Conda/virtualenv-függőségek)
    + Adatbemenetek 
+ A szolgáltatás meghatározza a lépések közötti függőségeket, ami dinamikus végrehajtási gráfot eredményez.
+ A végrehajtási gráf egyes csomópontjai a következőket futtatják:
    + A szolgáltatás konfigurálja a szükséges hardver-és szoftver-környezetet (például meglévő erőforrások újrafelhasználása)
    + A lépés futtatása, naplózási és figyelési információk biztosítása a tartalmazó `Experiment` objektumhoz
    + Ha a lépés befejeződik, a kimenetei a következő lépéshez és/vagy a Storage-ba írt bemenetként készülnek.
    + A már nem szükséges erőforrások véglegesítése és leválasztása

![Folyamat lépései](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Folyamatok fejlesztése a Python SDK-val

A [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-ban a folyamat egy Python-objektum, amely a `azureml.pipeline.core` modulban van definiálva. A [folyamat](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objektum egy vagy több [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) -objektum rendezett sorrendjét tartalmazza. Az `PipelineStep` osztály absztrakt, és a tényleges lépések alosztályok lesznek, például [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)vagy [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) osztály a folyamatok között megosztható lépések újrafelhasználható szakaszát tartalmazza. Egy `Pipeline` fut az a részeként `Experiment` .

Egy Azure Machine Learning munkaterülethez egy Azure ML-folyamat van társítva, és egy folyamat lépés társítva van egy, az adott munkaterületen belül elérhető számítási célhoz. További információ: Azure Machine Learning- [munkaterületek létrehozása és kezelése a Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) , illetve [Mik a Azure Machine learning számítási céljai?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

Azure Machine Learning a számítási cél az a környezet, amelyben egy ML-fázis történik. A szoftveres környezet lehet távoli virtuális gép, Azure Machine Learning számítás, Azure Databricks, Azure Batch stb. A hardveres környezet nagy mértékben is változhat, a GPU-támogatástól, a memóriától, a tárterülettől és így tovább. Megadhatja az egyes lépésekhez tartozó számítási célt, amely részletesen szabályozza a költségeket. A projekt adott műveletéhez, adatmennyiségéhez és teljesítményéhez szükséges több vagy kevésbé hatékony erőforrásokat is használhat. 

## <a name="building-pipelines-with-the-designer"></a>Folyamatok fejlesztése a tervezővel

A vizuális tervezési felületet előnyben részesített fejlesztők a Azure Machine Learning Designer használatával hozhatnak létre folyamatokat. Ehhez az eszközhöz a munkaterület kezdőlapján található **tervező** kijelölésen keresztül férhet hozzá.  A Designer lehetővé teszi, hogy a lépéseket a tervezési felületre húzza. A gyors fejlesztés érdekében a meglévő modulokat a ML-feladatok spektrumán keresztül használhatja; a meglévő modulok az adatok átalakítását és az algoritmus kiválasztását is lefedik az üzembe helyezéshez. Vagy létrehozhat egy teljesen egyéni folyamatot a Python-szkriptekben definiált saját lépések kombinálásával.

A folyamatok vizuális megtervezése során láthatók a lépések bemenetei és kimenetei. Az adatkapcsolatok húzásával gyorsan megismerheti és módosíthatja a folyamat adatfolyam.
 
![Azure Machine Learning Designer – példa](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>A végrehajtási gráf ismertetése

A folyamat lépései más lépésekkel is függőségekkel rendelkezhetnek. Az Azure ML-adatcsatorna szolgáltatás ezen függőségek elemzésével és összehangolásával dolgozik. Az eredményül kapott "végrehajtási gráf" csomópontjai a feldolgozási lépések. Az egyes lépések magukban foglalhatják a hardverek és szoftverek adott kombinációjának létrehozását vagy újbóli felhasználását, a gyorsítótárazott eredmények újrafelhasználását stb. A szolgáltatás előkészítési és optimalizálási folyamata jelentősen felgyorsíthatja az ML-fázist, és csökkentheti a költségeket. 

Mivel a lépések egymástól függetlenül futnak, a bemeneti és kimeneti adatokat tároló objektumokat külsőleg kell definiálni. Ez az [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)és a [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)szerepe, objektumok. Ezek az adatobjektumok egy [adattár](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) -objektumhoz vannak társítva, amely magában foglalja a tárolási konfigurációját. Az `PipelineStep` alaposztályt a rendszer mindig egy `name` sztringtel, egy listával `inputs` és egy listával hozza létre `outputs` . Általában az is szerepel a listán, `arguments` és gyakran is megjelenik a listája `resource_inputs` . Az alosztályok általában további argumentumokkal is rendelkeznek (például `PythonScriptStep` a futtatásához a parancsfájl fájlnevét és elérési útját kell megadni). 

A végrehajtási gráf aciklikus, de a folyamatok ismétlődő ütemterv szerint futtathatók, és futtathatnak olyan Python-parancsfájlokat, amelyek állapotinformációkat írhatnak a fájlrendszerbe, így összetett profilok hozhatók létre. Ha úgy tervezi a folyamatot, hogy bizonyos lépések párhuzamosan vagy aszinkron módon fussanak, Azure Machine Learning transzparens módon kezeli a függőségi elemzést és a ventilátorok és ventilátorok koordinálását. Általában nem kell foglalkoznia a végrehajtási gráf részleteivel, de a [folyamat. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) attribútumán keresztül érhető el. 


### <a name="a-simple-python-pipeline"></a>Egy egyszerű Python-folyamat

Ez a kódrészlet az alapszintű létrehozásához és futtatásához szükséges objektumokat és hívásokat mutatja be `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

A kódrészlet a Common Azure Machine Learning Objects, a, a `Workspace` `Datastore` , a [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)és a () karakterrel kezdődik `Experiment` . Ezután a kód létrehozza a tárolni kívánt objektumokat `input_data` `output_data` . A tömb `steps` egyetlen elemet `PythonScriptStep` tartalmaz, amely az adatobjektumokat fogja használni, és futtatja a parancsot `compute_target` . Ezután a kód maga hozza létre az `Pipeline` objektumot, és átadja a munkaterületet és a Steps tömböt. Az `experiment.submit(pipeline)` Azure ml-folyamat futtatásának megkezdésére irányuló hívás. A `wait_for_completion()` folyamat befejeződéséig a blokkoló hívás. 

Ha többet szeretne megtudni a folyamat adataihoz való csatlakoztatásáról, tekintse meg az [adathozzáférés a Azure Machine Learningban](concept-data.md) és az adatáthelyezés a következőbe: [ml-folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md)című cikk adatait. 

## <a name="best-practices-when-using-pipelines"></a>Ajánlott eljárások a folyamatok használatakor

Amint láthatja, az Azure ML-folyamat létrehozása valamivel összetettebb, mint egy parancsfájl elindítása. A folyamatokhoz néhány Python-objektum konfigurálása és létrehozása szükséges. 

Bizonyos helyzetekben, amelyek egy folyamat használatát sugallják:

* Csapat-környezetekben: az ML-feladatok több független lépésre oszthatók, így a fejlesztők egymástól függetlenül dolgozhatnak és bővíthetik a programjaikat. 

* Ha a vagy a közelében üzemelő példányban van: leszegezi a konfigurációt, és az ütemezett és eseményvezérelt műveletek használatával marad az adatok módosítása.

* Egy ML-projekt korai szakaszában, vagy önállóan működik: a buildek automatizálásához használjon folyamatokat. Ha már elkezdte a konfiguráció és a számítási állapot újbóli létrehozását az új ötlet megvalósítása előtt, akkor ez egy olyan jel, amelyet fontolóra vehet egy folyamat használatával a munkafolyamat automatizálásához. 

Könnyűvé válik a gyorsítótárazott eredmények újrafelhasználása, a számítási költségek részletes szabályozása és az elkülönítés feldolgozása, de a folyamatok költségei is megtalálhatók. Néhány példa a következőkre:

* A folyamatokat kizárólag különálló módon használhatja. A Python beépített funkciói, objektumai és moduljai hosszú utat biztosítanak a programozott állapot elkerüléséhez! A folyamat lépései sokkal drágábbak, mint a függvények hívása.

* Nagy mennyiségű kapcsolódás a folyamat lépései között. Ha egy függő lépést gyakran kell módosítani az előző lépés kimenetének módosítására, akkor valószínű, hogy a különálló lépések jelenleg több, mint egy előnyben részesülnek. Egy másik nyom, hogy a lépések túl vannak párosítva, olyan lépés argumentumai, amelyek nem adat, hanem a feldolgozás vezérlésére szolgáló jelzők. 

* A számítási erőforrások idő előtti optimalizálása. Előfordulhat például, hogy az adatok előkészítésének több szakasza is van, és az egyik gyakran "ó, itt van egy olyan hely, ahol `MpiStep` párhuzamos programozást használhatok, de itt van egy olyan hely, ahol egy `PythonScriptStep` kevésbé hatékony számítási célt használhatok," és így tovább. És lehet, hogy hosszú távon olyan részletes lépéseket hoz létre, mint például az, ami érdemes lehet, különösen, ha a gyorsítótárazott eredményeket nem mindig az újraszámítással szeretné használni. A folyamatok azonban nem helyettesíthetik a Python natív `multiprocessing` modulját. 

Amíg egy projekt nagy vagy közel kerül a központi telepítéshez, a folyamatoknak durvábbnak kell lenniük, nem pedig a részletesen. Ha úgy gondolja, hogy a ML-projekt a _szakaszokat_ és a folyamatokat is felhasználva teljes munkafolyamatot biztosít, amely egy adott fázison halad át, akkor a megfelelő elérési úton van. 

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Kulcs előnye|Description|
|:-------:|-----------|
|**Felügyelet nélküli &nbsp; futtatások**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. A rendelkezésre álló számítási erőforrások hatékony felhasználása a különböző számítási célokon, például a HDInsight, a GPU-adatelemzési virtuális gépeken és a Databricks futó egyes folyamatok lépéseinek futtatásával.|
|**Újrafelhasználhatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követés és verziószámozás**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
| **Modularitás** | Az érintett területek elkülönítése és a változások elkülönítése lehetővé teszi, hogy a szoftverek gyorsabb ütemben, magasabb színvonalú minőségben fejlődjenek. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>A megfelelő PipelineStep alosztály kiválasztása

A az `PythonScriptStep` absztrakt legrugalmasabb alosztálya `PipelineStep` . Más alosztályok, például `EstimatorStep` alosztályok, és `DataTransferStep` kevesebb kóddal rendelkező konkrét feladatokat is elvégezhetnek. Például `EstimatorStep` létrehozhat egy csak egy nevet a lépéshez, egy `Estimator` és egy számítási célra. Vagy felülbírálhatja a bemeneteket és kimeneteket, a folyamat paramétereit és az argumentumokat. További információ: a [modellek Betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md). 

A `DataTransferStep` lehetővé teszi az adatforrások és a mosdók közötti adatáthelyezést. Az átvitelhez szükséges kód egyszerű, de ismétlődő. Ehelyett egyszerűen létrehozhat egy `DataTransferStep` nevet, egy adatforrásra és egy adatfogadóra mutató hivatkozásokat, valamint egy számítási célt. A notebook [Azure Machine learning a DataTransferStep folyamata](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) ezt a rugalmasságot mutatja be.

## <a name="modules"></a>Modulok

Míg a folyamat lépései lehetővé teszik egy korábbi Futtatás eredményeinek újrafelhasználását, sok esetben a lépés felépítése feltételezi, hogy a szükséges parancsfájloknak és a függő fájloknak helyileg elérhetőnek kell lenniük. Ha egy adattudós már meglévő kód alapján kíván felépíteni, a szkripteket és a függőségeket gyakran külön tárházból kell klónozott.

A modulok a folyamat lépéseihez hasonlóan hasonlóak, de a munkaterületen elérhető verziószámozást biztosítanak, amely lehetővé teszi az együttműködés és a nagy léptékű használhatóság használatát. A modulok úgy lettek kialakítva, hogy több folyamaton újra felhasználhatók legyenek, és egy adott számítást különböző használati esetekhez igazítanak. A felhasználók a következő feladatokat végezhetik el a munkaterületen, külső adattárak használata nélkül:

* Új modulok létrehozása és a meglévő modulok új verzióinak közzététele
* Meglévő verziók elavulttá nyilvánítása
* Letiltott verziók megjelölése annak megakadályozása érdekében, hogy a felhasználók az adott verziót használják
* Alapértelmezett verziók kijelölése
* Modulok beolvasása a munkaterületről verzió alapján, annak biztosítása érdekében, hogy a csapatok ugyanazt a kódot használják

Az Azure Machine Learning-folyamatok moduljainak létrehozásához, összekapcsolásához és használatához szükséges példákért tekintse meg a [notebookot](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) .

## <a name="next-steps"></a>További lépések

Az Azure ML-folyamatok egy hatékony létesítmény, amely megkezdi az értékek megvalósítását a korai fejlesztési fázisokban. Az érték növekszik, ahogy a csapat és a projekt növekszik. Ez a cikk azt ismerteti, hogyan vannak megadva a folyamatok a Azure Machine Learning Python SDK-val és az Azure-ban. Megismerte az alapszintű forráskódot, és bevezette néhányat a `PipelineStep` rendelkezésre álló osztályokba. Érdemes lehet az Azure ML-adatfolyamatok és az Azure-t futtató folyamatok használatára. 


+ Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md).

+ Megtudhatja, hogyan [futtathat batch-előrejelzéseket nagy mennyiségű adattal](tutorial-pipeline-batch-scoring-classification.md ).

+ Tekintse meg az SDK-dokumentációt a [pipeline Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a [folyamat lépéseihez](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Próbálja ki például a [Azure Machine learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter notebookokat. Útmutató [jegyzetfüzetek futtatásához a szolgáltatás megismeréséhez](samples-notebooks.md).
