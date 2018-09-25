---
title: A valós életből vett prediktív karbantartás |} A Microsoft Docs
description: A valós életből vett példák segítségével PySpark prediktív karbantartás
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c154b0124acb5bee93211adb611356555526d2c0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996213"
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>A valós életből vett prediktív karbantartás

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



A berendezések váratlan üzemkimaradása minden cég számára káros lehet. Rendkívül fontos, hogy a mező berendezések kihasználtságát és a teljesítmény maximalizálása érdekében, illetve minimálisra csökkentheti a költséges, nem ütemezett üzemszünet futtatása. Problémák korai azonosítása segíthet korlátozott karbantartási erőforrásokat lefoglalni a költséghatékony és minőség javítása és ellátási lánc folyamatokat. 

Ez a forgatókönyv bemutatja egy viszonylag [szimulált nagy méretű adatkészlet](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) funkció bemutatására egy prediktív karbantartási adatelemzési projektjéhez az adatbetöltés, a mérnöki csapathoz, a modell létrehozásának és a modell operacionalizálás és a központi telepítés. A kód a teljes folyamat PySpark az Azure Machine Learning Workbench használatával a Jupyter Notebook nyelven van megírva. A kész modell Azure Machine Learning Modellkezelés használatával valós idejű berendezések meghibásodása adatelemzésre van telepítve.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-adattár

A Cortana Intelligence Gallery a PM-oktatóanyaghoz egy nyilvános GitHub-adattárból ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)), amelyen problémák jelentése és közreműködés.


## <a name="use-case-overview"></a>Használati eset – áttekintés

Üzletekre az eszközintelligencia-(nagy erőforrásigényű) iparágak szerint terhet jelentős probléma az késleltetések miatt vehesse a műszaki problémákat rendelt jelentős költségeit. A legtöbb vállalat szeretne előrejelzésére, ha ezek a problémák merülhetnek fel, amelyek segítségével proaktív módon megelőzheti azokat, mielőtt bekövetkeznének. A célja, hogy a költségek csökkentése az állásidő csökkentésével és felülbírálásáról biztonságát. 

Ebben a forgatókönyvben az ötleteit vesz igénybe a [prediktív karbantartási forgatókönyveket](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) bemutatni, hogyan hozhat létre egy szimulált adatkészlet egy prediktív modellt. A példaadatokat származik sok prediktív karbantartási használati esetek megfigyelt elemeket.

Ezen szimulált adatok az üzleti problémát az összetevő hibák által okozott problémákat. Az üzleti kérdés "*Mi a valószínűsége annak, hogy a gép leáll egy összetevő hibája miatt?*" Ez a probléma vannak formázva, egy többcsoportos osztályozási probléma (gépenként több összetevő). Gépi tanulási algoritmus segítségével a prediktív modell létrehozásához. A modell tanítása gépekről gyűjtött előzményadatok. Ebben a forgatókönyvben a felhasználó végighalad a modell megvalósításához a Machine Learning Workbench-környezetben különböző lépéseket.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
* Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Kövesse a [rövid telepítési útmutatójában](quickstart-installation.md) telepítse a programot, és hozzon létre egy munkaterületet.
* Az Azure Machine Learning Operacionalizálás megköveteli egy helyi környezet és a egy [Azure Machine Learning Modellkezelés-fiók](model-management-overview.md).

Ebben a példában minden Machine Learning Workbench számítási környezetben futtatja. Azonban azt javasoljuk, hogy a példa Futtatás legalább 16 GB memóriája. Ebben a forgatókönyvben beépített és a egy távoli DS4_V2 standard futó Windows 10-es gépre tesztelt [adatelemzési virtuális gép (DSVM) Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modell operacionalizálás verzió 0.1.0a22, az Azure Machine Learning parancssori felület használatával végezhető el.

## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:
1.  Nyissa meg a Machine Learning Workbenchet.
2.  Az a **projektek** lapon jelölje be **+**, majd válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt.
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Prediktív karbantartási", és válassza ki a **prediktív karbantartási** sablont.
5.  Kattintson a **Létrehozás** gombra.

## <a name="prepare-the-notebook-server-computation-target"></a>A notebook server számítási cél előkészítése

A helyi gépén, a Machine Learning Workbench futtatása **fájl** menüben válassza **parancssor megnyitása** vagy **megnyitott PowerShell parancssori felület**. A parancssori felületen elérheti az Azure-szolgáltatások használatával lehetővé teszi a `az` parancsokat. Első lépésként jelentkezzen be az Azure-fiókjával a paranccsal:

```
az login
``` 

Ez a parancs biztosítja a https használata hitelesítési kulcs:\\aka.ms\devicelogin URL-CÍMÉT. A parancssori felület vár, amíg az eszköz bejelentkezési művelet adja vissza, és néhány kapcsolódási információkat szolgáltat a. Mellett, ha egy helyi [Docker](https://www.docker.com/get-docker) telepítés, a paranccsal a helyi számítási környezet előkészítése:

```
az ml experiment prepare --target docker --run-configuration docker
```

Ezért célszerű futtatni egy [DSVM Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) a memória-és. A dsvm-hez van konfigurálva, miután előkészítése az alábbi két parancsot a távoli Docker-környezetben:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Miután csatlakozott a távoli Docker-tárolót, a parancs DSVM Docker számítási környezet előkészítése: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

A Docker számítási környezetet előkészített, nyissa meg a Jupyter notebook server az Azure Machine Learning Workbench **notebookok** lapon, vagy egy webböngésző-alapú kiszolgálón indítsa el a paranccsal: 

```
az ml notebook start
```

A példa notebookok az kódjának könyvtárában tárolódnak. A notebookok beállítása lefutni, az első (Code\1_data_ingestion.ipynb) notebook indítása. Minden egyes notebook megnyitásakor, válassza ki a számítási kernel kéri. Válassza ki a [projektnév] [Connection_Name] _sablon kernel a korábban konfigurált DSVM hajthat végre.

## <a name="data-description"></a>Adatok leírása

A [szimulált adatok](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) öt vesszővel tagolt (.csv) fájlból áll. Az alábbi hivatkozások segítségével első adatkészletek kapcsolatos részletes leírását.

* [Gépek](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): jellemzőket, például az életkor és a modell egyes gépek megkülönböztetésére.
* [Hiba](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): A hibanapló vannak lépett fel, miközben továbbra is működik a gép nem törhető hibákat tartalmazza. Ezek a hibák nem tekinthetők hibák, bár prediktív jövőbeli hibák esemény is lehet. Hibák dátum-idő értékek vannak kerekítve a legközelebbi óra, mivel a telemetriai adatok gyűjtése óránkénti.
* [Karbantartási](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): A karbantartási naplót mindkét ütemezett és nem tervezett karbantartás rekordokat tartalmaz. Ütemezett karbantartási megfelel-e a rendszeres időközönkénti összetevők vizsgálata. Nem ütemezett karbantartási gépi hiba vagy más teljesítménycsökkenés merülhetnek fel. A dátum-idő értékek karbantartás vannak kerekítve a legközelebbi óra, mivel a telemetriai adatok gyűjtése óránkénti.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): A telemetriai adatokat a processzoridővel sorozat minden gépen több érzékelőadatok áll. Az adatok naplózta érzékelő értékek átlaga egyes egy óra alatt.
* [Hibák](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): hibák összetevő cseréjére belül a karbantartási naplót felelnek meg. Minden rekord tartalmazza a gép azonosítója, összetevőtípus, és a helyettesítő dátuma és időpontja. Ezeket a rekordokat a machine learning-címkék, amelyek a modell előre jelezni próbál létrehozására szolgálnak.

Töltse le a nyers adatok beállítása a GitHub-adattárból, és hozzon létre ehhez az elemzéshez PySpark adatkészletek, tekintse meg a [adatbetöltés](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebookot a forgatókönyvben a kód mappában.

## <a name="scenario-structure"></a>A forgatókönyv-struktúra
A tartalom a forgatókönyvhöz érhető el: a [GitHub-adattár](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

A [információs](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fájl ismerteti az adatok előkészítése, jön létre a modell és a gyártási megoldás majd telepítése a munkafolyamatot. A munkafolyamat egyes lépések a Jupyter notebook van beágyazva a [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) mappán belül a tárházban.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Ez a jegyzetfüzet letölti az öt bemeneti .csv-fájlt, és néhány előzetes adatok karbantartása és a Vizualizáció. A notebook PySpark formátumra alakítja át minden egyes adatkészlethez, és tárolja azokat egy Azure blob-tárolóban, a szolgáltatás mérnöki jegyzetfüzet használatra.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): megközelítéssel téli idő sorozat telemetriai adatokat, a hibák és a karbantartási adatokat a modell funkciók tevődnek össze a nyers adatok beállítása az Azure Blob storage-ból. Az összetevő hiba kapcsolatos cseréjére segítségével hozza létre a model címkék, amelyek bemutatják, hogy melyik összetevő működése sikertelen volt. A címkézett funkció mentett adatok az Azure-blobba a modell létrehozása notebook.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): modell létrehozása a Notebook a címkézett szolgáltatás adatokat használja, és az adatok felosztja a tanítási és fejlesztési adatkészletek a dátum-idő bélyeg mentén. A notebook pyspark.ml.classification modellel próbaképpen van beállítva. A betanítási adatok vectorized van. A felhasználó is kipróbálhat akár egy **DecisionTreeClassifier** vagy **RandomForestClassifier** , keresse meg a legjobban teljesítő modellt hiperparaméterek módosítására. Teljesítmény kiértékelése mérési statisztikák a fejlesztési adatkészlet határozza meg. A statisztikák nyomon követésére, a Machine Learning Workbench futásidejű képernyő vissza a rendszer naplózza. Mindegyik futtatás során a notebookot az eredményül kapott modell a helyi lemezen, amelyen fut a Jupyter notebook kernel menti. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Ez a jegyzetfüzet hozhat létre egy Azure-WebApp üzembe a modell üzembe helyezésével összetevői a helyi (Jupyter notebook kernel) lemezre mentett legutóbbi modellt használja. A teljes működési eszközöket egy másik Azure blob-tárolóban tárolt o16n.zip fájlba vannak tömöríteni. A tömörített fájl tartalmazza:

* **service_schema.JSON**: az üzemelő példány definíciója sémafájl. 
* **pdmscore.PY**: A **init()** és **run()** , amelyek szükségesek az Azure functions webes szolgáltatás.
* **pdmrfull.Model**: A modell definícióját könyvtárat.
    
A notebook teszteli a modell-definíciót, mielőtt csomagolására, üzembe helyezéshez operacionalizálás eszközök együttműködik. A notebook végén található központi telepítési utasításokat is.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben egy teljes körű prediktív karbantartási megoldás kialakításához PySpark a Jupyter Notebook környezetben, a Machine Learning Workbench használatával áttekintést nyújt. Ebben a példaforgatókönyvben a modell-üzembehelyezés, valós idejű berendezések meghibásodása előrejelzéseket a Machine Learning Modellkezelés környezetben keresztül is részletezi.

## <a name="references"></a>Referencia

A következő hivatkozásokat biztosít, más prediktív karbantartási példák használati esetek különböző platformokhoz:

* [A prediktív karbantartási megoldás – sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Prediktív karbantartás Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktív karbantartás modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Útmutató Python-jegyzetfüzetet a modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark prediktív karbantartás](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Mélytanulás a prediktív karbantartás](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>További lépések

Más példaforgatókönyvek bemutatják a termék további funkciók, a Machine Learning Workbench alkalmazásban érhető el. 
