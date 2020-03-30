---
title: Mik azok az ML-folyamatok?
titleSuffix: Azure Machine Learning
description: Ebben a cikkben ismerje meg a gépi tanulási (ML) folyamatok az Azure Machine Learning SDK pythonhoz hozhat létre előnyeit. A gépi tanulási folyamatokat adatszakértők használják a gépi tanulási munkafolyamatok létrehozásához, optimalizálásához és kezeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: da45c0db027dffc89bd058b70331a4bd6d093b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336964"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok az Azure Machine Learning-folyamatok?

Az Azure Machine Learning-folyamatok lehetővé teszik, hogy munkafolyamatokat hozzon létre a gépi tanulási projektekben. Ezek a munkafolyamatok számos előnnyel járnak: 

+ Egyszerűség
+ Sebesség
+ Ismételhetőség
+ Rugalmasság
+ Verziószámozás és nyomon követés
+ Modularitás 
+ Minőségbiztosítás
+ Költségellenőrzés

Ezek az előnyök akkor válnak jelentőssé, amikor a gépi tanulási projekt túllép a tiszta feltáráson és az iteráción. Még az egyszerű, egylépéses csővezetékek is értékesek lehetnek. A gépi tanulási projektek gyakran összetett állapotban vannak, és megkönnyebbülés lehet, ha egyetlen munkafolyamat pontos megvalósítása triviális folyamat.

További információ [az első folyamat létrehozásáról.](how-to-create-your-first-pipeline.md)

![Gépi tanulási folyamatok az Azure Machine Learningben](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-folyamattechnológiát használjak?

Az Azure-felhő számos más folyamatot biztosít, amelyek mindegyike más céllal rendelkezik. Az alábbi táblázat a különböző folyamatokat és azok használt céljait sorolja fel:

| Forgatókönyv | Elsődleges személyiség | Azure-ajánlat | OSS ajánlat | Kanonikus cső | Erősségeit | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modell vezénylése (gépi tanulás) | Adatelemző | Azure Machine Learning-folyamatok | Kubeflow-csővezetékek | Adatok -> modell | Elosztás, gyorsítótárazás, kódelső, újrafelhasználás | 
| Adatvezénylés (Adatelőkészítés) | Adatszakértő | [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache légáramlás | Adatok -> adatok | Erősen gépelt mozgás. Adatközpontú tevékenységek. |
| Kód & alkalmazás vezénylése (CI/CD) | Alkalmazásfejlesztő / Ops | [Azure DevOps-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kód + Model -> alkalmazás/szolgáltatás | A legtöbb nyitott és rugalmas tevékenységtámogatás, jóváhagyási sorok, fázisok a gating | 


## <a name="what-can-azure-ml-pipelines-do"></a>Mit tehetnek az Azure ML-folyamatok?

Az Azure Machine Learning-folyamat egy teljes gépi tanulási feladat egymástól függetlenül végrehajtható munkafolyamata. Az alfeladatok lépések sorozataként vannak beágyazva a folyamatba. Az Azure Machine Learning-folyamat lehet olyan egyszerű, mint az egyik, amely meghívja a Python-parancsfájlt, így lehet, _hogy_ bármit. A _folyamatoknak_ a gépi tanulási feladatokra kell összpontosítaniuk, például:

+ Adat-előkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a kezelést és az átalakítást, a normalizálást és az előkészítést
+ Betanítás konfigurálása, beleértve az argumentumok, a fájlok elérési útvonalai és a naplózási/jelentéskészítési konfigurációk paraméterezését
+ Képzés és érvényesítése hatékonyan és többször. A hatékonyság származhat konkrét adathalmazok, különböző hardveres számítási erőforrások, elosztott feldolgozás és folyamatfigyelés megadásából
+ Üzembe helyezés, beleértve a verziószámozást, a méretezést, a kiépítést és a hozzáférés-vezérlést 

A független lépések lehetővé teszik, hogy több adattudós dolgozzon ugyanazon a folyamaton egyszerre anélkül, hogy túlterhelné a számítási erőforrásokat. A különálló lépések megkönnyítik a különböző számítási típusok/méretek használatát az egyes lépésekhez.

A folyamat tervezése után gyakran több finomhangolása körül a betanítási hurok a folyamat körül. Folyamat újrafuttatásakor a futtatás az újrafuttatandó lépésekre, például egy frissített betanítási parancsfájlra ugrik. A program kihagyja azújrafuttatandó lépéseket. Ugyanez az elemzés vonatkozik a lépés megvalósításához használt változatlan parancsfájlokra is. Ez az újrafelhasználási funkció segít elkerülni a költséges és időigényes lépések, például az adatok betöltését és az átalakítást, ha az alapul szolgáló adatok nem változtak.

Az Azure Machine Learning segítségével különböző eszközkészleteket és keretrendszereket használhat, például a PyTorch vagy a TensorFlow a folyamat minden egyes lépéséhez. Az Azure koordinálja a különböző [számítási célokat,](concept-azure-machine-learning-architecture.md) így a köztes adatok megoszthatók az alsóbb rétegbeli számítási célokkal.

A [folyamatkísérletek metrikáit](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) közvetlenül az Azure Portalon vagy a [munkaterület céloldalában (előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után konfigurálhat egy REST-végpontot, amely lehetővé teszi a folyamat bármely platformról vagy veremből történő újrafuttatását.

Röviden, a gépi tanulási életciklus összes összetett feladata segítséget kaphat a folyamatokban. Más Azure-folyamattechnológiák saját erősségei, például [az Azure Data Factory folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) az adatokkal való munka és az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) folyamatos integráció és üzembe helyezés érdekében. Ha azonban a gépi tanulásra összpontosít, az Azure Machine Learning-folyamatok valószínűleg a legjobb választás a munkafolyamat-igényeknek megfelelően. 

## <a name="what-are-azure-ml-pipelines"></a>Mik azok az Azure ML-folyamatok?

Az Azure ML-folyamat teljes logikai munkafolyamatot hajt végre a lépések rendezett sorozatával. Minden lépés egy különálló feldolgozási művelet. A folyamatok egy Azure Machine Learning-kísérlet környezetében [futnak.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

Egy gépi tanulási projekt korai szakaszában, akkor rendben van, hogy egyetlen Jupyter notebook vagy Python-parancsfájl, amely az Azure-munkaterület és az erőforrás-konfiguráció, az adatok előkészítése, a futtatási konfiguráció, a betanítás és az érvényesítés. De ahogy a függvények és osztályok gyorsan válnak előnyösebb egyetlen kötelező kódblokk, ML munkafolyamatok gyorsan válnak előnyösebb egy monolitikus notebook vagy script. 

Modularizáló ML feladatok, csővezetékek támogatja a Computer Science elengedhetetlen, hogy egy összetevő kell "nem (csak) egy dolog is." Modularitás egyértelműen létfontosságú a projekt sikerét, amikor a programozás csapatokban, de még akkor is, ha egyedül dolgozik, még akkor is, ha egyedül dolgozik, még egy kis ML projekt magában foglalja a különböző feladatok, mindegyik egy jó adag összetettsége. A feladatok a következők: munkaterület-konfiguráció és adathozzáférés, adatelőkészítés, modelldefiníció és -konfiguráció, valamint központi telepítés. Míg egy vagy több feladat kimenetei alkotják a bemeneteket egy másikba, az egyik feladat pontos megvalósítási részletei a legjobb esetben is lényegtelen zavaró tényezők a következőben. Legrosszabb esetben az egyik feladat számítási állapota hibát okozhat egy másikban. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztéma rendelkezik olyan eszközökkel, amelyek koordinálják az erőforrás-, könyvtár- vagy összeállítási függőségeket. Ezek az eszközök általában fájlidőbélyegeket használnak a függőségek kiszámításához. Ha egy fájl módosul, csak azt és annak eltartottjait frissítik (letöltik, újrafordítják vagy csomagolják). Az Azure ML-folyamatok jelentősen kiterjesztik ezt a koncepciót. A hagyományos buildeszközökhöz hasonlóan a folyamatok is kiszámítják a lépések közötti függőségeket, és csak a szükséges újraszámításokat hajtják végre. 

Az Azure ML-folyamatok függőségi elemzése kifinomultabb, mint az egyszerű időbélyegek. Minden lépés más hardver- és szoftverkörnyezetben is futtatható. Az adatok előkészítése időigényes folyamat lehet, de nem kell nagy méretű GPU-kkal rendelkező hardveren futtatni, bizonyos lépések operációsrendszer-specifikus szoftvert igényelhetnek, előfordulhat, hogy elosztott képzést szeretne használni, és így tovább. Míg a költségmegtakarítás optimalizálása források jelentős lehet, lehet elsöprő manuálisan zsonglőrködik a különböző variációk hardver és szoftver erőforrásokat. Ez még nehezebb megtenni, hogy anélkül, hogy valaha is hibát követ el az adatok között át lépések. 

A folyamatok megoldják ezt a problémát. Az Azure Machine Learning automatikusan vezényli a folyamatlépések közötti függőségeket. Ez a vezénylési lehetnek a Docker-lemezképek fel- és lebontása, a számítási erőforrások csatolása és leválasztása, valamint az adatok áthelyezése a lépések között konzisztens és automatikus módon.

### <a name="reusing-results"></a>Eredmények újrafelhasználása

Továbbá, a kimenet egy lépés, ha úgy dönt, újra fel lehet használni. Ha lehetőségként újrafelhasználást ad meg, és nincsenek újraszámítást kiváltó upstream függőségek, a folyamatszolgáltatás a lépés eredményeinek gyorsítótárazott verzióját fogja használni. Az ilyen újrafelhasználás jelentősen csökkentheti a fejlesztési időt. Ha összetett adat-előkészítési feladattal rendelkezik, valószínűleg gyakrabban futtatja újra, mint amennyi feltétlenül szükséges. A csővezetékek mentesítik Önt az aggodalomtól: ha szükséges, a lépés futni fog, ha nem, akkor nem.

Ezt a függőségelemzést, vezénylést és aktiválást az Azure Machine Learning kezeli, amikor `Experiment`egy `submit()` [folyamatobjektumot](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) példányosít, átadja egy nak, és meghívja. 

### <a name="coordinating-the-steps-involved"></a>A felmerülő lépések összehangolása

`Pipeline` Objektum létrehozásakor és futtatásakor a következő magas szintű lépések történnek:

+ A szolgáltatás minden egyes lépésnél kiszámítja a következő követelményeket:
    + Hardveres számítási erőforrások
    + Operációs rendszer erőforrásai (Docker-rendszerkép(ek))
    + Szoftverforrások (Conda / virtualenv függőségek)
    + Adatbevitel 
+ A szolgáltatás határozza meg a lépések közötti függőségeket, ami dinamikus végrehajtási grafikont eredményez
+ Amikor a végrehajtási grafikon minden csomópontja fut:
    + A szolgáltatás konfigurálja a szükséges hardver- és szoftverkörnyezetet (esetleg újrafelhasználja a meglévő erőforrásokat)
    + A lépés fut, amely naplózási és `Experiment` figyelési információkat tartalmaz a tartalmazó objektum
    + Amikor a lépés befejeződik, a kimenetek a következő lépés bemeneteként és/vagy a tárolóba írásban
    + A már nem szükséges erőforrások véglegesítésre és leválasztva

![Csővezeték lépései](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Folyamatok létrehozása a Python SDK-val

Az [Azure Machine Learning Python SDK-ban](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)egy folyamat `azureml.pipeline.core` egy Python-objektum a modulban definiálva. A [folyamatobjektum](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) egy vagy több [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) objektum rendezett sorozatát tartalmazza. Az `PipelineStep` osztály absztrakt, és a tényleges lépések olyan alosztályoklesznek, mint [az EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [pythonscriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)vagy [a DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) osztály a folyamatok között megosztható lépések újrafelhasználható sorozatát tartalmazza. A `Pipeline` fut részeként `Experiment`.

Az Azure ML-folyamat egy Azure Machine Learning-munkaterülethez van társítva, és egy folyamatlépés az adott munkaterületen elérhető számítási célhoz van társítva. További információ: [Azure Machine Learning-munkaterületek létrehozása és kezelése az Azure Portalon,](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) vagy mik azok a [számítási célok az Azure Machine Learningben?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

Az Azure Machine Learningben a számítási cél az a környezet, amelyben egy gépi tanulási fázis fordul elő. A szoftverkörnyezet lehet egy távoli virtuális gép, az Azure Machine Learning Compute, az Azure Databricks, az Azure Batch és így tovább. A hardverkörnyezet is nagymértékben változhat, a GPU-támogatástól, a memóriától, a tárhelytől és így tovább. Megadhatja a számítási cél minden lépésnél, amely lehetővé teszi a költségek részletes szabályozását. A projekt adott műveletéhez, adatmennyiségéhez és teljesítményigényeihez több vagy kevesebb hatékony erőforrást is használhat. 

## <a name="building-pipelines-with-the-designer"></a>Csővezetékek építése a tervezővel

A vizuális tervezési felületet kedvelő fejlesztők az Azure Machine Learning tervezője segítségével hozhatnak létre folyamatokat. Ezt az eszközt a munkaterület kezdőlapján található **Tervezőválasztásból** érheti el.  A tervező lehetővé teszi, hogy húzza a lépéseket rá a tervezési felületre. A gyors fejlesztés érdekében a meglévő modulokat a pénzmosási feladatok teljes spektrumában használhatja; a meglévő modulok mindent lefednek az adatátalakítástól az algoritmuskiválasztásán át a képzésen át a központi telepítésig. Vagy létrehozhat egy teljesen egyéni folyamatot a Python-parancsfájlokban definiált saját lépések kombinálásával.

Amikor vizuálisan tervez folyamatokat, a lépés bemenetei és kimenetei láthatóan jelennek meg. Az adatkapcsolatok húzásával gyorsan megértheti és módosíthatja a folyamat adatfolyamát.
 
![Példa az Azure Machine Learning tervezőjének](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>A végrehajtási grafikon ismertetése

A folyamaton belüli lépések más lépésektől is függhetnek. Az Azure ML-folyamatszolgáltatás végzi a függőségek elemzését és vezénylését. Az eredményül kapott "végrehajtási grafikon" csomópontjai lépések feldolgozása. Minden lépés magában foglalhatja a hardver és a szoftver egy adott kombinációjának létrehozását vagy újrafelhasználását, a gyorsítótárazott eredmények újrafelhasználását és így tovább. A szolgáltatás vezénylése és a végrehajtási grafikon optimalizálása jelentősen felgyorsíthatja az ML-fázist, és csökkentheti a költségeket. 

Mivel a lépések egymástól függetlenül futnak, a lépések között áramló bemeneti és kimeneti adatok tárolására irányuló objektumokat külsőleg kell definiálni. Ez a [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)és a kapcsolódó osztályok szerepköre. Ezek az adatobjektumok egy [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) objektumhoz vannak társítva, amely beágyazi a tárolási konfigurációjukat. Az `PipelineStep` alaposztály mindig karakterlánccal, `name` `inputs`a . `outputs` Általában, ez is egy `arguments` listát, és gyakran `resource_inputs`lesz egy lista . Az alosztályok általában további argumentumokkal is `PythonScriptStep` rendelkeznek (például a parancsfájl nevének és elérési útjának futtatásához szükséges). 

A végrehajtási grafikon aciklikus, de a folyamatok futtathatók ismétlődő ütemezés szerint, és futtathatók Python-parancsfájlok, amelyek állapotadatokat írhatnak a fájlrendszerbe, lehetővé téve összetett profilok létrehozását. Ha úgy tervezi meg a folyamatot, hogy bizonyos lépések párhuzamosan vagy aszinkron módon fussanak, az Azure Machine Learning transzparens módon kezeli a függőségi elemzést és a ventilátorok és a ventilátor-be- és a rajongói adatok koordinációját. Általában nem kell a végrehajtási grafikon részleteivel törődnie, de a [Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) attribútumon keresztül érhető el. 


### <a name="a-simple-python-pipeline"></a>Egy egyszerű Python-folyamat

Ez a kódrészlet az alapszintű `Pipeline`objektumok at és hívásokat jeleníti meg:

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

A kódrészlet gyakori Azure Machine Learning-objektumokkal kezdődik, a `Workspace`, a `Datastore`, a [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)és egy `Experiment`. Ezután a kód létrehozza `input_data` a `output_data`tartandó objektumokat és a . A `steps` tömb egyetlen elemet `PythonScriptStep` tartalmaz, amely az adatobjektumokat `compute_target`fogja használni, és a . Ezután a kód példányosodik az `Pipeline` objektum maga, halad a munkaterületen, és lépéseket tömb. Az Azure `experiment.submit(pipeline)` ML-folyamat futásának hívása. A letiltása `wait_for_completion()` a folyamat befejezéséig. 

Ha többet szeretne tudni a folyamat adatokhoz való csatlakoztatásáról, olvassa el az [Adatok elérése](how-to-access-data.md) és az [adatkészletek regisztrálása című cikkeket.](how-to-create-register-datasets.md) 

## <a name="best-practices-when-using-pipelines"></a>Gyakorlati tanácsok a folyamatok használatakor

Mint látható, egy Azure ML-folyamat létrehozása egy kicsit bonyolultabb, mint egy parancsfájl indítása. A folyamatok néhány Python-objektum konfigurálását és létrehozását igénylik. 

Egyes helyzetek, amelyek egy csővezeték használatát javasolják:

* Csapatkörnyezetben: ossza fel az ML-feladatokat több független lépésre, hogy a fejlesztők egymástól függetlenül dolgozhassanak és fejleszthessék programjaikat. 

* Üzembe helyezéskor vagy annak közelében: szög le a konfigurációt, és az ütemezett és eseményvezérelt műveleteket, hogy felfüggeszti a változó adatok.

* Egy gépi tanulási projekt korai szakaszában vagy egyedül dolgozik: használjon folyamatokat a build automatizálásához. Ha egy új ötlet megvalósítása előtt elkezdte újra létrehozni a konfigurációs és számítási állapotot, ez egy olyan jel, amely et érdemes lehet egy folyamat használatával automatizálni a munkafolyamatot. 

A gyorsítótárazott eredmények újrafelhasználása, a számítási költségek részletes szabályozása és a folyamatelkülönítés könnyen lelkesedhet, de a folyamatoknak vannak költségeik. Néhány anti-minták közé tartozik:

* A csővezetékek használata az aggodalmak elkülönítésére. A Python beépített funkciói, objektumai és moduljai hosszú utat tesznek meg, hogy elkerüljék a zavaros programszerű állapotot! A folyamatlépés sokkal drágább, mint egy függvényhívás.

* Nehéz kapcsolás a csővezeték lépcsői között. Ha egy függő lépés újrabontása gyakran szükséges módosítani az előző lépés kimeneteit, valószínű, hogy a külön lépések jelenleg több költség, mint a haszon. Egy másik nyom, hogy a lépések túlságosan összekapcsolt az érveket egy lépés, amely nem adat, hanem jelzők ellenőrzésére feldolgozás. 

* A számítási erőforrások idő előtti optimalizálása. Például, gyakran több szakaszból áll az adatok előkészítése, és gyakran látni "Ó, `MpiStep` itt van egy hely, ahol tudnám használni `PythonScriptStep` a párhuzamos programozás, de itt van egy hely, ahol tudnám használni a kevésbé erős számítási cél," és így tovább. És talán, hosszú távon, ami finom szemcsés lépéseket, mint hogy érdemesnek bizonyulhat, különösen, ha van egy lehetőség, hogy használja a gyorsítótárazott eredmények helyett mindig újraszámítása. A folyamatok azonban nem helyettesítik a Python `multiprocessing` natív modulját. 

Amíg egy projekt nagy méretű lesz, vagy a telepítéshez közeledik, a folyamatoknak durvábbnak kell lenniük, nem pedig részletesen. Ha úgy gondolja, hogy az ml-projekt _szakaszokat_ és egy folyamatot foglal magában, amely teljes munkafolyamatot biztosít egy adott szakaszban való átszállításhoz, akkor jó úton jár. 

## <a name="key-advantages"></a>A legfontosabb előnyök

A folyamatok gépi tanulási munkafolyamatokhoz való használatának fő előnyei a következők:

|Kulcselőny|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;futások**|Ütemezze a lépéseket, hogy megbízható és felügyelet nélküli módon párhuzamosan vagy sorrendben fusson. Az adatok előkészítése és modellezése napokig vagy hetekig is eltarthat, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Heterogén számítás**|Használjon több folyamatot, amelyek megbízhatóan vannak koordinálva heterogén és méretezhető számítási erőforrások és tárolási helyek között. A rendelkezésre álló számítási erőforrások hatékony kihasználása az egyes folyamatlépések futtatásával a különböző számítási célok, például a HDInsight, a GPU Data Science virtuális gépek és databricks.|
|**Újrafelhasználhatóság**|Hozzon létre folyamatsablonokat adott forgatókönyvekhez, például átképzéshez és kötegelt pontozáshoz. Egyszerű REST-hívásokon keresztül aktiválja a közzétett folyamatokat a külső rendszerekről.|
|**Nyomon követés és verziószámozás**|Ahelyett, hogy manuálisan követené az adatokat és az eredményútvonalakat, miközben iterate, használja a folyamatok SDK explicit módon nevét és verzióját az adatforrások, bemenetek és kimenetek. A parancsfájlokat és az adatokat külön is kezelheti a nagyobb termelékenység érdekében.|
| **Modularitás** | Az aggodalomra okot adó területek elkülönítése és a változások elkülönítése lehetővé teszi a szoftverek gyorsabb, jobb minőségű fejlődését. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy együttműködjenek a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamatlépésein.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>A megfelelő PipelineStep alosztály kiválasztása

Az `PythonScriptStep` az absztrakt `PipelineStep`legrugalmasabb alosztálya. Más alosztályok, például `EstimatorStep` alosztályok, és `DataTransferStep` kevesebb kóddal képesek bizonyos feladatokelvégzésére. Például egy `EstimatorStep` hozható létre egyszerűen halad egy nevet a `Estimator`lépés, egy , és egy számítási cél. Vagy felülbírálhatja a bemeneteket és kimeneteket, a csővezeték-paramétereket és az argumentumokat. További információ: [Modellek betanítása az Azure Machine Learning használatával becslés sel](how-to-train-ml-models.md)című témakörben talál. 

A `DataTransferStep` lehetővé teszi az adatok áthelyezését az adatforrások és a fogadók között. A manuálisan ehhez szükséges kód egyszerű, de ismétlődő. Ehelyett létrehozhat egy `DataTransferStep` nevet, egy adatforrásra és egy adatgyűjtőre, valamint egy számítási célt. A notebook [Azure Machine Learning Pipeline a DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) bemutatja ezt a rugalmasságot.

## <a name="modules"></a>Modulok

Míg a folyamat lépései lehetővé teszik egy korábbi futtatás eredményeinek újrafelhasználását, sok esetben a lépés létrehozása feltételezi, hogy a szükséges parancsfájlok és függő fájlok helyileg elérhetőnek kell lenniük. Ha egy adattudós a meglévő kódra szeretne építeni, a parancsfájlokat és a függőségeket gyakran egy külön tárházból kell klónozni.

A modulok használata hasonló a folyamatlépésekhez, de a munkaterületen keresztül könnyített verziószámozást biztosít, ami lehetővé teszi az együttműködést és az újrafelhasználhatóságot nagy léptékben. A modulokat úgy tervezték, hogy több csővezetékben is újra felhasználhatók legyenek, és úgy fejlődhetnek, hogy egy adott számítást a különböző felhasználási esetekhez igazítsanak. A felhasználók a következő feladatokat tehetik meg a munkaterületen keresztül, külső adattárak használata nélkül:

* Új modulok létrehozása és a meglévő modulok új verzióinak közzététele
* Meglévő verziók elavulta
* A verziók letiltásának megjelölése, hogy a fogyasztók ne használják az adott verziót
* Alapértelmezett verziók kijelölése
* Modulok lekérése verzió szerint a munkaterületről, annak biztosítása érdekében, hogy a csapatok ugyanazt a kódot használják

Tekintse meg a [jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) a kód példákat, hogyan hozhat létre, csatlakoztathat és használhat modulokat az Azure Machine Learning-folyamatokban.

## <a name="next-steps"></a>További lépések

Az Azure ML-folyamatok egy hatékony létesítmény, amely megkezdi az érték biztosítását a korai fejlesztési szakaszban. Az érték növekszik a csapat és a projekt növekedésével. Ez a cikk ismerteti, hogyan vannak megadva a folyamatok az Azure Machine Learning Python SDK-val, és hogyan vannak megadva az Azure-ban. Láttál néhány alapvető forráskódot, és bevezették `PipelineStep` néhány, a rendelkezésre álló osztályok. Meg kell egyfajta, hogy mikor kell használni az Azure ML-folyamatok, és hogyan azure fut őket. 


+ További információ [az első folyamat létrehozásáról.](how-to-create-your-first-pipeline.md)

+ Ismerje meg, hogyan [futtathat kötegelt előrejelzéseket nagyméretű adatokon.](tutorial-pipeline-batch-scoring-classification.md )

+ Tekintse meg az SDK referencia-dokumentumait a [csővezetékmag](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a [csővezeték lépéseiről.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

+ Próbálja ki például az [Azure Machine Learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter-jegyzetfüzeteket. Ebből a [szolgáltatásból megtudhatja, hogy miként futtathat jegyzetfüzeteket a szolgáltatás felfedezéséhez.](samples-notebooks.md)
