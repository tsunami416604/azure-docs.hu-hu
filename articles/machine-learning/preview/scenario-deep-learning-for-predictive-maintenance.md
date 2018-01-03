---
title: "A részletes learning a prediktív karbantartási valós forgatókönyv - Azure |} Microsoft Docs"
description: "Ismerje meg, hogyan replikáljon az oktatóanyag részletes learning a prediktív karbantartási az Azure Machine Learning munkaterület."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 6019437763f82fa14b8677b6c7ec7f0c6938fda7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>A részletes learning a prediktív karbantartási valós forgatókönyv

A részletes tanulási egyike a legnépszerűbb trendjeinek a gépi tanulás. Részletes tanulási számos mezők és alkalmazások, beleértve a driverless autók, beszéd- és képfájlok felismerés, robotics és pénzügyi használatban van. A részletes tanulási algoritmusok készleteiből állnak, amelyek a agy (biológiai Neurális hálózatokat), és a gépi tanulás alakja van ösztönző. Kognitív kutatók általában tekintse meg a részletes tanulási mint mesterséges Neurális hálózatokat (ANNs).

A prediktív karbantartási egyben népszerű. A prediktív karbantartás számos különböző módszer úgy tervezték, annak meghatározásához, a készülék állapota, és előre jelezni, amikor karbantartási végre kell hajtani. Néhány gyakori használati módjai prediktív karbantartási a következők: előrejelzés, vizsgálata (probléma alapvető okát), tárhelyhiba-észlelés, hiba besorolásának és ajánlás megoldás vagy karbantartást végeznek műveleteket a meghibásodás után.

A prediktív karbantartási forgatókönyvben adatgyűjtés időszakosan berendezések állapotának figyelése. A cél, hogy keresse meg, amelyek segítségével előre jelezni, és végső soron megelőzheti a hibák mintákat. Használatával [hosszú rövid távú memória (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) hálózatok egy különösen tetszetős prediktív karbantartási módszert tanulási mélyen. LSTM hálózatok megfelelőek, feladatütemezések alapján. Adatsorozat időadatok segítségével vissza nézze meg hosszabb időszakokra, hiba mintázatok észlelését.

Az oktatóanyag azt egy LSTM hálózat az adatkészlet és a következő témakörben ismertetett forgatókönyv építése [prediktív karbantartási](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). A hálózati repülőgép motorok hasznos élettartama előrejelzése használjuk. A sablon előre jelezni, amikor egy repülőgép alrendszeren sikertelen lesz, a jövőben szimulált repülőgép érzékelő értékeket használja. Az előrejelzés használ, karbantartási is előre meg kell terveznie, probléma megelőzése érdekében.

Ez az oktatóanyag használja a [Keras](https://keras.io/) mély könyvtárban, és a Microsoft kognitív eszközkészlet [CNTK](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras) háttéradatbázisként.

A nyilvános GitHub-tárházban, amely rendelkezik a mintákat a jelen oktatóanyag jelenleg [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Használja az eset áttekintése

Ez az oktatóanyag szimulált repülőgép motor Futtatás-hiba esemény szemléltetési a prediktív karbantartási folyamat modellezési bemutatásához. 

Az itt leírt modellezési adatok implicit feltételezése a, hogy az eszköz rendelkezik-e a továbblépés teljesítménycsökkenést minta. A minta az eszköz érzékelő mérések is megjelenik. Az eszköz érzékelő értékek időbeli megvizsgálásával a gépi tanulási algoritmus megtudhatja, az érzékelő értékek érzékelő értékek változásait, és a korábbi hibák közötti kapcsolat. Ez a kapcsolat előre jelezni jövőbeli hibák szolgál. 

Javasoljuk, hogy az adatformátum vizsgálja meg, és végezze el a sablon minden három lépést, a mintaadatok cserélje a saját dátum előtt.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
- Az Azure Machine Learning munkaterület, egy munkaterület létrehozása.
- A modell operationalization: az Azure Machine Learning Operationalization, egy helyi központi telepítési környezettel, állítsa be, és egy [Azure Machine Learning modell felügyeleti fiók](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:

1. Nyissa meg a gépi tanulási munkaterületet.
2. Az a **projektek** lapon jelölje be  **+** , majd válassza ki **új projekt**.
3. Az a **új projekt létrehozása** ablak, írja be az új projekt adatait.
4. Az a **keresési Projektsablonjai** keresési mezőbe, írja be **prediktív karbantartási**, majd válassza ki a sablont.
5. Kattintson a **Létrehozás** gombra.

## <a name="prepare-the-notebook-server-computation-target"></a>Készítse elő a notebook server számítási cél

A helyi számítógépen, a Machine Learning-munkaterület a **fájl** menüben válassza **nyissa meg a parancssort** vagy **nyitott PowerShell**. A parancssori ablakban, a beállítás, az Ön által hajtsa végre a következő parancsokat:

`az ml experiment prepare --target docker --run-configuration docker`

Javasoljuk, hogy a notebook kiszolgálón futó DS4_V2 szabványos [adatok tudományos virtuális gép (DSVM) Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu). Miután a DSVM van konfigurálva, a következő parancsokat a Docker képek készítésére való:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Az előkészített Docker-lemezképekkel nyissa meg a Jupyter notebook kiszolgáló. Nyissa meg a Jupyter notebook kiszolgáló, vagy keresse fel a Machine Learning-munkaterület **notebookok** lapon, vagy egy webböngésző-alapú kiszolgáló indítása:`az ml notebook start`

Notebookok a Jupyter környezetben a kódjának könyvtárában tárolódnak. Futtassa ezen notebookok egymás után, számozott, Code\1_data_ingestion_and_preparation.ipynb kezdve.

Válassza ki a [projektnév] _Template [connection_name] értéket, majd válassza ki a rendszermag **beállítása Kernel**.

## <a name="data-description"></a>Adatok leírása

A sablon három adatkészletek, fájljaiban PM_train.txt, PM_test.txt és PM_truth.txt bemeneteként használja.

-  **Adatok**: A repülőgép motor Futtatás-hiba adatait. A tanítási adatokat (PM_train.txt) áll több, multivariate idősorozatban, rendelkező *ciklus* , időegységét. Minden egyes 21 érzékelő értékeket tartalmazza. 

    Feltételezhető, hogy minden a time series ugyanolyan típusú másik motor hozható létre. Minden egyes motor feltételezett, hogy különböző mértékben kezdeti elhasználódását és a gyártási variation elindításához. Ez az információ nem ismeri fel a felhasználót. 

    A szimulált ezeket az adatokat a motor feltételezett, hogy minden a time series elején megfelelően működik. Az adatsorozat működési ciklusok során egy bizonyos ponton kezd kezdődik. A teljesítménycsökkenés megfelelően, és a nagyságrendet növekszik. 

    Egy előre meghatározott küszöbérték elérése után a motor nem biztonságos további művelet tekinthető. Az utolsó ciklus az egyes adatsorok a hiba pont a megfelelő motor tekinthető meg.

-   **Adatok tesztelésére**: a motor működési adatokat, a repülőgép rögzített hibaesemények nélkül. A Tesztadatok (PM_test.txt) rendelkezik a betanítási adatok, ugyanazon adatok séma. Az egyetlen különbség az, hogy az adatok nem jelzi a hiba esetén (a legutóbbi időszakban does *nem* határoz meg a hiba pont). Nem ismert hány további ciklusok Ez a motor tarthat, mielőtt sikertelen.

- **Igazság adatok**: true fennmaradó információk ciklusok minden motor a tesztelési adatokban. Az alapoktól igazság adatokat a tesztelési adatokban motorok fennmaradó működő ciklusok számát jeleníti meg.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A forgatókönyv esetében a tartalom érhető el a [GitHub-tárházban] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

A tárházban található [fontos] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) fájlt a folyamatok, az adatokat, létrehozása és a modell végrehajtott előkészítésétől ismerteti. A három Jupyter notebookok a tárházban található [kódot] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) mappában érhetők el. 

A következő azt írják le a részletes forgatókönyv munkafolyamat.

### <a name="task-1-data-ingestion-and-preparation"></a>1. feladat: Adatfeldolgozást és előkészítése

Az adatok adatfeldolgozást Jupyter Notebook Code/1_data_ingestion_and_preparation.ipnyb a három bemeneti adatkészletek Pandas dataframe formátumra tölti be. Ezután előkészíti az adatokat a modellezési, és néhány előzetes adatábrázolási does. Majd PySpark formátumra alakítja át és a következő modellezési feladata használható az Azure-előfizetéshez az Azure Blob storage tárolók tárolja az adatokat.

### <a name="task-2-model-building-and-evaluation"></a>2. feladat: Modell létrehozásának és kiértékelése

A modell épület Jupyter Notebook Code/2_model_building_and_evaluation.ipnyb olvasások a PySpark a vonat, és tesztelje a blobtárolóból adatkészletek. Ezt követően egy LSTM hálózati képzési adatkészletek épül. A TesztKészlet a modell teljesítményének mérése történik. Az eredményül kapott modell szerializált és használja a operationalization feladat a helyi számítási környezetben tárolja.

### <a name="task-3-operationalization"></a>3. feladat: Operationalization

A Jupyter Notebook a Code/3_operationalization.ipnyb operationalization hozhat létre funkciók és sémában, amely szükséges a modell egy Azure-kiszolgálón futó webes szolgáltatás hívása a tárolt modellt használ. A notebook teszteli a Funkciók, és ezután üzenet pillanatok alatt (tömöríti) a operationalization eszközök azokat egy .zip fájl.

A zip-fájl tartalmazza ezeket a fájlokat:

- **modellstm.JSON**: A definíciós fájl központi telepítéshez. 
- **lstmscore.PY**: A **init()** és **run()** függvények, amelyek az Azure web service igényel.
- **lstm.Model**: A modell definition könyvtár.

A notebook teszteli a Funkciók, a model definition használatával, mielőtt azt a operationalization eszközök központi telepítési csomagok. A telepítési utasításokat a notebook végén érhetők el.


## <a name="conclusion"></a>Összegzés

Ez az oktatóanyag, amely csak egy adatforrás (érzékelő értékek) használatával előrejelzéseket készítsen egy egyszerű forgatókönyv használja. A speciális prediktív karbantartási forgatókönyvben például a [prediktív karbantartási modellezési R kalauz](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), sok adatforrást szüksége lehet. Más adatforrások korábbi karbantartási rekordokat, a hibanaplókat és a gép és az operátorral szolgáltatások közé tartozik. További adatforrások szükség lehet a kezelés, mély tanulási hálózatok használt különböző típusú. Fontos továbbá a modelleket a megfelelő paramétereket a finomhangolásához például a az ablak. 

Ebben a forgatókönyvben vonatkozó részeinek szerkesztheti, és próbálja meg különböző probléma forgatókönyvek, például a leírt a [prediktív karbantartási modellezési útmutató](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), amely magában foglalja a több más adatforrásokhoz.


## <a name="references"></a>Referencia

- [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

