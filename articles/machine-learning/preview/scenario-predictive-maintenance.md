---
title: "A prediktív karbantartási valós forgatókönyvekben |} Microsoft Docs"
description: "A prediktív karbantartási valós globális forgatókönyvben PySpark használatával"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 2687eb022bce0b71c217f0be611c8fabdfb66040
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>A prediktív karbantartási valós forgatókönyv.

A nem ütemezett berendezések állásidő hatását hátrányos bármely vállalati lehet. Fontos, ezért megtartása mező berendezéshez való használat és a teljesítmény maximalizálása érdekében fut és költséges, nem tervezett leállás csökkentésével. Korai problémákat azonosítása segíthet korlátozott karbantartási erőforrásokat költséghatékony és minőségének javítása és ellátási lánc folyamatokat. 

Ebben a forgatókönyvben használjuk egy viszonylag [nagy méretű adatok](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) lépésről-lépésre a felhasználó leállította a fő lépések az adatfeldolgozást, a mérnöki csapathoz, modell létrehozásának, és ezután végül modell operationalization és a központi telepítési beállítást. A kód a teljes folyamat PySpark nyelven van megírva, és Azure ML-munkaterületen Jupyter notebookok használatával megvalósított. Végül van operationalized a legjobb modell Azure Machine Learning modell Management használatával adja meg a megfelelő valós idejű hiba előrejelzéseket éles környezetben használható.   

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

Az alábbiakban látható a hivatkozásra kattintva a nyilvános GitHub-tárházban: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Használja az eset áttekintése

Eszköz-gyakori iparágakban a vállalatok által tapasztalt az is gépi problémák késések társított jelentős költségeket. A legtöbb vállalat érdekelt előrejelzésére, ha ezek a problémák merülhetnek fel ahhoz, hogy proaktív módon, hogy mielőtt bekövetkeznének. A cél, hogy a költségek csökkentése állásidő csökkentésével, és esetleg a biztonság fokozása. Tekintse meg a [alkalmazástervezési prediktív karbantartási](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) közös részletes leírását használja az esetek, valamint a prediktív karbantartási modellezési megközelítésben.

Ebben a forgatókönyvben a azzal a céllal, hogy a lépéseket forgatókönyvek esetében a több valós üzleti problémák összefoglaló alapuló a prediktív modell megvalósításához alkalmazástervezési ötleteket kihasználja. Ez a példa egyesíti közös adatelemek között számos prediktív karbantartási megfigyelt használati esetekben.

A szimulált adatok az üzleti probléma előre problémák okozta összetevőinek meghibásodása esetén. Az üzleti kérdésre, ezért nem "*mi annak, hogy az összetevő hibája miatt leáll a gép*?" Ez a probléma a multiclass osztályozási problémához (több összetevő gépenként) körbefuttatási, és a gépi tanulási algoritmus a prediktív modell létrehozásához használt. A modell betanítása a korábbi gépekről összegyűjtött adatokat. Ebben a forgatókönyvben a felhasználó végighalad számos lépés végrehajtási modell, az Azure Machine Learning-munkaterület környezeten belül.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/en-us/free/) (az ingyenes próbaverzió érhetők el).
* Egy telepített példánya [Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](./quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.
* Az Azure Machine Learning Operationalization megköveteli egy helyi környezet és a [modellhez tartozó felügyeleti fiók](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

Ebben a példában minden AML munkaterület számítási környezetben futtatható. Azonban ajánlott futtatni a legalább 16 GB memória. Ebben a forgatókönyvben tervezték és távoli DS4_V2 szabványos futó Windows 10-es gépre tesztelték [adatok tudományos virtuális gép Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modell operationalization tettük verzió 0.1.0a22 az Azure ML parancssori felület használatával.

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Prediktív karbantartási", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="prepare-the-notebook-server-computation-target"></a>Készítse elő a notebook server számítási cél

A helyi gépén, futtatását a AML munkaterület `File` menüben válassza a `Open Command Prompt` vagy `Open PowerShell CLI`. A parancssori felület windows belül hajtható végre a következő parancsokat:

`az ml experiment prepare --target docker --run-configuration docker`

Javasoljuk, hogy az adatok tudományos virtuális gépet futtató Linux (Ubuntu). Miután beállította a DSVM, futtassa az alábbi két parancsot:

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Az előkészített docker-lemezképekkel, nyissa meg a Jupyter notebook kiszolgáló, vagy a AML munkaterület notebookok lapon belül, vagy egy webböngésző-alapú kiszolgáló indításához futtassa: `az ml notebook start`.

Notebookok tárolódnak a `Code` könyvtár a Jupyter környezeten belül. Azt a notebookok egymás után futnak, az első indítása (`Code\1_data_ingestion.ipynb`) notebookot. Minden egyes notebook megnyitásakor kéri a számítási kernel kiválasztásához. Válassza ki a [Desired_Connection_Name] [projektnév] _Template, és kattintson a Kernel beállítása.

## <a name="data-description"></a>Adatok leírása

A [adatok szimulált](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) öt vesszővel tagolt (.csv) fájlt tartalmaz. További adatkészletek részletes leírása az alábbi hivatkozásokat követve.

* [Gépek](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): megkülönböztetése az egyes szolgáltatások. Például a korszűrő és a modell.
* [Hiba](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): A hiba a napló tartalmazza nem törhető hiba lépett fel, amíg a számítógép továbbra is működik. Ezek a hibák nem számít hibák, abban az esetben, ha azokat a jövőbeli hibaeseményt lehet. Hiba történt a dátum-idő vannak kerekítve a legközelebbi óra, mivel a telemetriai adatok gyűjtése óránkénti ütemben.
* [Karbantartási](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): A karbantartási naplót mindkét ütemezett és nem ütemezett karbantartás rekordokat tartalmaz. Ütemezett karbantartási összetevők rendszeres ellenőrzéssel megfelel, a tervezett karbantartást gépi hibák vagy egyéb teljesítménycsökkenés lépnek fel. Mivel a telemetriai adatok gyűjtése az óránkénti díj a karbantartás ideje a legközelebbi óra vannak kerekítve.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): A telemetria-idősoros adatok feszültség, elforgatás, nyomás és vibráció érzékelő mérések gyűjtött valós idejű minden gép áll. Az adatok egy órán átlagolva és tárolja a telemetriai adatok naplók
* [Hibák](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): hibák összetevő cserékhez belül a karbantartási naplót felel meg. Minden rekordot a gép Azonosítóját, összetevőtípust, és helyettesítő dátumát és idejét tartalmazza. Ezeket a rekordokat a gépi tanulási a címkéket, hogy a modell előrejelzése létrehozásához használt.

Tekintse meg a [adatfeldolgozást](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook forgatókönyv nyers adatkészletek letölthető a GitHub-tárházban, és hozzon létre a PySpark adatkészletek ehhez az elemzéshez kód szakaszban.

## <a name="scenario-structure"></a>A forgatókönyv struktúra
A forgatókönyv esetében a tartalom érhető el: a [GitHub-tárházban](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

A tárház van egy [információs](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fájlt, amely ismerteti a folyamatok néhány modellek létrehozása, és végül végrehajtott a egyik legjobb modell csak az adatok előkészítésétől. A négy Jupyter notebookok érhetők el a [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) a tárház mappában.   

Ezután azt írják le a részletes forgatókönyv munkafolyamat. A végpontok közötti forgatókönyv PySpark nyelven van megírva, és négy notebookok oszlik:

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Ez a notebook az öt bemeneti .csv fájlokat tölti le, nem néhány előzetes adatok karbantartása és a képi megjelenítés. A notebook PySpark formátumra alakítja át az adatokat, és tárolja az eredményeket egy Azure blob-tároló, a szolgáltatás Mérnöki tevékenység használható.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): A telemetriai adatok érzékelők és a hiba száma, az összetevő cserékhez a megtisztított adathalmazt, az előző lépésben, lag és összesített szolgáltatások használatával jön létre, a telemetriai adatok tartományhoz gép adatait. A címkék leíró, hogy melyik összetevő nem tudta összeállítani az összetevő hiba kapcsolatos új szolgálnak. A címkézett szolgáltatás adatok mentése az Azure BLOB feladat létrehozása a modell.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): A címkézett szolgáltatás dataset használata a modellezési notebook az adatok felosztja a tanítási és fejlesztői adatkészletek a dátum-időbélyeg mentén. A notebook a telepítő a készlet kísérletet `pyspark.ml.classification` modellek. A betanítási adatok vectorized van, és a felhasználó kísérletezhet, sem a `DecisionTreeClassifier` vagy egy `RandomForestClassifier`, a legjobb teljesítményt modell található hiperparamétereket kezelésére. Teljesítmény kiértékelése a fejlesztői dataset mérték statisztikák határozza meg. A statisztikai információk a Futtatás AML munkaterület idő képernyő nyomon követésére vissza a rendszer naplózza. Minden egyes használatkor a notebook az eredményül kapott modell lemezre menti, a helyi futtatása a Jupyter notebook kernel. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Az utolsó modell segítségével menti a helyi (Jupyter notebook kernel) rendszer, a notebook létrehozza az összetevőket a modell végrehajtott egy Azure webes szolgáltatásba. A teljes működési eszközök be vannak tömöríteni a `o16n.zip` fájlt egy másik Azure blob-tárolóban. A zip-fájl tartalmazza:

* `service_schema.json`A séma definíciós fájl központi telepítéshez. 
* `pdmscore.py`Az az Azure webszolgáltatáshoz szükséges init() és run() funkciók
* `pdmrfull.model`A modell definition könyvtár.
    
 A notebook teszteli a működik együtt a modell-definíciót, mielőtt a operationalization eszközök központi telepítési csomagban. A telepítési utasításokat a notebook végén érhetők el.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben áttekintést az olvasó hogyan hozhat létre egy teljes körű prediktív karbantartási megoldás PySpark használata az Azure ML munkaterület Jupyter notebook környezetből. A forgatókönyv az olvasó is részletesen a legjobb modell is könnyen operationalized és a telepített Azure Machine Learning modell felügyeleti környezet használatával, hogy a valós idejű hiba előrejelzéseket éles környezetben használható. Az olvasó is szerkesztheti a forgatókönyvhöz a taper azt az üzleti igényeiknek megfelelő részeit.  

## <a name="references"></a>Referencia

A használati eset korábban kidolgozott több platformon:

* [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [A prediktív karbantartási modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>Következő lépések

Nincsenek elérhető az Azure Machine Learning-munkaterületen, a termék további funkciókat fogunk bemutatni, sok más példaforgatókönyvek. 