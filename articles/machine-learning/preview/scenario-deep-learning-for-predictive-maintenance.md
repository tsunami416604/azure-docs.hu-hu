---
title: "A részletes learning a prediktív karbantartási valós forgatókönyv - Azure |} Microsoft Docs"
description: "Ismerje meg, hogyan replikáljon az oktatóanyag részletes learning a prediktív karbantartási az Azure Machine Learning munkaterület."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 022e629469745fceb4fb9355cb6259a144dda222
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>A részletes learning a prediktív karbantartási valós forgatókönyv

A részletes tanulási a legnépszerűbb trendjeinek a gépi tanulás egyike, és számos területen, beleértve az alkalmazások rendelkezik:
- Driverless autók és robotics.
- Beszéd- és képfájlok használatát.
- pénzügyi előrejelzés.

Részletes Neurális hálózatokat (DNN) néven is ismert, ezek a módszerek vannak ösztönző által az agy (biológiai Neurális hálózatokat) belüli egyes idegsejtek csoportjának viselkedését.

A nem ütemezett berendezések állásidő hatását hátrányos bármely vállalati lehet. Nagyon fontos a mező berendezések futtatja a kihasználtsági és a teljesítmény maximalizálása érdekében, és költséges, nem ütemezett állásidő minimalizálása érdekében hagyja. Korai problémákat azonosítása segíthet korlátozott karbantartási erőforrásokat költséghatékony és minőségének javítása és ellátási lánc folyamatokat. 

A prediktív karbantartási (óra) stratégia machine learning módszerek alapján határozza meg a feltétel berendezések megelőző jelleggel karbantartás káros gépek teljesítménye elkerülése érdekében. PM, az adatok gyűjtése adott idő alatt a számítógép állapotának figyelése és minták hibák megjósolható kereséséhez majd meg. [Hosszú rövid távú memória (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) hálózatok az ezt a beállítást, mert úgy lettek kialakítva, az adatok sorozatok megtudjuk vonzó.

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-adattár

A Cortana Intelligence Gallery a PM oktatóanyag egy nyilvános GitHub-adattár ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) ahol jelenthetem a problémát, és ellenőrizze hozzájárulásokat.


## <a name="use-case-overview"></a>Használja az eset áttekintése

Ez az oktatóanyag szimulált repülőgép motor Futtatás-hiba esemény szemléltetési a prediktív karbantartási folyamat modellezési bemutatásához. A forgatókönyv közzétett [prediktív karbantartási](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Ez a beállítás fő feltételezése a motor fokozatosan csökkentett teljesítményű az élettartama során. A teljesítménycsökkenés motor érzékelő mérések érzékelhető. PM megkísérli a modell változásait érzékelő értékeiről és a korábbi hibák közötti kapcsolat. A modell is majd előre jelezni, mikor és motor sikertelen lehet a jövőben érzékelő mérések aktuális állapotán alapuló korlátozásához.

Ebben a forgatókönyvben hálózatot hoz létre egy LSTM előre jelezni a fennmaradó élettartama (Szabályainak) repülőgép motorok korábbi érzékelő értékek használatával. A forgatókönyv használ a [Keras](https://keras.io/) függvénytár, amely a [Tensorflow](https://www.tensorflow.org/) mély tanulási keretegyezménynek egy számítási motor. A forgatókönyv motorok egy számú LSTM betanítja, és egy nem látható motor-készletet a hálózati tesztek.

## <a name="prerequisites"></a>Előfeltételek
- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
- Az Azure Machine Learning munkaterület, egy munkaterület létrehozása.
- A modell operationalization: az Azure Machine Learning Operationalization egy helyi központi telepítési környezettel, állítsa be, és egy [Azure Machine Learning modell felügyeleti fiók](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:

1. Nyissa meg a gépi tanulási munkaterületet.
2. Az a **projektek** lapon jelölje be  **+** , majd válassza ki **új projekt**.
3. Az a **új projekt létrehozása** ablak, írja be az új projekt adatait.
4. Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Prediktív karbantartási", és válassza ki a **mély Learning a prediktív karbantartási forgatókönyvben** sablont.
5. Kattintson a **Létrehozás** gombra.

## <a name="prepare-the-notebook-server-computation-target"></a>Készítse elő a notebook server számítási cél

A helyi gépén, futtatását a Machine Learning-munkaterület **fájl** menüben válassza **nyissa meg a parancssort** vagy **nyitott PowerShell parancssori felület**. A parancssori felület lehetővé teszi az Azure-szolgáltatások használatával a `az` parancsok. Első lépésként jelentkezzen be az Azure-fiókjával a parancsot:

```
az login
``` 

Ez a parancs biztosítja a https használható egy hitelesítési kulcs:\\aka.ms\devicelogin URL-CÍMÉT. A parancssori felület megvárja, amíg az eszköz bejelentkezési művelet értéket ad vissza, és bizonyos csatlakozási információkat nyújt. Mellett, ha egy helyi [Docker](https://www.docker.com/get-docker) telepítés, a parancs a helyi számítási környezet előkészítése:

```
az ml experiment prepare --target docker --run-configuration docker
```

Érdemes futtatni egy [adatok tudományos virtuális gép (DSVM) Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) memóriát és lemezterületet követelményeket. A DSVM konfigurálása után készítse elő a távoli Docker környezet az alábbi két parancsot:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Miután csatlakozott a távoli Docker-tároló, a parancs DSVM Docker számítási környezet előkészítése: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

A Docker számítási környezet készített, nyissa meg a Jupyter notebook kiszolgáló a Machine Learning-munkaterület **notebookok** lapon, vagy egy webböngésző-alapú kiszolgáló start paranccsal: 

```
az ml notebook start
```

A példa notebookok a kódjának könyvtárában tárolódnak. A notebookok beállítása futtatásához egymás után, az első (Code\1_data_ingestion.ipynb) notebook elindítja. Minden egyes notebook megnyitásakor felkéri válassza ki a számítási kernel. Válassza ki a [projektnév] [Connection_Name] _Template kernel a korábban konfigurált DSVM végrehajtásához.

## <a name="data-description"></a>Adatok leírása

A sablon három adatkészletek fájljaiban PM_train.txt, PM_test.txt és PM_truth.txt bemeneteként használja. 

- **Adatok**: A repülőgép motor Futtatás-hiba adatait. A vonat (PM_train.txt) tartalmaz a több, multivariate idősorozatban *ciklus* , időegységét. Minden egyes 21 érzékelő értékeket tartalmazza. 

    - Minden egyes adatsorok ugyanolyan típusú másik motort jönnek létre. Minden egyes motor kezdeti elhasználódását és néhány egyedi gyártási variation különböző fokú kezdődik. Ez az információ nem ismeri fel a felhasználót. 

    - A szimulált ezeket az adatokat a motor feltételezett, hogy minden a time series elején megfelelően működik. Az adatsorozat működési ciklusok során egy bizonyos ponton kezd kezdődik. A teljesítménycsökkenés megfelelően, és a nagyságrendet növekszik. 

    - Egy előre meghatározott küszöbérték elérése után a motor nem biztonságos további művelet tekinthető. Az utolsó ciklus minden idősorozat motor hiba pont.

- **Adatok tesztelésére**: a motor működési adatokat, a repülőgép rögzített hibaesemények nélkül. A Tesztadatok (PM_test.txt) rendelkezik a betanítási adatok, ugyanazon adatok séma. Az egyetlen különbség az, hogy az adatok nem jelzi a hiba esetén (a legutóbbi időszakban does *nem* határoz meg a hiba pont). Nem ismert hány további ciklusok Ez a motor tarthat, mielőtt sikertelen.

- **Igazság adatok**: true fennmaradó információk ciklusok minden motor a tesztelési adatokban. Az alapoktól igazság adatokat a tesztelési adatokban motorok fennmaradó működő ciklusok számát jeleníti meg.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A forgatókönyv munkafolyamat meg van osztva a három lépést, és minden egyes lépést kell végrehajtania a Jupyter notebook. Minden egyes notebook helyileg a jegyzetfüzetekben használatra megőrzött adatösszetevők eredményez.

### <a name="task-1-data-ingestion-and-preparation"></a>1. feladat: Adatfeldolgozást és előkészítése

Az adatok adatfeldolgozást Jupyter Notebook Code/1_data_ingestion_and_preparation.ipnyb a betölti a három bemeneti adatkészletek Pandas DataFrame formátumra alakítja. A notebook majd az adatok előkészíti a modellezési és amelyen néhány előzetes adatábrázolási. Az adatkészletek helyileg a számítási környezet a modell épület Jupyter Notebook használható tárolódnak.

### <a name="task-2-model-building-and-evaluation"></a>2. feladat: Modell létrehozásának és kiértékelése

A modell épület Jupyter Notebook Code/2_model_building_and_evaluation.ipnyb a tanítási és tesztelési adatkészletek beolvasása a lemezről, és a tanítási adathalmazt egy LSTM hálózatot hoz létre. A teszt adatkészlet a modell teljesítményének mérése történik. Az eredményül kapott modell szerializált és használja a operationalization feladat a helyi számítási környezetben tárolja.

### <a name="task-3-operationalization"></a>3. feladat: Operationalization

A Operationalization Jupyter Notebook Code/3_operationalization.ipnyb a Funkciók, illetve egy Azure-kiszolgálón futó webes szolgáltatás hívása a modell sémája a tárolt modellt használ. A notebook teszteli a Funkciók, és majd tömöríti az eszközök a LSTM_o16n.zip fájlba. A fájl be van töltve, a központi telepítés az Azure storage tárolója be.

A LSTM_o16n.zip telepítési fájl a következő összetevőket tartalmazza:

- **webservices_conda.yaml**: a Python-csomagokat, amelyek szükségesek a LSTM modell futtatni a központi telepítés célja határozza meg.  
- **service_schema.JSON**: sémájának definiálásához adat, amelyet a LSTM modellt vár.   
- **lstmscore.PY**: határozza meg a Funkciók, amely a központi telepítés célja futtatja a pontszám új adatokat.    
- **modellstm.JSON**: a LSTM architektúra határozza meg. A lstmscore.py funkciók olvassa el a architektúra súlyúakét pedig a modell inicializálni.
- **modellstm.H5**: a modell súlyok határozza meg.
- **test_service.PY**: A teszt parancsfájlt, amely a központi telepítés végpont meghívja a teszt rekordok. 
- **PM_test_files.pkl**: A test_service.py parancsfájl korábbi motor adatokat olvas a PM_test_files.pkl fájlt, és a webszolgáltatás küld vissza a motor hiba valószínűség LSTM elég ciklusainak.

A notebook teszteli a Funkciók, a model definition használatával, mielőtt azt a operationalization eszközök központi telepítési csomagok. És teszteléséhez a webszolgáltatás vonatkozó utasítások megtalálhatók a Code/3_operationalization.ipnyb notebook végén.

## <a name="conclusion"></a>Összegzés

Ebben az oktatóanyagban egy egyszerű forgatókönyv előrejelzéseket készítsen érzékelő értékek használó biztosít. Fejlettebb, prediktív karbantartási helyzetekben, például a [prediktív karbantartási modellezési R kalauz](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) sok adatforrások, például a korábbi karbantartási rögzíti, a hibanaplókat és a gép szolgáltatások használhatja. További adatforrások különböző kezelés, mély tanulási használata lehet szükség.


## <a name="references"></a>Referencia

A következő hivatkozásokat adjon meg más prediktív karbantartási példái a különböző platformok használati esetekben:

* [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [A prediktív karbantartási modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [A prediktív karbantartási valós forgatókönyv](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>További lépések

A Machine Learning Ezzzel a termék további funkciókat fogunk bemutatni, más példaforgatókönyvek érhetők el. 
