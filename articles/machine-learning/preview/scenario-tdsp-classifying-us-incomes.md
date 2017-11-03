---
title: "Bevétel osztályozási - csoport adatok tudományos folyamat - Azure Machine Learning |} Microsoft Docs"
description: "Hogyan használható a csapat adatok tudományos folyamatsablont-projekt létrehozása az Azure Machine Learning USA bevételek osztályozó."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 3e92687657b4e80e75fd869da454970622f7178c
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Bevétel besorolás Team adatok tudományos folyamat (TDSP)-projekt

## <a name="introduction"></a>Bevezetés

Struktúra szabványügyi és dokumentáció található adatok tudományos projektek, azaz egy meghatározott horgonyozva [adatok tudományos életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), kulcsa adatok tudományos csoportban hatékony együttműködés megkönnyítése. Azure Machine Learning-projektek létrehozása a [Team adatok tudományos folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP) sablon keretet biztosít az ilyen szabványosítás.

Korábban rendelkezett megjelent egy [GitHub-tárházban TDSP szerkezetének és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). De nem volt lehetséges, amíg nem most, amelyekből példányokat kell TDSP felépítését és sablonok tudományos eszköz belül. Most már engedélyezte a azt létrehozása az Azure Machine Learning-projektek, amely a létrehozásának [TDSP struktúra és dokumentáció található sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp). Az Azure Machine Learning TDSP struktúra és a sablonok használatával utasításai [Itt](https://aka.ms/how-to-use-tdsp-in-aml). Itt nyújtunk hogyan tényleges gépi tanulás projektben hozható létre TDSP struktúra használata, beírja a projekthez tartozó kódot, összetevők és dokumentumokhoz, és az Azure Machine Learning futtatásuk példát.

## <a name="link-to-github-repository"></a>GitHub-tárházban csatolása
Összegző dokumentáció nyújtunk [Itt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) a minta kapcsolatban. További részletes dokumentációt a GitHub webhelyen található.

### <a name="purpose"></a>Cél
Ez a minta elsődleges célja bemutatják, hogyan hozható létre, és hajtsa végre a machine learning-projektet a a [Team adatok tudományos folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktúra és az Azure Machine Learning-sablonok. Erre a célra használjuk a jól ismert [1994 Velünk nyilvántartásba adatait a UCI Machine Learning tárház](https://archive.ics.uci.edu/ml/datasets/adult). A modellezési feladata előre jelezni az USA éves bevétel osztályok Velünk nyilvántartásba információk (például kor, versenyhelyzet, education szint, a származási, stb.)

### <a name="scope"></a>Hatókör
 * Az adatok feltárása, képzés és a gépi tanulási modell, amely ismerteti a használható Case áttekintése előrejelzés problémájára központi telepítését. 
 * Az Azure Machine Learning-sablonnal a csapat adatok tudományos folyamat (TDSP) az Azure Machine Learning ebben a projektben a projekt végrehajtásának. A projekt végrehajtása és a reporting fogjuk használni a TDSP életciklus.
 * A megoldás közvetlenül az Azure Machine Learning Azure tároló szolgáltatásokban operationalization.

 A projekt több funkciót az Azure Machine Learning, ilyen TDSP struktúra példányának létrehozásánál és használatra, végrehajtási kód Jupyter notebookok, valamint a Python-fájlokat, és az Azure-tároló szolgáltatások Docker és Kubernetes segítségével könnyen operationalization mutatja be.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Team adatok tudományos folyamat (TDSP) életciklusa
Lásd: [vonja össze az adatokat tudományos folyamat (TDSP) életciklusa](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Előfeltételek
### <a name="required-subscription-hardware-software"></a>Kötelező: a előfizetés, hardverek, szoftverek
1. Egy Azure [előfizetés](https://azure.microsoft.com). Beszerezheti a [ingyenes előfizetés](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) is végrehajtani ezt a mintát.
2. Egy [Azure adatok tudományos virtuális gép (DSVM) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (Virtuálisgép-méret: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), 4 virtuális processzor és 14 Gb RAM). Bár az Azure DSVM vizsgálni, valószínű, minden Windows 10-es gépen.
3. Tekintse át a dokumentáció az Azure Machine Learning és a kapcsolódó szolgáltatások (lásd lent hivatkozások).
4. Győződjön meg arról, hogy megfelelően telepítve Azure Machine Learning által a [– első lépések a telepítési útmutató](quickstart-installation.md).

Ez a minta az adatkészletet a UCI ML tárházból van [[hivatkozás]](https://archive.ics.uci.edu/ml/datasets/adult). A 1994 Velünk nyilvántartásba adatbázis származik, és körülbelül 50 000 egyéni nyilvántartásba és bevétel tudnivalókat tartalmaz. Ez az rendelkező numerikus strukturált adathalmaz, kategorikus elemet, és két bevétel kategóriák álló kategorikus célja ("> 50-K" vagy "< = 50-K"). 

### <a name="optional-version-control-repository"></a>Választható lehetőség: Verzió tárházat
Ha menteni szeretné és verzió a projekt és annak tartalmát, szüksége van egy adott ehhez verzió verziókövetési tárházzal. A Git-tárház helye adhatja meg az TDSP sablon használata az Azure Machine Learning az új projekt létrehozása során. Lásd: [Git használata az Azure Machine Learning](using-git-ml-project.md) további tájékoztatást talál.

### <a name="informational-about-azure-machine-learning"></a>Tájékoztató: Kapcsolatban az Azure gépi tanulás
* [Gyakori kérdések – első lépések](frequently-asked-questions.md)
* [Áttekintés](overview-what-is-azure-ml.md)
* [Telepítés](quickstart-installation.md)
* [Végrehajtás](experimentation-service-configuration.md)
* [TDSP használatával](https://aka.ms/how-to-use-tdsp-in-aml)
* [Fájlok olvasása és írása](how-to-read-write-files.md)
* [Git használata az Azure Machine Learninggel](using-git-ml-project.md)
* [Az ML-modell egy webszolgáltatás telepítése](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be az "USA besorolás bevételek - TDSP projekt", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

Ha megad egy üres Git-tárház helye létrehozása a projekthez (a megfelelő mezőben) során, majd adott tárház tölti fel a projekt struktúra és a tartalmakat a projekt létrehozása után.

## <a name="use-case-overview"></a>Használja az eset áttekintése
A probléma a megértése, hogyan-társadalmi Velünk nyilvántartásba rögzített adatok segítségével előre jelezni az Egyesült Államokban egyének származó éves. Az ilyen nyilvántartásba szolgáltatások alapján, a machine learning feladat előre jelezni, ha egy egyéni bevétel túllépi ezt az értéket $ 50 000 vagy nem (bináris osztályozás feladat).

## <a name="data-description"></a>Adatok leírása
Az adatokkal kapcsolatos részletes információkért tekintse meg a [leírás](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) a UCI tárházban. 

Ezeket az adatokat a következő címen található nyilvántartásba iroda adatbázis kinyert: https://www.census.gov/en.html. 


* Összesen (előtt szűrés) 48,842 példányok, folyamatos és különálló kombinálhatók (betanítása = 32, 561, tesztelje = 16, 281)
* A címke valószínűség "> 50-K": 23.93 % / 24.78 % (nélkül unknowns)
* A címke valószínűség "< 50 K =": 76.07 % / 75.22 % (nélkül unknowns)  

* **CÉL**: bevétel osztály "> 50 K', ' < 50 K =". Ezek helyébe 1 és 0 rendre adatok előkészítési fázisban.
* **SZOLGÁLTATÁSOK**: kor, munkahelyi osztály, education szint, education szint, versenyhelyzet, nemét, munkaórákon hét stb.


## <a name="project-structure-execution-and-reporting"></a>Projekt struktúra, végrehajtási és jelentéskészítés

### <a name="structure"></a>struktúra
Ebben a projektben a TDSP struktúra és a dokumentáció a sablonokkal (alább), mely az alábbiak szerint használjuk a [TDSP életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projekt kapott utasítások alapján jön létre [Itt](https://aka.ms/how-to-use-tdsp-in-aml). A projekt kóddal és összetevők tölti, miután a struktúra a következőképpen néz ki (lásd az alábbi képen piros bevitel szerkezetének).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Végrehajtás
Ebben a példában a kód azt végrehajtását **helyi számítási környezet**. Az Azure Machine Learning dokumentumok további részletekért tekintse meg [végrehajtási beállítások](experimentation-service-configuration.md).

A helyi Python-futtatókörnyezet egy Python-parancsfájl végrehajtása az egyszerű:

    az ml experiment submit -c local my_script.py

IPython notebook fájlokat az Azure Machine Learning a felhasználói felület a bal oldalon a projekt szerkezet komponensre, és a Jypyter Notebook Server rendszerben.


A részletes adatelemezési munkafolyamatot a következő volt:

* [**Adatok megszerzését és ismertetése**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Adatok letöltött UCI ML-tárházban URL-címeket a .csv formátumban [[hivatkozás]](https://archive.ics.uci.edu/ml/datasets/adult). Funkciók, a cél és azok átalakítások a ProjectReport.md fájlban részletesen ismerteti.

Adatok megszerzését és ismertetése kódját található: / kódot/01_data_acquisition_and_understanding.

Az adatok feltárása segítségével végezhető el a Python 3 [IDEAR (interaktív adatok feltárása és jelentéskészítési) segédprogram](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) részeként közzétett [TDSP eszközcsomagot jelent adatokat tudományos](https://github.com/Azure/Azure-TDSP-Utilities). A segédprogram segítségével szabványosított adatok feltárása jelentések adatokat tartalmazó numerikus és kategorikus szolgáltatásait és a cél. A Python 3 IDEAR segédprogram használatáról részleteket lejjebb tekinthetők meg. 

A végső adatok feltárása jelentés helye [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). A nézet a IDEAR jelentés alább találja:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modellezési**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Két modell létrehozott 3-fold kereszt-ellenőrzési a: Net rugalmas és a véletlenszerű erdő. Használtuk [59-pont mintavételi](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) véletlenszerű rács Search stratégiát, a kereszt-ellenőrzési és a model paraméter optimalizálásához. A modell pontosságát volt mérni a teszt adatkészlet AUC (görbe területen) használatával. 

A modellezési található kód: / kódot/02_modeling.

Rugalmas Net és a véletlenszerű erdő modellek AUC > 0.85 volt. Mindkét modellt elmentjük a pickled.pkl fájlokat, és a kimeneti a ROC tevékenységtérkép, mind a két modellben. Véletlenszerű erdő AUC modell 0.92 volt, és a rugalmas Net modell 0,90. Ezenkívül modell értelmezéséhez funkció fontos a véletlenszerű erdőmodell egy CSV-fájlt a kimenetet, és ábrázolt pdf (első 20 prediktív funkciók csak).

A: ROC-görbe **véletlenszerű erdőmodell** tesztek adatok alább láthatók. Ez volt a modell telepített:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

A szolgáltatás fontosságát (első 20) véletlenszerű erdőmodell alább láthatók. Szolgáltatások származó nyereség összeg, eduction, családi állapotát jeleníti meg, a legmagasabb szolgáltatás jelentősége.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Központi telepítés**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

A véletlenszerű erdőmodell helyeztünk üzembe webszolgáltatásként – a fürt a [Azure tároló szolgáltatás (ACS)](https://azure.microsoft.com/services/container-service/). A operationalization környezet rendelkezések Docker és Kubernetes webszolgáltatás avatkozzon a fürtben. Tekinthet meg további információkat a operationalization folyamathoz [Itt](model-management-service-deploy.md). 

A központi telepítési található kód: / kódot/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[A projekt végső jelentés](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
A fenti szakaszokban mindegyikének adatait a lefordított végső projekt jelentésben szerepelnek [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). A projekt jelentés is tartalmaz további részleteket a használati eset, modell teljesítménymutatók, telepítési és infrastruktúra, amelyre a projekt fejlesztette ki és telepítve.

A projekt jelentést, és a teljes projekt mappa, és a tárházat előfordulhat, hogy kézbesítve lenne az ügyfél verziója tartalmát.


## <a name="conclusion"></a>Összegzés

Ez a példa azt bemutatta használó TDSP struktúra és a sablonokat az Azure Machine Learning. A dokumentum és az összetevő sablonok használatával a következő műveletek végezhetők el:
1. Megfelelően adja meg a cél és a projekt hatóköre
2. Hozzon létre egy projektcsapat elosztott szerepkörök és felelősségek
3. Szerkezeti és hajtható végre a TDSP életciklusának szakaszait megfelelően projektek
4. Hogyan szabványos jelentések TDSP adatok tudományos segédprogramok (például a IDEAR adatok feltárása és a képi megjelenítés jelentés).
5. Végső adatok tudományos projekt jelentés készítése egy ügyfél el lehet juttatni

Reméljük, ez a szolgáltatás az Azure Machine Learning segítségével az adatok tudományos adapterekhez belül szabványügyi és együttműködés megkönnyítése.

## <a name="next-steps"></a>Következő lépések

Első lépésként az alábbi hivatkozásokat lásd:

[Az Azure Machine Learning Team adatok tudományos folyamat (TDSP) használata](https://aka.ms/how-to-use-tdsp-in-aml)

[Team adatok tudományos folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Az Azure Machine Learning TDSP projektsablon](https://aka.ms/tdspamlgithubrepo)

[USA bevétel UCI ML tárházból adatkészlet](https://archive.ics.uci.edu/ml/datasets/adult)