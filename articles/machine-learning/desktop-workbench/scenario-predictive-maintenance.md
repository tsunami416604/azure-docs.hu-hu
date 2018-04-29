---
title: A valós forgatókönyv prediktív karbantartási |} Microsoft Docs
description: A PySpark használatával valós forgatókönyv prediktív karbantartás
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
ms.openlocfilehash: 791c34785fa817fd68d0bec8111bf23e606c9b64
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>A valós forgatókönyv prediktív karbantartás

A nem ütemezett berendezések állásidő hatását hátrányos bármely vállalati lehet. Nagyon fontos a mező berendezések kihasználtságát és a teljesítmény maximalizálása és költséges, nem ütemezett állásidő minimalizálása érdekében hagyja. Korai problémákat azonosítása segíthet korlátozott karbantartási erőforrásokat költséghatékony és minőségének javítása és ellátási lánc folyamatokat. 

Ebben a forgatókönyvben felderíti a viszonylag [nagyméretű szimulált adatkészlet](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) bízná egy prediktív karbantartási adatok tudományos projektet az adatfeldolgozást, funkció a mérnöki csapathoz, a modell létrehozásának és a modell operationalization és központi telepítés. A kód a teljes folyamat a Jupyter Notebook PySpark Azure Machine Learning munkaterület használatával íródott. A végső modell Azure Machine Learning modell Management használatával valós idejű berendezések hiba előrejelzéseket készítsen a rendszer.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-adattár

A Cortana Intelligence Gallery a PM oktatóanyag egy nyilvános GitHub-adattár ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) ahol jelenthetem a problémát, és ellenőrizze hozzájárulások.


## <a name="use-case-overview"></a>Használja az eset áttekintése

Eszköz-gyakori iparágakban a vállalatok által tapasztalt az is gépi kapcsolatos problémák miatt késések társított jelentős költségeket. A legtöbb vállalat érdekelt előrejelzésére, ha ezek a problémák léphetnek fel a proaktív módon, hogy mielőtt bekövetkeznének. A cél, hogy a költségek csökkentése állásidő csökkentésével, és esetleg a biztonság fokozása. 

Ebben a forgatókönyvben a ötleteket vesz igénybe a [prediktív karbantartási alkalmazástervezési](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) annak bemutatásához, hogyan hozhat létre prediktív modellek egy szimulált adathalmaz. A példaadatokat származik sok prediktív karbantartási feladatok ellátására megfigyelt szokványos elemeket.

A szimulált adatok az üzleti probléma összetevő hibák által okozott problémák előre jelezni. Az üzleti kérdést "*mi annak, hogy az összetevő hibája miatt leáll a gépek?*" Ez a probléma több osztály besorolás probléma (több összetevő gépenként) formátuma. A gépi tanulási algoritmus a prediktív modell létrehozásához használt. A modell betanítása előzmény-gépekről összegyűjtött adatokat. Ebben a forgatókönyvben a felhasználó végighalad a modell a Machine Learning-munkaterület környezet megvalósításának számos lépés.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
* Egy telepített példánya [Azure Machine Learning-munkaterület](../service/overview-what-is-azure-ml.md). Kövesse a [gyors üzembe helyezés a telepítési útmutató](../service/quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.
* Az Azure Machine Learning Operationalization megköveteli egy helyi környezet és egy [Azure Machine Learning modell felügyeleti fiók](model-management-overview.md).

Ebben a példában a Machine Learning-munkaterület számítási környezetben futtatható. Azonban javasoljuk a példa futtatásához legalább 16 GB memóriája. Ebben a forgatókönyvben tervezték és távoli DS4_V2 szabványos futó Windows 10-es gépre tesztelték [adatok tudományos virtuális gép (DSVM) Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Modell operationalization verzió 0.1.0a22, az Azure Machine Learning parancssori felület használatával végezhető el.

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:
1.  Nyissa meg a Machine Learning Workbenchet.
2.  Az a **projektek** lapon jelölje be **+**, majd válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt.
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Prediktív karbantartási", és válassza ki a **prediktív karbantartási** sablont.
5.  Kattintson a **Létrehozás** gombra.

## <a name="prepare-the-notebook-server-computation-target"></a>Készítse elő a notebook server számítási cél

A helyi gépén, futtatását a Machine Learning-munkaterület **fájl** menüben válassza **nyissa meg a parancssort** vagy **nyitott PowerShell parancssori felület**. A parancssori felület lehetővé teszi az Azure-szolgáltatások használatával a `az` parancsok. Első lépésként jelentkezzen be az Azure-fiókjával a parancsot:

```
az login
``` 

Ez a parancs biztosítja a https használata egy hitelesítési kulcs:\\aka.ms\devicelogin URL-CÍMÉT. A parancssori felület megvárja, amíg az eszköz bejelentkezési művelet értéket ad vissza, és bizonyos csatlakozási információkat nyújt. Mellett, ha egy helyi [Docker](https://www.docker.com/get-docker) telepítés, a parancs a helyi számítási környezet előkészítése:

```
az ml experiment prepare --target docker --run-configuration docker
```

Érdemes futtatni egy [Linux (Ubuntu) DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) memóriát és lemezterületet követelményeket. A DSVM konfigurálása után készítse elő a távoli Docker környezet az alábbi két parancsot:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Miután csatlakozott a távoli Docker-tároló, a parancs DSVM Docker számítási környezet előkészítése: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

A Docker számítási környezet készített, nyissa meg a Jupyter notebook server az Azure Machine Learning-munkaterület **notebookok** lapon, vagy egy webböngésző-alapú kiszolgáló start paranccsal: 

```
az ml notebook start
```

A példa notebookok a kódjának könyvtárában tárolódnak. A notebookok beállítása futtatásához egymás után, az első (Code\1_data_ingestion.ipynb) notebook elindítja. Minden egyes notebook megnyitásakor felkéri válassza ki a számítási kernel. Válassza ki a [projektnév] [Connection_Name] _Template kernel a korábban konfigurált DSVM végrehajtásához.

## <a name="data-description"></a>Adatok leírása

A [adatok szimulált](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) öt vesszővel tagolt (.csv) fájlt tartalmaz. Az alábbi hivatkozások segítségével adatkészletek részletes leírást beolvasása.

* [Gépek](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): különböztetni az egyes gépek, például a korszűrő és a modell funkciókat.
* [Hiba](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): A hiba a napló tartalmazza okozott továbbra is működik. a gép pedig nem törhető hibák. Ezek a hibák nem tekinthetők hibák, bár a jövőbeli hibaeseményt el. A telemetriai adatok gyűjtése egy óradíjat, mert a rendszer a legközelebbi óra kerekítése a dátum-idő érték a hibákat.
* [Karbantartási](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): A karbantartási naplót mindkét ütemezett és nem ütemezett karbantartás rekordokat tartalmaz. Ütemezett karbantartási összetevők rendszeres ellenőrzéssel megfelel. Tervezett karbantartást gépi hibák vagy egyéb teljesítménycsökkenés merülhetnek fel. A telemetriai adatok gyűjtése egy óradíjat, mert a rendszer a legközelebbi óra kerekítése a karbantartás dátum-idő érték.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): A telemetriai adatok az egyes gépek belül több érzékelők adatsorozat processzoridővel áll. Az adatok minden egy órával időszakban érzékelő értékek átlaga van bejelentkezve.
* [Hibák](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): hibák összetevő cserékhez belül a karbantartási naplót felel meg. Minden rekordot a gép Azonosítóját, összetevőtípust, és helyettesítő dátumát és idejét tartalmazza. Ezeket a rekordokat a gépi tanulási a címkéket, hogy a modell előrejelzése létrehozásához használt.

A nyers adatkészletek letölthető a GitHub-tárházban, és hozzon létre ehhez az elemzéshez PySpark adatkészletek, tekintse meg a [adatfeldolgozást](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook forgatókönyv a kód mappában.

## <a name="scenario-structure"></a>A forgatókönyv struktúra
A forgatókönyv esetében a tartalom érhető el: a [GitHub-tárházban](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

A [információs](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) fájl ismerteti a munkafolyamatot az adatok előkészítése, a modell létrehozása és majd a termelési környezetben egy megoldás telepítésére. A munkafolyamat minden lépés a Jupyter notebook van beágyazva az [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) a tárház mappában.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): A notebook az öt bemeneti .csv fájlokat tölti le, és nem néhány előzetes adatok karbantartása és a képi megjelenítés. A notebook PySpark formátumra alakítja át minden egyes, és azt egy Azure blob-tároló, a szolgáltatás mérnöki Notebook használható tárolja.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): A modell szolgáltatások össze a nyers adatok beállítása az Azure Blob storage a téli idő adatsorozat megközelítés telemetriai adatokat, a hibák és a karbantartási adatok használatával. Az összetevő hiba kapcsolatos új leíró, hogy melyik összetevő működése sikertelen volt a modell feliratok összeállíthatja szolgálnak. A címkézett funkció mentett adatok az Azure BLOB a modell épület notebook.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): A modell épület Notebook címkézett szolgáltatás adatkészlet használ, és az adatok felosztja a tanítási és fejlesztői adatkészletek a dátum-időbélyeg mentén. A notebook pyspark.ml.classification modellekkel való kísérlet lett beállítva. A betanítási adatok vectorized van. A felhasználó is kísérletezhet vagy egy **DecisionTreeClassifier** vagy **RandomForestClassifier** a legjobb teljesítményt modell található hiperparamétereket módosítására. Teljesítmény kiértékelése a fejlesztői adatkészlet mérési statisztikák határozza meg. A statisztikai információk a Machine Learning-munkaterület futásidejű képernyő nyomon követésére vissza a rendszer naplózza. Minden egyes használatkor a notebook az eredményül kapott modell a helyi lemezre menti, hogy fut a Jupyter notebook kernel. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): A notebook hozhat létre az összetevők telepítéséhez a modell egy Azure webes szolgáltatás a helyi (Jupyter notebook kernel) lemezre mentett utolsó modellt használ. A teljes működési eszközöket egy másik Azure blob-tárolóban tárolt o16n.zip fájlba vannak tömöríteni. A zip-fájl tartalmazza:

* **service_schema.JSON**: központi telepítésére vonatkozó definíciójának sémafájl. 
* **pdmscore.PY**: A **init()** és **run()** az Azure által igényelt funkciók webes szolgáltatás.
* **pdmrfull.Model**: A modell definition könyvtár.
    
A notebook teszteli a működik együtt a modell-definíciót, mielőtt a operationalization eszközök központi telepítési csomagban. A telepítési utasításokat a notebook végén érhetők el.

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben egy végpontok közötti prediktív karbantartási megoldás kialakításának PySpark a Machine Learning-munkaterület Jupyter Notebook környezetben áttekintést. Ebben a példaforgatókönyvben a modell központi telepítési hiba előrejelzéseket készítsen a valós idejű berendezések a Machine Learning modell felügyeleti környezet keresztül is részletezi.

## <a name="references"></a>Referencia

A következő hivatkozásokat adjon meg más prediktív karbantartási példái a különböző platformok használati esetekben:

* [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [A prediktív karbantartási modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Learning a prediktív karbantartási mély](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>További lépések

A Machine Learning Ezzzel a termék további funkciókat fogunk bemutatni, más példaforgatókönyvek érhetők el. 
