---
title: Deep learninget a prediktív karbantartási valós életből vett példák – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan replikáljon az arra vonatkozó útmutató, deep learning a prediktív karbantartáshoz, az Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 83e1f14db317f59ab2063a9d020adbdb6fe78e5f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646471"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Deep learninget a prediktív karbantartási valós életből vett példák

Deep learning egy gépi tanulás a legnépszerűbb trendekkel és sok terület, például alkalmazások:
- Driverless ábrázolja és robotika.
- Beszéd- és képfájlok felismerése.
- Pénzügyi előrejelzéseket.

Neurális hálózatok (DNN) néven is ismert, ezek a metódusok vannak merítsen inspirációt az agy (biológiai Neurális hálózatok) belüli egyes idegsejtek csoportjának viselkedését.

A berendezések váratlan üzemkimaradása minden cég számára káros lehet. Rendkívül fontos, hogy a mező berendezések futó kihasználtságát és a teljesítmény maximalizálása és költséges, nem ütemezett állásidő minimálisra csökkentése érdekében. Problémák korai azonosítása segíthet korlátozott karbantartási erőforrásokat lefoglalni a költséghatékony és minőség javítása és ellátási lánc folyamatokat. 

Egy prediktív karbantartási (óra) stratégia machine learning módszerek alapján határozza meg a feltétel berendezések előrelátó módon végrehajtani a karbantartási káros gép teljesítményét elkerülése érdekében. PM, az adatokat a számítógép állapotának figyeléséhez folyamatosan gyűjti és minták segítségével megkönnyíti a hibák kereséséhez majd elemezni. [Hosszú rövid távú memória (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) hálózatok olyan kínál vonzó megoldást ezt a beállítást, mivel az adatok sorrendje további lettek kialakítva.

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub-adattár

A Cortana Intelligence Gallery a PM-oktatóanyaghoz egy nyilvános GitHub-adattárból ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)), amelyen problémák jelentése és közreműködés.


## <a name="use-case-overview"></a>Használati eset – áttekintés

Ebben az oktatóanyagban a példában szimulált repülőgép motor sikertelenül futtatott események a modellezési folyamat prediktív karbantartás bemutatása. A forgatókönyv közzétett [prediktív karbantartási](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Ez a beállítás a fő feltételezzük, a motor az élettartamuk fokozatosan csökken. A teljesítménycsökkenés motor érzékelő mérések érzékelhető. DU. próbál modellezheti az érzékelő ezeket az értékeket a módosítások és a korábbi hibák közötti kapcsolatot. A modell majd tudja jelezni, mikor és motor sikertelen lehet a jövőben érzékelő mérések jelenlegi állapota alapján.

Ebben a forgatókönyvben hálózatot hoz létre egy LSTM hátralévő hasznos élettartamának (RUL) megjósolni a repülőgép-hajtóművek érzékelőktől kapott korábbi értékek alapján. A forgatókönyv használja a [Keras](https://keras.io/) függvénytár, amelynek a [Tensorflow](https://www.tensorflow.org/) deep learning keretrendszer, mint egy számítási motort. A forgatókönyv betanítja a LSTM-motorok egy készletét, és teszteli a hálózaton olyan látott motor csoportot.

## <a name="prerequisites"></a>Előfeltételek
- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
- Az Azure Machine Learning Workbench alkalmazásban egy létrehozott munkaterülethez.
- Az operacionalizálás modell: az Azure Machine Learning működőképessé tételét egy helyi környezet beállítása, és a egy [Azure Machine Learning Modellkezelés-fiók](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:

1. Nyissa meg a Machine Learning Workbenchet.
2. Az a **projektek** lapon jelölje be **+**, majd válassza ki **új projekt**.
3. Az a **új projekt létrehozása** panelen adja meg az adatokat az új projekt.
4. Az a **projektsablonok keresése** keresőmezőbe, írja be a "Prediktív karbantartási", és válassza ki a **Deep Learning a prediktív karbantartási forgatókönyvben** sablont.
5. Kattintson a **Létrehozás** gombra.

## <a name="prepare-the-notebook-server-computation-target"></a>A notebook server számítási cél előkészítése

A helyi gépén, a Machine Learning Workbench futtatása **fájl** menüben válassza **parancssor megnyitása** vagy **megnyitott PowerShell parancssori felület**. A parancssori felületen elérheti az Azure-szolgáltatások használatával lehetővé teszi a `az` parancsokat. Első lépésként jelentkezzen be az Azure-fiókjával a paranccsal:

```
az login
``` 

Ez a parancs biztosítja a https használható hitelesítési kulcs:\\aka.ms\devicelogin URL-CÍMÉT. A parancssori felület vár, amíg az eszköz bejelentkezési művelet adja vissza, és néhány kapcsolódási információkat szolgáltat a. Mellett, ha egy helyi [Docker](https://www.docker.com/get-docker) telepítés, a paranccsal a helyi számítási környezet előkészítése:

```
az ml experiment prepare --target docker --run-configuration docker
```

Ezért célszerű futtatni egy [adatelemzési virtuális gép (DSVM) Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) a memória-és. A dsvm-hez van konfigurálva, miután előkészítése az alábbi két parancsot a távoli Docker-környezetben:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Miután csatlakozott a távoli Docker-tárolót, a parancs DSVM Docker számítási környezet előkészítése: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

A Docker számítási környezetet előkészített, nyissa meg a Jupyter notebook server a Machine Learning Workbench **notebookok** lapon, vagy egy webböngésző-alapú kiszolgálón indítsa el a paranccsal: 

```
az ml notebook start
```

A példa notebookok az kódjának könyvtárában tárolódnak. A notebookok beállítása lefutni, az első (Code\1_data_ingestion.ipynb) notebook indítása. Minden egyes notebook megnyitásakor, válassza ki a számítási kernel kéri. Válassza ki a [projektnév] [Connection_Name] _sablon kernel a korábban konfigurált DSVM hajthat végre.

## <a name="data-description"></a>Adatok leírása

A sablon három adatkészletek használja a fájlok PM_train.txt PM_test.txt és PM_truth.txt bemenetként. 

- **Adatok**: A repülőgéphajtómű-sikertelenül futtatott adatokat. A tanítási adatokat (PM_train.txt) áll a több, többváltozós idősorozat *ciklus* idő egységet. Minden egyes 21 érzékelőinek tartalmazza. 

    - Minden egyes ugyanolyan típusú másik motor jön létre. Mindegyik motor kezdeti motorkopási és a egy egyedi gyártási variation különböző fok kezdődik. Ezt az információt nem ismeri fel a felhasználót. 

    - A szimulált adatokat, az a motor feltételezhető, hogy minden egyes elején megfelelően működik. A sorozat, a üzemeltetési ciklus végrehajtása során valamikor csökkeni kezdi. A teljesítménycsökkenés menet közben, és nagyságrendűek növekszik. 

    - Ha egy előre meghatározott küszöbérték elérése esetén a motor további művelet nem biztonságos számít. A legutóbbi ciklus egyes idősorok, amelyek a motor, a meghibásodási pont.

- **Tesztadatok**: A repülőgéphajtómű működési adatokat, anélkül, hogy a hibaeseményeket rögzíti. A teszt adatai (PM_test.txt) rendelkezik az azonos sémát, a betanítási adatok. Az egyetlen különbség, hogy az adatok nem jelzi a hiba bekövetkezésekor (az elmúlt időszakban does *nem* jelölik, a meghibásodási pont). Hány további ciklusok Ez a motor tarthat, mielőtt sikertelen nem ismert.

- **Hiteles adatok**: mindegyik motor a tesztelési adatok ciklusok igaz fennmaradó vonatkozó információkat. Az alapoktól hiteles adatokat biztosít a fennmaradó működő ciklusok a motorok a tesztelési adatok számát.

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

A forgatókönyv munkafolyamat meg van osztva a három lépést, és minden egyes lépést kell végrehajtania a Jupyter notebook. Minden egyes notebook adatösszetevők, amelyek helyileg tárolja a jegyzetfüzetekben használatra hoz létre.

### <a name="task-1-data-ingestion-and-preparation"></a>1. feladat: Adatbetöltést és -előkészítés

Az adatok betöltési Jupyter Notebook Code/1_data_ingestion_and_preparation.ipnyb a három bemeneti adatkészletek betölti a Pandas DataFrame formátumra alakítja. A notebook majd modellezési előkészíti az adatokat, és néhány előzetes adatvizualizáció does. Az adatkészletek rendszer helyben tárolja a számítási környezetet a modell létrehozása Jupyter Notebook használható.

### <a name="task-2-model-building-and-evaluation"></a>2. feladat: Modell létrehozásának és értékelés céljából használják

A modell létrehozása Jupyter Notebook Code/2_model_building_and_evaluation.ipnyb a a tanítási és tesztelési adatkészletek beolvasása a lemezről, és összeállít egy LSTM hálózatot a tanítási adathalmazt. A modell teljesítményét a tesztelési adathalmazon mérjük. Az eredményül kapott modell szerializált és a helyi számítási környezetben való használatra az operacionalizáláshoz feladatban tárolja.

### <a name="task-3-operationalization"></a>3. feladat: Operacionalizálás

Az Operacionalizálás Jupyter Notebook Code/3_operationalization.ipnyb a funkciók és-sémát a modell egy Azure-ban üzemeltetett webes szolgáltatás hívása a tárolt modellt használja. A notebook teszteli, az funkciók, és ezután tömöríti az eszközök az LSTM_o16n.zip fájlba. A fájl tölti be az Azure storage-tároló üzembe helyezéshez.

A LSTM_o16n.zip üzembe helyezési fájl a következő összetevőket tartalmazza:

- **webservices_conda.yaml**: határozza meg a Python-csomagok futtatása a LSTM-modell a központi telepítési cél szükséges.  
- **service_schema.JSON**: meghatározza a sémát a LSTM-modell által várt.   
- **lstmscore.PY**: határozza meg a functions, amely a központi telepítési cél futtatja a pontszám új adatokat.    
- **modellstm.JSON**: meghatározza a LSTM-architektúra. A lstmscore.py funkciók olvassa el az architektúra és a modell inicializálása súlyok.
- **modellstm.H5**: a modell súlyok határozza meg.
- **test_service.PY**: A teszt szkriptjét, amely meghívja ezt az üzembe helyezési végpont tesztelése az adatfelderítési rekordok. 
- **PM_test_files.pkl**: A test_service.py parancsfájl korábbi motor adatokat olvas be a PM_test_files.pkl fájlt, és elküldi a web service a visszaadandó motor meghibásodási valószínűség LSTM elegendő ciklusainak.

A notebook teszteli, az funkciók a modell definícióját előtt, csomagok, az üzembe helyezéshez operacionalizálás eszközök használatával. A Code/3_operationalization.ipnyb notebook végén és a webszolgáltatás teszteléséhez vonatkozó utasításokat is.

## <a name="conclusion"></a>Összegzés

Ebben az oktatóanyagban egy egyszerű forgatókönyvet, hogy előrejelzéseket végezzen érzékelő értékeket használó biztosít. Speciális prediktív karbantartási forgatókönyveket, például a [prediktív karbantartási modellezése az R kalauz](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) használhat több adatforráshoz, például a korábbi karbantartási rekordok, a hibanaplókat és a gép – jellemzők. További adatforrások különböző kezelések deep learning használata lehet szükség.


## <a name="references"></a>Referencia

A következő hivatkozásokat biztosít, más prediktív karbantartási példák használati esetek különböző platformokhoz:

* [A prediktív karbantartási megoldás – sablon](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Prediktív karbantartás Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktív karbantartás modellezési útmutató SQL R Services segítségével](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Útmutató Python-jegyzetfüzetet a modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark prediktív karbantartás](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Prediktív karbantartás valós életből vett példák](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>További lépések

Más példaforgatókönyvek bemutatják a termék további funkciók, a Machine Learning Workbench alkalmazásban érhető el. 
