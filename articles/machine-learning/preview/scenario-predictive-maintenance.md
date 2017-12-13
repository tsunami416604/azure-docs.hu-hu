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
ms.openlocfilehash: d8e34924cb29e2e6469d009e40b04d5cee8930a6
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>A prediktív karbantartási valós forgatókönyv.

A nem ütemezett berendezések állásidő hatását hátrányos bármely vállalati lehet. Fontos, ezért megtartása mező berendezéshez való használat és a teljesítmény maximalizálása érdekében fut és költséges, nem tervezett leállás csökkentésével. Korai problémákat azonosítása segíthet korlátozott karbantartási erőforrásokat költséghatékony és minőségének javítása és ellátási lánc folyamatokat. 

Ebben a forgatókönyvben felderíti a viszonylag [nagyméretű szimulált adatkészlet](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) bízná egy prediktív karbantartási adatok tudományos projektet az adatfeldolgozást, funkció a mérnöki csapathoz, a modell létrehozásának és a modell operationalization és központi telepítés. A kód a teljes folyamat Jupyter notebookok használata az Azure ML-munkaterületen PySpark nyelven van megírva. A végső modell Azure Machine Learning modell Management használatával valós idejű berendezések hiba előrejelzéseket készítsen a van telepítve.   

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

Az alábbiakban látható a hivatkozásra kattintva a nyilvános GitHub-tárházban: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Használja az eset áttekintése

Eszköz-gyakori iparágakban a vállalatok által tapasztalt az is gépi problémák késések társított jelentős költségeket. A legtöbb vállalat érdekelt előrejelzésére, ha ezek a problémák merülhetnek fel ahhoz, hogy proaktív módon, hogy mielőtt bekövetkeznének. A cél, hogy a költségek csökkentése állásidő csökkentésével, és esetleg a biztonság fokozása. 

Ebben a forgatókönyvben a ötleteket vesz igénybe a [prediktív karbantartási alkalmazástervezési](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) szimulált adatkészlet prediktív modellek létrehozása bemutatásához. A példaadatokat a szokványos elemeket a prediktív karbantartási használata sok esetben megfigyelhető származik.

A szimulált adatok az üzleti probléma előre problémák okozta összetevőinek meghibásodása esetén. Az üzleti kérdésre, ezért nem "*mi annak, hogy az összetevő hibája miatt leáll a gép*?" Ez a probléma a multiclass osztályozási problémához (több összetevő gépenként) körbefuttatási, és a gépi tanulási algoritmus a prediktív modell létrehozásához használt. A modell betanítása a korábbi gépekről összegyűjtött adatokat. Ebben a forgatókönyvben a felhasználó végighalad számos lépés végrehajtási modell, az Azure Machine Learning-munkaterület környezeten belül.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/en-us/free/) (az ingyenes próbaverzió érhetők el).
* Egy telepített példánya [Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md) következő a [gyors üzembe helyezés a telepítési útmutató](./quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.
* Az Azure Machine Learning Operationalization megköveteli egy helyi környezet és a [modellhez tartozó felügyeleti fiók](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

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

A helyi gépén, futtatását a AML munkaterület `File` menüben válassza a `Open Command Prompt` vagy `Open PowerShell CLI`. A parancssori felület lehetővé teszi az Azure-szolgáltatások használata a `az` parancsok. Az Azure-fiókjával a parancs első, bejelentkezés:

```
az login
``` 

Ez a parancs egy használni kívánt hitelesítési kulcs biztosít a `https:\\aka.ms\devicelogin` URL-CÍMÉT. A parancssori felület megvárja, amíg az eszköz bejelentkezési művelet értéket ad vissza, és bizonyos csatlakozási információkat nyújt. Mellett, ha egy helyi [docker](https://www.docker.com/get-docker) telepíteni, készítse elő a helyi számítási környezetet az alábbi parancsokkal:

```
az ml experiment prepare --target docker --run-configuration docker
```

Érdemes futtatni egy [adatok tudományos virtuális gép Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) memóriát és lemezterületet követelményeket. Miután beállította a DSVM, készítse elő a távoli docker környezet az alábbi két parancsot:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Miután csatlakozott a távoli docker-tároló, készítse elő a DSVM docker számítási környezet használatával: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

A docker számítási környezet készített, nyissa meg a Jupyter notebook kiszolgáló, vagy a AML munkaterület notebookok lapon belül, vagy a webböngésző-alapú kiszolgáló indítása: 
```
az ml notebook start
```

A példa notebookok tárolódnak a `Code` könyvtár. A notebookok be vannak állítva futtatásához egymás után, az első indítása (`Code\1_data_ingestion.ipynb`) notebookot. Minden notebook megnyitásakor, jelölje be a számítási kernel kéri. Válassza ki a `[Project_Name]_Template [Connection_Name]` kernel a korábban konfigurált DSVM végrehajtásához.

## <a name="data-description"></a>Adatok leírása

A [adatok szimulált](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) öt vesszővel tagolt (.csv) fájlt tartalmaz. További adatkészletek részletes leírása az alábbi hivatkozásokat követve.

* [Gépek](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): megkülönböztetése az egyes szolgáltatások. Például a korszűrő és a modell.
* [Hiba](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): A hiba a napló tartalmazza nem törhető hiba lépett fel, amíg a számítógép továbbra is működik. Ezek a hibák nem számít hibák, abban az esetben, ha azokat a jövőbeli hibaeseményt lehet. Hiba történt a dátum-idő vannak kerekítve a legközelebbi óra, mivel a telemetriai adatok gyűjtése óránkénti ütemben.
* [Karbantartási](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): A karbantartási naplót mindkét ütemezett és nem ütemezett karbantartás rekordokat tartalmaz. Ütemezett karbantartási összetevők rendszeres ellenőrzéssel megfelel, a tervezett karbantartást gépi hibák vagy egyéb teljesítménycsökkenés lépnek fel. Mivel a telemetriai adatok gyűjtése az óránkénti díj a karbantartás ideje a legközelebbi óra vannak kerekítve.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): A telemetriai adatok állnak a time series az egyes gépek belül több érzékelők méri. Az adatok minden egy órával időszakban érzékelő értékek átlaga van bejelentkezve.
* [Hibák](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): hibák összetevő cserékhez belül a karbantartási naplót felel meg. Minden rekordot a gép Azonosítóját, összetevőtípust, és helyettesítő dátumát és idejét tartalmazza. Ezeket a rekordokat a gépi tanulási a címkéket, hogy a modell előrejelzése létrehozásához használt.

Tekintse meg a [adatfeldolgozást](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook forgatókönyv nyers adatkészletek letölthető a GitHub-tárházban, és hozzon létre a PySpark adatkészletek ehhez az elemzéshez kód szakaszban.

## <a name="scenario-structure"></a>A forgatókönyv struktúra
A forgatókönyv esetében a tartalom érhető el: a [GitHub-tárházban](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

A [információs](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fájl előkészítése az adatokat. a modell létrehozása, majd a termelési környezetben egy megoldás telepítésére a munkafolyamatot ismerteti. A munkafolyamat egyes lépéseinek a Jupyter notebook van beágyazva az [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) a tárház mappában.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Ez a notebook az öt bemeneti .csv fájlokat tölti le, nem néhány előzetes adatok karbantartása és a képi megjelenítés. A notebook PySpark formátumra alakítja át minden egyes, és tárolja a szolgáltatás mérnöki notebook használható egy Azure blob-tárolóhoz.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Az Azure blob, a szolgáltatások össze téli idő adatsorozat megközelítést használ telemetriai adatokat, a hibák és a karbantartási adatok modell nyers adatkészlet használatával. A modell címkék leíró, hogy melyik összetevő nem tudta összeállítani az összetevő hiba kapcsolatos új szolgálnak. A címkézett szolgáltatás adatok mentése az Azure BLOB notebook létrehozása a modell.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): A címkézett szolgáltatás dataset használata a modellezési notebook az adatok felosztja a tanítási és fejlesztői adatkészletek a dátum-időbélyeg mentén. A notebook a telepítő a készlet kísérletet `pyspark.ml.classification` modellek. A betanítási adatok vectorized van, és a felhasználó kísérletezhet, sem a `DecisionTreeClassifier` vagy egy `RandomForestClassifier`, a legjobb teljesítményt modell található hiperparamétereket kezelésére. Teljesítmény kiértékelése a fejlesztői dataset mérték statisztikák határozza meg. A statisztikai információk a Futtatás AML munkaterület idő képernyő nyomon követésére vissza a rendszer naplózza. Minden egyes használatkor a notebook az eredményül kapott modell lemezre menti, a helyi futtatása a Jupyter notebook kernel. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Az utolsó modell segítségével menti a helyi (Jupyter notebook kernel) rendszer, a notebook épít fel az összetevők telepítéséhez a modell egy Azure webes szolgáltatásba. A teljes működési eszközök be vannak tömöríteni a `o16n.zip` fájlt egy másik Azure blob-tárolóban. A zip-fájl tartalmazza:

* `service_schema.json`A séma definíciós fájl központi telepítéshez. 
* `pdmscore.py`Az az Azure webszolgáltatáshoz szükséges init() és run() funkciók
* `pdmrfull.model`A modell definition könyvtár.
    
 A notebook teszteli a működik együtt a modell-definíciót, mielőtt a operationalization eszközök központi telepítési csomagban. A telepítési utasításokat a notebook végén érhetők el.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben áttekintést az olvasó hogyan hozhat létre egy teljes körű prediktív karbantartási megoldás PySpark használata az Azure ML munkaterület Jupyter notebook környezetből. Ebben a példaforgatókönyvben a modell telepítése az Azure Machine Learning modell felügyeleti környezetben berendezések hiba előrejelzéseket készítsen a valós idejű keresztül is részletezi.

## <a name="references"></a>Referencia

A használati eset korábban kidolgozott több platformon:

* [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [A prediktív karbantartási modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="next-steps"></a>Következő lépések

Nincsenek elérhető az Azure Machine Learning-munkaterületen, a termék további funkciókat fogunk bemutatni, sok más példaforgatókönyvek. 