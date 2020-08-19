---
title: Mik azok a Azure Machine Learning folyamatok?
description: Ismerje meg, hogyan hozhat létre, optimalizálhat és kezelhet gépi tanulási munkafolyamatokat a Machine learning (ML) folyamatokban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 08/17/2020
ms.custom: devx-track-python
ms.openlocfilehash: a62d12aa92e41d4a91f963d962616af11d917195
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604465"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok a Azure Machine Learning folyamatok?

Ebből a cikkből megtudhatja, hogyan segíti a Azure Machine Learning folyamatokat a gépi tanulási munkafolyamatok létrehozásában, optimalizálásában és felügyeletében. Ezek a munkafolyamatok számos előnnyel rendelkeznek: 

+ Egyszerűség kedvéért
+ Sebesség
+ Ismételhetőség
+ Rugalmasság
+ Verziószámozás és nyomon követés
+ Modularitás 
+ Minőségbiztosítás
+ Cost Control

Ezek az előnyök jelentősek lesznek, amint a gépi tanulási projekt a tiszta feltáráson és az iteráción túl is halad. Még az egyszerű egylépéses folyamatok is hasznosak lehetnek. A gépi tanulási projektek gyakran összetett állapotban vannak, és az is lehetséges, hogy az egyetlen munkafolyamat pontos megvalósítását egy triviális folyamat teszi lehetővé.

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-adatcsatorna-technológiát használok?

Az Azure-felhő számos más folyamatot is biztosít, amelyek mindegyike más célra szolgál. A következő táblázat felsorolja a különböző folyamatokat, és azt, hogy mire szolgálnak:

| Forgatókönyv | Elsődleges persona | Azure-ajánlat | OSS-ajánlat | Kanonikus cső | Erősségeit | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modell-előkészítés (gépi tanulás) | Adattudós | Azure Machine Learning folyamatok | Kubeflow-folyamatok | Adat> modell | Eloszlás, gyorsítótárazás, kód – első, újrafelhasználás | 
| Adat-előkészítés (adat-előkészítés) | Adatmérnök | [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache légáram | Adat>i adatszolgáltatások | Szigorúan beírt mozgás, adatközpontú tevékenységek |
| Kód & alkalmazás-előkészítés (CI/CD) | Alkalmazás fejlesztője/Ops | [Azure DevOps-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model – > alkalmazás/szolgáltatás | A legtöbb nyitott és rugalmas tevékenység támogatása, jóváhagyási várólisták, fázisok és kapuzás | 

## <a name="what-can-azure-ml-pipelines-do"></a>Mit tehet az Azure ML-folyamatok?

Az Azure Machine Learning folyamat egy teljes gépi tanulási feladat egymástól független végrehajtható munkafolyamata. Az alfeladatok lépések sorozataként vannak beágyazva a folyamatba. Egy Azure Machine Learning folyamat olyan egyszerű lehet, amely egy Python-szkriptet hív meg, _így bármit elvégezhet._ A folyamatoknak a gépi tanulási feladatokra _kell_ összpontosítaniuk, például:

+ Adat-előkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a kezelést és az átalakítást, a normalizálást és az előkészítést
+ Betanítás konfigurálása, beleértve az argumentumok, a fájlok elérési útvonalai és a naplózási/jelentéskészítési konfigurációk paraméterezését
+ Hatékony és ismételt betanítás és ellenőrzés. A hatékonyság az adott adathalmazok, a különböző hardveres számítási erőforrások, az elosztott feldolgozás és a folyamat figyelésének megadásával lehetséges.
+ Üzembe helyezés, beleértve a verziószámozást, a méretezést, a kiépítést és a hozzáférés-vezérlést

A független lépések lehetővé teszik, hogy több adatszakértő egyszerre működjön ugyanazon a folyamaton, és ne legyenek túlterhelő számítási erőforrások. A külön lépések azt is megkönnyítik, hogy az egyes lépésekhez különböző számítási típusokat/méreteket lehessen használni.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újra futtatandó lépésekre ugrik, például egy frissített tanítási parancsfájlt. Az újrafuttatást nem igénylő lépéseket a rendszer kihagyja. 

A folyamatok esetében választhat, hogy különböző hardvereket használ a különböző feladatokhoz. Az Azure koordinálja az Ön által használt különböző [számítási célokat](concept-azure-machine-learning-architecture.md) , így a köztes adatok zökkenőmentesen áramlanak az alárendelt számítási célokba.

A [folyamat-kísérletek metrikáit](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) közvetlenül Azure Portal vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat újrafuttatását bármely platformról vagy veremből.

Röviden, a gépi tanulási életciklus összes összetett feladata segíthet a folyamatokban. Más Azure-adatcsatorna-technológiák saját erősségei vannak. Az adatkezelést és az [Azure-folyamatokat](https://azure.microsoft.com/services/devops/pipelines/) tartalmazó [Azure Data Factory folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a megfelelő eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ha azonban a hangsúly a gépi tanulás, a munkafolyamatok igényei szerint Azure Machine Learning folyamatok valószínűleg a legjobb választás. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztémához olyan eszközök tartoznak, amelyek az erőforrás-, a könyvtár-és a fordítási függőségeket hangolják össze. Ezek az eszközök általában a fájlok időbélyegét használják a függőségek kiszámításához. Egy fájl módosításakor a rendszer csak az IT-t és a hozzá tartozó függőket frissíti (letöltött, újrafordított vagy csomagolt). Az Azure ML-folyamatok kiterjesztik ezt a koncepciót. A hagyományos Build-eszközökhöz hasonlóan a folyamatok is kiszámítják a lépések közötti függőségeket, és csak a szükséges újraszámításokat hajtják végre. 

Az Azure ML-folyamatok függőségi elemzése bonyolultabb, mint az egyszerű időbélyegek. Előfordulhat, hogy minden lépés más hardver-és szoftver-környezetben fut. Az adatelőkészítés lehet időigényes folyamat, de nem kell hatékony GPU-val futtatni a hardveren, bizonyos lépésekhez szükség lehet az operációs rendszer-specifikus szoftverekre, érdemes lehet elosztott képzést használni, és így tovább. 

Azure Machine Learning automatikusan összehangolja a folyamat lépései közötti összes függőséget. Ez a beállítás magában foglalhatja a Docker-rendszerképek rögzítését, a számítási erőforrások csatlakoztatását és leválasztását, valamint az adatok konzisztens és automatikus módon történő áthelyezését.

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

## <a name="building-pipelines-with-the-designer"></a>Folyamatok fejlesztése a tervezővel

A vizuális tervezési felületet előnyben részesített fejlesztők a Azure Machine Learning Designer használatával hozhatnak létre folyamatokat. Ehhez az eszközhöz a munkaterület kezdőlapján található **tervező** kijelölésen keresztül férhet hozzá.  A Designer lehetővé teszi, hogy a lépéseket a tervezési felületre húzza. 

A folyamatok vizuális megtervezése során láthatók a lépések bemenetei és kimenetei. Az adatkapcsolatok húzásával gyorsan megismerheti és módosíthatja a folyamat adatfolyam.

![Azure Machine Learning Designer – példa](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Kulcs előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli &nbsp; futtatások**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. A rendelkezésre álló számítási erőforrások hatékony felhasználása a különböző számítási célokon, például a HDInsight, a GPU-adatelemzési virtuális gépeken és a Databricks futó egyes folyamatok lépéseinek futtatásával.|
|**Újrafelhasználhatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követés és verziószámozás**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
| **Modularitás** | Az érintett területek elkülönítése és a változások elkülönítése lehetővé teszi, hogy a szoftverek gyorsabb ütemben, magasabb színvonalú minőségben fejlődjenek. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="next-steps"></a>Következő lépések

Az Azure ML-folyamatok egy hatékony létesítmény, amely megkezdi az értékek megvalósítását a korai fejlesztési fázisokban. Az érték növekszik, ahogy a csapat és a projekt növekszik. Ez a cikk azt ismerteti, hogyan vannak megadva a folyamatok a Azure Machine Learning Python SDK-val és az Azure-ban. Megismerte az alapszintű forráskódot, és bevezette néhányat a `PipelineStep` rendelkezésre álló osztályokba. Érdemes lehet az Azure ML-adatfolyamatok és az Azure-t futtató folyamatok használatára. 


+ Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md).

+ Megtudhatja, hogyan [futtathat batch-előrejelzéseket nagy mennyiségű adattal](tutorial-pipeline-batch-scoring-classification.md ).

+ Tekintse meg az SDK-dokumentációt a [pipeline Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a [folyamat lépéseihez](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Próbálja ki például a [Azure Machine learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter notebookokat. Útmutató [jegyzetfüzetek futtatásához a szolgáltatás megismeréséhez](samples-notebooks.md).
