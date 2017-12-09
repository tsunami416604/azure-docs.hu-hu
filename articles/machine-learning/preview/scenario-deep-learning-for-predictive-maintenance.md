---
title: "Learning a prediktív karbantartási valós forgatókönyv – Azure mély |} Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogyan replikáljon a mély Learning a prediktív karbantartási oktatóanyag az Azure Machine Learning-munkaterület"
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 8997e38a81ed848c9e052ab08566ffc99560ed86
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>A mélyhivatkozással learning a prediktív karbantartási valós forgatókönyvvel

A részletes tanulási egyik gépi tanulási ma terület a legnépszerűbb trendeket, és nincsenek számos mezők és alkalmazások ahol azt jelenti, például driverless autók, a beszéd- és képfájlok használatát, a robotics és a pénzügyi. A részletes tanulási algoritmusok készleteiből állnak, amelyek a agy (biológiai Neurális hálózatokat) alakja ösztönző van, és gépi tanulási és kognitív kutatók általában hivatkozik rá, mesterséges Neurális hálózatokat (REINO).

A prediktív karbantartási egyben egy népszerű területre, ahol számos különböző módszer meghatározásához, hogy a feltétel berendezések ahhoz, hogy előre jelezni, amikor karbantartási végre kell hajtani készültek. Prediktív karbantartási foglalkozó témakörök, de nem kizárólagosan beleértve: előrejelzés, vizsgálata (kiváltó okának elemzése), tárhelyhiba-észlelés, hiba besorolásának és javaslat megoldás vagy a karbantartási műveleteket követően Hiba történt.

A prediktív karbantartási forgatókönyvben adatgyűjtés idővel rendelkező hibák előre jelezni, hogy a rendszer minták a végső cél berendezések állapot figyelésére. A képzési módszereket, mély között [hosszú rövid távú memória (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) hálózatok a prediktív karbantartási tartomány különösen tetszetős oka az oka, hogy azok alapján, feladatütemezések, nagyon jó. Ez a tény adatmodelljeinek az alkalmazások idő adatsorozat adatok segítségével vissza meg hosszabb időszakokra, lehetővé téve hiba mintázatok észlelését.

Ebben az oktatóanyagban azt felépítéséhez adatkészlet LSTM hálózaton, és ismerteti a forgatókönyv [prediktív karbantartási](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3) repülőgép motorok élettartama fennmaradó előre. Összefoglalva a sablon értékeket használja, szimulált repülőgép érzékelő előre jelezni, amikor repülőgép motor sikertelen lesz, a jövőben, hogy a karbantartási előre lehet megtervezni.

Ez az oktatóanyag használja [keras](https://keras.io/) mély tanulási függvénytár, amely Microsoft kognitív eszközkészlet [CNTK](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras) néven foghatók össze.

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása 

Az alábbiakban látható a hivatkozásra kattintva a nyilvános GitHub-tárházban: [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)

## <a name="use-case-overview"></a>Használja az eset áttekintése

Ez az oktatóanyag szimulált repülőgép motor Futtatás-hiba esemény szemléltetési a prediktív karbantartási folyamat modellezési bemutatásához. A modellezési adatok, mint alatt implicit feltételezése a, hogy az eszköz egyik fontos rendelkezik-e a továbblépés teljesítménycsökkenést mintát, amely az eszköz érzékelő mérések is megjelenik. Az eszköz érzékelő értékek időbeli megvizsgálásával a gépi tanulási algoritmus megtudhatja, az érzékelő értékek és a korábbi hibák ahhoz, hogy előre jelezni jövőbeli hibák érzékelő értékek változásait közötti kapcsolat. Javasoljuk, hogy adatformátum vizsgálata, és a sablon minden három lépést áthaladás az Ön által az adatok betöltése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
- Egy telepített példánya Azure Machine Learning-munkaterület egy munkaterület létrehozása.
- A modell operationalization: az Azure Machine Learning Operationalization egy helyi központi telepítési környezetben telepítés és a [modellhez tartozó felügyeleti fiók](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:

1. Nyissa meg az Azure Machine Learning-munkaterület
2. A projektek lapon kattintson a + aláírásához, és válassza ki az új projekt
3. Új projekt létrehozása panelen írja be az adatokat az új projekt
4. A keresési Projektsablonjai keresési mezőbe írja be a "Prediktív karbantartási", és válassza ki a sablont
5. Kattintson a Létrehozás gombra

## <a name="prepare-the-notebook-server-computation-target"></a>Készítse elő a notebook server számítási cél

A helyi gépén, futtatását a AML munkaterület `File` menüben válassza a `Open Command Prompt` vagy `Open PowerShell` parancssori felület. A parancssori felület windows belül hajtható végre a következő parancsokat:

`az ml experiment prepare --target docker --run-configuration docker`

 Javasoljuk, hogy fut a DS4_V2 szabványos [adatok tudományos virtuális gép Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu). Miután beállította a DSVM, az alábbi két parancs futtatásához szükséges:

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

A docker-lemezképekkel _előkészített_, nyissa meg a jupyter notebook kiszolgáló, vagy a belül a *AML munkaterület* notebookok lapon, vagy egy webböngésző-alapú kiszolgáló indításához futtassa:`az ml notebook start`

- Notebookok tárolódnak a `Code` a Jupyter környezetben található könyvtár. Futtatás ezek notebookok egymás után számozott, mint a kezdési (`Code\1_data_ingestionand_and_preparation.ipynb`).

- Válassza ki a rendszermag felel meg a [projektnév] _Template [Desired_Connection_Name], majd a Kernel beállítása

## <a name="data-description"></a>Adatok leírása

A sablon veszi bemeneteként három adatkészletek: "PM_train.txt", "PM_test.txt" és "PM_truth.txt"
1. Adatok: a repülőgép motor Futtatás hibát jelentő adatok. A tanítási adatokat ("PM_train.txt") "ciklus" idő egységeként 21 érzékelő értékek együtt minden egyes több multivariate idősorozatban áll. Minden egyes idősor feltételezhető, ugyanolyan típusú másik motor létrehozása folyamatban. Minden motor feltételezett, hogy a kezdeti elhasználódását és a gyártási variation különböző fokú kezdődnie, és ezt az információt nem ismeri fel a felhasználót. A szimulált ezeket az adatokat a motor feltételezett, hogy minden a time series elején megfelelően működik. Az adatsorozat működési ciklusok során egy bizonyos ponton kezd kezdődik. A teljesítménycsökkenés megfelelően, és a nagyságrendet növekszik. Egy előre meghatározott küszöbérték elérése után a motor nem biztonságos további művelet tekinthető. Ez azt jelenti az utolsó ciklus az egyes adatsorok tekinthető hiba pontnak a megfelelő motor.

2. Adatok tesztelésére: a repülőgép motor működési adatok rögzített hibaesemények nélkül. A Tesztadatok ("PM_test.txt") rendelkezik a betanítási adatok, ugyanazon adatok séma. Az egyetlen különbség az, hogy az adatok nem jelzi a hiba esetén (más szóval a legutóbbi időszakban nem felel meg a hiba pont). Nem ismert hány további ciklusok Ez a motor tarthat, mielőtt sikertelen.

3. Igazság adatok: minden motor a tesztelési adatokban igaz fennmaradó ciklusok adatokat tartalmazza. Az alapoktól igazság adatokat a tesztelési adatokban motorok fennmaradó működő ciklusok számát jeleníti meg.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A tartalom az eset áll rendelkezésre a [GitHub-tárházban] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

A tárház nincs egy [információs] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) fájl, amely ismerteti a folyamatok kialakításához és végrehajtott amíg az adatok előkészítésétől az modell. A három Jupyter notebookok belül a tárház [kódot] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) mappában érhetők el. 

Ezután azt mutatják be, a részletes forgatókönyv munkafolyamat:

### <a name="task-1-data-ingestion--preparation"></a>1. feladat: Adatfeldolgozást & előkészítése

Az adatok adatfeldolgozást Jupyter Notebook a `Code/1_data_ingestion_and_preparation.ipnyb` betölti a három bemeneti adatkészletek történő `Pandas` dataframe formátumban, az adatok előkészíti a modellezési részére, és amelyen néhány előzetes adatábrázolási. Az adatok majd alakul `PySpark` formázása, és egy Azure Blob storage tárolót használja a következő modellezési feladat előfizetését a tárolja.

### <a name="task-2-model-building--evaluation"></a>2. feladat: Modell létrehozásának & kiértékelése

A modell épület Jupyter Notebook `Code/2_model_building_and_evaluation.ipnyb` , amely beolvassa `PySpark` betanítása és adatkészletek blobtárolóból tesztelése. Ezután egy LSTM hálózati képzési adatkészletek épül. A TesztKészlet a modell teljesítményének mérése történik. Az eredményül kapott modell szerializált és használja a operationalization feladat a helyi számítási környezetben tárolja.

### <a name="task-3-operationalization"></a>3. feladat: Operationalization

A Jupyter Notebook operationalization a `Code/3_operationalization.ipnyb` vesz igénybe a tárolt modell és a buildek szükséges funkciók és -sémát a hívása a modellt az Azure üzemeltetett webszolgáltatás. A notebook teszteli a Funkciók, és az üzenet pillanatok alatt a operationalization eszközök egy zip-fájl, amely az Azure Blob storage tárolóban tárolja azokat.
A zip-fájl tartalmazza:

- `service_schema.json`A séma definíciós fájl központi telepítéshez. 
- `lstmscore.py`Az az Azure webszolgáltatáshoz szükséges init() és run() funkciók
- `lstmfull.model`A modell definition könyvtár.

A notebook teszteli a működik együtt a modell-definíciót, mielőtt a operationalization eszközök központi telepítési csomagban. A telepítési utasításokat a notebook végén érhetők el.


## <a name="conclusion"></a>Összegzés

Ez az oktatóanyag részletes learning a prediktív karbantartási tartomány a Jupyter notebook környezetben alkalmazni kívánja kezdőknek útmutatóként szolgál *Azure Machine Learning munkaterület*. Ez az oktatóanyag használja egy olyan egyszerű forgatókönyvet, ahol csak egy adatforrás (érzékelő értékek) használatával előrejelzéseket készítsen. A speciális ilyen, mint a prediktív karbantartási forgatókönyvek [útmutató modellezési prediktív karbantartási](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-modeling-Guide-R-Notebook-1), nincsenek sok más adatforrások (azaz korábbi karbantartási rekordok, a hibanaplókban, számítógép és az operátorral szolgáltatások stb.) amely kezelés hálózatok tanulási átfogó használt különböző típusú lehet szükség. Mivel a prediktív karbantartási nem egy tipikus tartományt a mély tanulási, az alkalmazás kutatási nyitott területe.

## <a name="references"></a>Referencia

- [A prediktív karbantartási megoldás sablon](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-modeling-Guide-1)
- [Python kalauz modellezési prediktív karbantartás](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-modeling-Guide-Python-Notebook-1)
- [A prediktív karbantartási PySpark használatával](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="future-directions-and-improvements"></a>Jövőbeli irány és fejlesztések

Ez az oktatóanyag részletes learning a prediktív karbantartás; használatának alapjaival foglalkozik sok prediktív karbantartási problémákat általában tartalmaz, amely az adatforrásokat, amelyeket figyelembe kell venni, amikor alkalmazza a részletes tanulási ebben a tartományban kell különböző. Emellett fontos a modelleket a megfelelő paraméterek, például ablakban a hangolására. Az olvasó ebben a forgatókönyvben vonatkozó részeinek szerkesztheti, és próbálja meg egy másik probléma forgatókönyv esetén, mint például a leírt a [prediktív karbantartási modellezési útmutató](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-modeling-Guide-1) ahol több más adatforrások van szó.
