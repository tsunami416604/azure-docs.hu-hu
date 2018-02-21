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
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>Learning a prediktív karbantartási valós forgatókönyvvel mély.

A részletes tanulási a gépi tanulási alkalmazásokkal való számos területen, például a legnépszerűbb trendeket egyike:
- driverless autók és robotics
- beszéd- és képfájlok felismerés
- pénzügyi előrejelzés.

Is ismert, mély Neurális hálózatokat (DNN), ezek a módszerek vannak ösztönző által az egyes idegsejtek csoportjának viselkedését az agy (biológiai Neurális hálózatokat) belül.

A nem ütemezett berendezések állásidő hatását hátrányos bármely vállalati lehet. Nagyon fontos a mező berendezések futtatja a kihasználtsági és a teljesítmény maximalizálása érdekében, és költséges, nem ütemezett állásidő minimalizálása érdekében hagyja. Korai problémákat azonosítása segíthet korlátozott karbantartási erőforrásokat költséghatékony és minőségének javítása és ellátási lánc folyamatokat. 

A prediktív karbantartási (óra) stratégia machine learning módszerek alapján határozza meg a feltétel berendezések megelőző jelleggel karbantartás káros gépek teljesítménye elkerülése érdekében. PM adatok, a gép állapotát, figyelheti folyamatosan gyűjti elemzések mintáról olvashat, amelyek segítségével előre jelezni hibák találhatók. [Hosszú rövid távú memória (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) hálózatok az ezt a beállítást, mert az a célja, hogy megtudjuk az adatok sorozatok vonzó.

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

Az alábbiakban látható a hivatkozásra kattintva a nyilvános GitHub-tárházban probléma jelentések és a hozzájárulások: [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>Használja az eset áttekintése

Ez az oktatóanyag szimulált repülőgép motor Futtatás-hiba esemény szemléltetési a prediktív karbantartási folyamat modellezési bemutatásához. A forgatókönyv közzétett [prediktív karbantartás](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)

Ez a beállítás fő feltételezése a motor fokozatosan csökkentett teljesítményű az élettartama során. A teljesítménycsökkenés motor érzékelő mérések érzékelhető. PM kísérli meg a modell változásait érzékelő értékeiről és a korábbi hibák közötti kapcsolat. A modell is majd előre jelezni, mikor és motor sikertelen lehet a jövőben érzékelő mérések aktuális állapotán alapuló korlátozásához.

Ebben a forgatókönyvben egy LSTM hálózatot előre jelezni fennmaradó élettartama (Szabályainak) repülőgép motorok korábbi érzékelő értékek használatával hoz létre. Használatával [Keras](https://keras.io/) rendelkező [Tensorflow](https://www.tensorflow.org/) mély tanulási egy számítási motor keretegyezménynek, a forgatókönyv betanítja a LSTM egy motorok és egy nem látható motor-készletet a hálózati teszteléséhez vannak beállítva.

## <a name="prerequisites"></a>Előfeltételek
- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
- Az Azure Machine Learning munkaterület, egy munkaterület létrehozása.
- A modell operationalization: az Azure Machine Learning Operationalization, egy helyi központi telepítési környezettel, állítsa be, és egy [Azure Machine Learning modell felügyeleti fiók](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:

1. Nyissa meg a gépi tanulási munkaterületet.
2. Az a **projektek** lapon jelölje be  **+** , majd válassza ki **új projekt**.
3. Az a **új projekt létrehozása** ablak, írja be az új projekt adatait.
4. Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Prediktív karbantartási", és válassza ki a **mély Learning a prediktív karbantartási forgatókönyvben** sablont.
5. Kattintson a **létrehozása** gomb

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

A sablon által három adatkészletek bemeneteként, fájljaiban **PM_train.txt**, **PM_test.txt**, és **PM_truth.txt**. 

-  **Adatok**: A repülőgép motor Futtatás-hiba adatait. A tanítási adatokat (PM_train.txt) áll több, multivariate idősorozatban, rendelkező *ciklus* , időegységét. Minden egyes 21 érzékelő értékeket tartalmazza. 

    - Minden egyes adatsorok ugyanolyan típusú másik motort jönnek létre. Minden egyes motor kezdeti elhasználódását és néhány egyedi gyártási variation különböző fokú kezdődik. Ez az információ nem ismeri fel a felhasználót. 

    - A szimulált ezeket az adatokat a motor feltételezett, hogy minden a time series elején megfelelően működik. Az adatsorozat működési ciklusok során egy bizonyos ponton kezd kezdődik. A teljesítménycsökkenés megfelelően, és a nagyságrendet növekszik. 

    - Egy előre meghatározott küszöbérték elérése után a motor nem biztonságos további művelet tekinthető. Az utolsó ciklus minden idősorozat motor hiba pont.

-   **Adatok tesztelésére**: a motor működési adatokat, a repülőgép rögzített hibaesemények nélkül. A Tesztadatok (PM_test.txt) rendelkezik a betanítási adatok, ugyanazon adatok séma. Az egyetlen különbség az, hogy az adatok nem jelzi a hiba esetén (a legutóbbi időszakban does *nem* határoz meg a hiba pont). Nem ismert hány további ciklusok Ez a motor tarthat, mielőtt sikertelen.

- **Igazság adatok**: true fennmaradó információk ciklusok minden motor a tesztelési adatokban. Az alapoktól igazság adatokat a tesztelési adatokban motorok fennmaradó működő ciklusok számát jeleníti meg.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A forgatókönyv munkafolyamat meg van osztva a három lépést, a Jupyter notebookok egyes végre. Minden notebook hoz létre a következő jegyzetfüzetekben használatra helyileg megőrzött adatösszetevők: 

### <a name="task-1-data-ingestion-and-preparation"></a>1. feladat: Adatfeldolgozást és előkészítése

Az adatok adatfeldolgozást Jupyter Notebook `Code/1_data_ingestion_and_preparation.ipnyb` három bemeneti adatkészletek betölti a Pandas adatok keretformátum. Ezután előkészíti az adatokat a modellezési, és amelyen néhány előzetes adatábrázolási. Az adatkészletek tárolása helyi a compute környezethez a modell épület notebook használható.

### <a name="task-2-model-building-and-evaluation"></a>2. feladat: Modell létrehozásának és kiértékelése

A modell épület Jupyter Notebook `Code/2_model_building_and_evaluation.ipnyb` tanítási és tesztelési adatkészletek beolvasása a lemezről, és összeállít egy LSTM hálózati a tanítási adathalmazt. A TesztKészlet a modell teljesítményének mérése történik. Az eredményül kapott modell szerializált és használja a operationalization feladat a helyi számítási környezetben tárolja.

### <a name="task-3-operationalization"></a>3. feladat: Operationalization

A Jupyter Notebook operationalization a `Code/3_operationalization.ipnyb` hozhat létre funkciók és egy Azure-kiszolgálón futó webes szolgáltatás hívása a modell sémája a tárolt modellt használ. A notebook teszteli a Funkciók, és majd tömöríti az eszközök a `LSTM_o16n.zip` fájlt, amely be van töltve, a központi telepítés az Azure storage tárolója.

A `LSTM_o16n.zip` telepítési fájl a következő összetevőket tartalmazza:

- `webservices_conda.yaml` határozza meg a központi telepítés célja a LSTM modell futtatásához szükséges python-csomagokat.  
- `service_schema.json` Meghatározza a LSTM modell által várt Adatséma.     
- `lstmscore.py` a funkciók pontszám új adatok futtatja a központi telepítés célja határozza meg.    
- `modellstm.json` Meghatározza a LSTM architektúra. A lstmscore.py funkciók olvassa el a architektúra súlyúakét pedig a modell inicializálni.
- `modellstm.h5` Meghatározza a modell súlyt.
- `test_service.py` Egy teszt parancsfájlt, amely a központi telepítés végpont meghívja a teszt rekordok. 
- `PM_test_files.pkl` A `test_service.py` származó adatok korábbi motor beolvassa a `PM_test_files.pkl` fájlt, és a webservice küld vissza a motor hiba valószínűség LSTM elég ciklusainak.

A notebook teszteli a Funkciók, a model definition használata előtt a operationalization eszközök központi telepítési csomagok azt. És teszteléséhez a webservice vonatkozó utasítások megtalálhatók végén a `Code/3_operationalization.ipnyb` notebookot.

## <a name="conclusion"></a>Összegzés

Ez az oktatóanyag egy egyszerű forgatókönyv előrejelzéseket készítsen a érzékelő értékekkel használja. Speciális forgatókönyveit Prediktív karbantartási, például a [prediktív karbantartási modellezési R kalauz](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), használhatja a sok adatforrások, például a korábbi karbantartási rekordokat, a hibanaplókat, és számítógép-szolgáltatásokat. További adatforrások mély tanulási használható különböző kezelést igényelhet.


## <a name="references"></a>Referencia

Nincsenek a különböző platformokon elérhető egyéb prediktív karbantartási eset példák:

* [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [A prediktív karbantartási modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [A prediktív karbantartási valós forgatókönyvvel](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>További lépések

Nincsenek elérhető az Azure Machine Learning-munkaterületen, a termék további funkciókat fogunk bemutatni, sok más példaforgatókönyvek. 