---
title: Bevételek besorolása – csoportos adatelemzési folyamat – az Azure Machine Learning |} A Microsoft Docs
description: Hogyan projekt létrehozása az Azure Machine Learning, amely az Egyesült államokbeli bevételek osztályozza a csoportos adatelemzési folyamat sablon használatával.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: edc3fc5e2a625a14bcb48b03f32cd99069a0ad53
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42058145"
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>A csoportos adatelemzési folyamat (TDSP) project bevételek besorolása

## <a name="introduction"></a>Bevezetés

Struktúra szabványügyi szervezet és a dokumentáció adatelemzési projektek, azaz egy meghatározott horgonyozva [adattudományi életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), hogy azoknak az hatékony együttműködés megkönnyítése. Az Azure Machine Learning-projektek létrehozása a [csoportos adatelemzési folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP) sablon keretrendszert biztosít az ilyen szabványügyi szervezet.

Korábban bemutattuk kellett egy [GitHub-tárházat a TDSP projekt felépítése és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). De nem volt lehetséges, amíg most, hogy hozza létre a TDSP struktúra és a egy adattudományi eszközt belül sablonokat. Most már lehetővé tettük, példányosítása az Azure Machine Learning-projektek létrehozása [TDSP struktúra és a dokumentáció sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp). A TDSP struktúra és a sablonok használatát az Azure Machine Learning utasításokat [Itt](https://aka.ms/how-to-use-tdsp-in-aml). Itt egy példa hogyan egy tényleges machine learning-projektet is lehet TDSP szerkezet használatával létrehozott, projektre jellemző kódra, összetevők és dokumentumokhoz, feltöltve és belül az Azure Machine Learning biztosítunk.

## <a name="link-to-github-repository"></a>GitHub-adattárra mutató hivatkozás
Összefoglaló dokumentáció kínálunk [Itt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) tudnivalók a mintáról. További részletes dokumentációt a GitHub webhelyen található.

### <a name="purpose"></a>Cél
Ez a minta elsődleges célja, hogy bemutatják, hogyan hozható létre, és hajtsa végre a machine learning-projektet a [csoportos adatelemzési folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktúra és az Azure Machine Learning-sablonok. Erre a célra használjuk a jól ismert [1994 USA népszámlálási adatok a UCI Machine Learning adattárból](https://archive.ics.uci.edu/ml/datasets/adult). A modellezés feladat, hogy előre jelezni az Egyesült Államok éves jövedelem osztályok USA népszámlálási információk (például életkor, faji, a iskolázottsági szint, a származási, stb.)

### <a name="scope"></a>Hatókör
 * Adatfeltárás, a képzés és a egy gépi tanulási modellt, amely a használati eset – áttekintés ismertetett előrejelzési problémájára telepítésének. 
 * A projekt az Azure Machine Learning az Azure Machine Learning a csoportos adatelemzési folyamat (TDSP) sablon használatával a projekthez tartozó végrehajtását. Projekt végrehajtása és a jelentéskészítési fogjuk használni a TDSP életciklus.
 * A megoldás közvetlenül az Azure Container Service szolgáltatásait az Azure Machine Learning operacionalizálás.

 A projekt számos funkciót az Azure Machine Learning, az ilyen TDSP struktúra példányosítás és használható, a Jupyter notebooks, valamint a Python-fájlok kódot és az Azure Container Service a Docker és a Kubernetes használatával egyszerűen operacionalizálás végrehajtási emeli ki.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Csapat adatelemzési folyamat (TDSP) életciklusa
Lásd: [csoportos adatelemzési folyamat (TDSP) életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Előfeltételek
### <a name="required-subscription-hardware-software"></a>Kötelező: a előfizetés, hardverek, szoftverek
1. Azure-beli [előfizetés](https://azure.microsoft.com). Megjelenik egy [ingyenes előfizetés](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) is végrehajtani ezt a mintát.
2. Egy [Azure adatelemzési virtuális gép (DSVM) a Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (Virtuálisgép-méret: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), 4 virtuális processzorok és 14 Gb RAM). Bár az Azure-DSVM tesztelve, akkor valószínű, bármilyen Windows 10-es gépen.
3. Tekintse át a dokumentáció az Azure Machine Learning és a kapcsolódó szolgáltatásokat (lásd lent kapcsolatokhoz).
4. Győződjön meg arról, hogy megfelelően telepítette az Azure Machine Learning által a [rövid telepítési útmutatójában](../service/quickstart-installation.md).

Az adatkészlet ehhez a mintához van a UCI ML adattárból [[-hivatkozás]](https://archive.ics.uci.edu/ml/datasets/adult). A 1994 USA népszámlálási adatbázis származik, és körülbelül 50 000 egyének népszámlálási és a bevételi adatait tartalmazza. Numerikus kellene strukturált adathalmaz és a kategorikus funkciók és a egy kategorikus cél álló jövedelem két kategóriába ('> 50 K "vagy" < = 50 K "). 

### <a name="optional-version-control-repository"></a>Választható lehetőség: Verzió verziókövetési adattár
Ha szeretné menteni a verzió és a projektet, és annak tartalmát, szüksége lesz egy verzió verziókövetési adattár, ahol ezt megteheti. A TDSP-sablon használatával az Azure Machine Learning az új projekt létrehozása során a Git a tárház is megadhatja. Lásd: [Git használatával az Azure Machine Learning](using-git-ml-project.md) további tájékoztatást talál.

### <a name="informational-about-azure-machine-learning"></a>Tájékoztató: Az Azure Machine Learning szolgáltatásról
* [Gyakori kérdések – első lépések](frequently-asked-questions.md)
* [Áttekintés](../service/overview-what-is-azure-ml.md)
* [Telepítés](../service/quickstart-installation.md)
* [Végrehajtás](experimentation-service-configuration.md)
* [TDSP használata](https://aka.ms/how-to-use-tdsp-in-aml)
* [Fájlok olvasása és írása](how-to-read-write-files.md)
* [Git használata az Azure Machine Learninggel](using-git-ml-project.md)
* [Egy gépi Tanulási modellek üzembe webszolgáltatásként](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Egy új workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Osztályozás Egyesült Államokbeli bevételek – TDSP projekt", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

Ha megad egy üres Git-tárház létrehozása a projekthez (a megfelelő mezőben) során, majd a tárház tölti fel a projektstruktúra és annak tartalmát a projekt létrehozása után.

## <a name="use-case-overview"></a>Használati eset – áttekintés
A probléma, tudni, hogyan társadalmi-gazdasági adatokat a Velünk népszámlálási rögzített segítségével a felhasználók az Egyesült Államok éves bevétel előrejelzése. A machine learning feladat népszámlálási funkciók alapuló, előre jelezni, ha a egyének jövedelem $ 50 000 felett van, vagy nem (bináris osztályozás feladat).

## <a name="data-description"></a>Adatok leírása
Az adatokkal kapcsolatos részletes információkért lásd: a [leírás](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) a UCI tárházban. 

Ezek az adatok ki kell olvasni az népszámlálási Hivatala adatbázisból található a következő helyen: https://www.census.gov/en.html. 


* Nincsenek (szűrés) előtt 48,842 példányok összesen, folyamatos és különálló vegyesen (betanításához = 32, 561, tesztelje = 16, 281)
* A címke valószínűség "> 50 K": 23.93 % / 24.78 % (nélkül unknowns)
* A címke valószínűség a(z) < = 50 K ": 76.07 % / 75.22 % (nélkül unknowns)  

* **CÉL**: bevétel osztály "> 50 K", ' < = 50 K ". Ezek helyébe 1 és 0 rendre az adat-előkészítési fázisban.
* **SZOLGÁLTATÁSOK**: kor, munkahelyi osztály, a iskolázottsági szint, a iskolázottsági szint, faji, nemét, munkaórákon héten, és így tovább.


## <a name="project-structure-execution-and-reporting"></a>Projektstruktúra, végrehajtási és jelentéskészítés

### <a name="structure"></a>struktúra
Az ebben a projektben a TDSP struktúra és a dokumentáció a sablonokkal (alább), mely a következő használunk a [TDSP életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projekt létrehozása utasítások alapján [Itt](https://aka.ms/how-to-use-tdsp-in-aml). Azt a rendszer beírja a projekt kódját és összetevők, miután a struktúrát a következőképpen néz ki (lásd az alábbi ábrán pirossal bevitel projektstruktúra).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Futtatási
Ebben a példában azt futtassa a kódot **helyi számítási környezetben**. Az Azure Machine Learning dokumentumok további részletekért tekintse meg [végrehajtási beállítások](experimentation-service-configuration.md).

Egy Python-szkript végrehajtása helyi Python-futtatókörnyezetben a rendszer egyszerű:

    az ml experiment submit -c local my_script.py

IPython notebook fájlok lehetnek duplán kattint a projektstruktúra bal oldalán, az Azure Machine Learning felhasználói felületén, és a Jypyter Notebook Server rendszerben.


A részletes adatelemzési munkafolyamathoz a következő volt:

* [**Adatgyűjtés és adatértelmezés**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Adatokat .csv formátumban a UCI ML-tárház URL-címekről letöltött [[-hivatkozás]](https://archive.ics.uci.edu/ml/datasets/adult). Funkciók, a cél és azok az átalakítások ismerteti részletesen a ProjectReport.md fájlban.

Adatgyűjtés és adatértelmezés kódját található: / kód/01_data_acquisition_and_understanding.

Adatfeltárás segítségével végezhető el a Python 3 [IDEAR (interaktív Adatáttekintés és jelentéskészítés) segédprogram](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) részeként közzétett [TDSP csomag beépített adatelemzési eszközzel,](https://github.com/Azure/Azure-TDSP-Utilities). Ez a segédprogram segítségével szabványosított adatok feltárása jelentéseket tartalmazó numerikus és a kategorikus funkciók és a célként megadott adatokat. A Python 3 IDEAR segédprogram használatáról részleteket lejjebb találja. 

A végső soron az adatok feltárására jelentés helye [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). A IDEAR jelentés nézet alább látható:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modellezés**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Létrehozott két modell 3-fold kereszt-ellenőrzés a: Net rugalmas és a véletlenszerű erdőben. Használtuk [59-pont mintavételi](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) véletlenszerű rács keresés stratégiánk a kereszt-ellenőrzési és a model paraméter optimalizálásához. A modell pontosságát is mérni a tesztelési adathalmazon AUC (görbe alatti terület) használatával. 

A modellezés található kód: / kód/02_modeling.

Rugalmas Net és a véletlenszerű erdő modellek AUC > 0.85 voltak. Mindkét modellt pickled.pkl fájlokat és a kimenet a ROC jeleníti meg mind a két modellben mentse azt. Véletlenszerű erdő AUC modell 0.92 volt, és a rugalmas Net modell volt 0,90. Emellett modell értelmezéséhez, a véletlenszerű erdőmodell funkció fontos a kimenetet egy .csv-fájlba és a PDF-fájlt (első 20 prediktív funkciók csak) megjelennek.

A ROC-görbe **véletlenszerű erdőmodell** teszt az adatokat alább látható. Ez volt a modell üzembe lett helyezve:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

A szolgáltatás fontossága (első 20) véletlenszerű erdőmodell alább látható. Szolgáltatások tőke nyereség összeg, eduction, családi állapot jelenik meg, a legmagasabb funkció fontosságát.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Üzembe helyezés**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

A véletlenszerű erdőmodell helyeztünk üzembe webszolgáltatásként, amely – a fürtön a [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Az üzembehelyezési környezetet a Docker és a Kubernetes építi ki a fürt kezelése a webszolgáltatás üzembe helyezés. Talál további információkat a operacionalizálás folyamatot [Itt](model-management-service-deploy.md). 

Az üzembe helyezés található kód: / kód/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[A projekt végső jelentés](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
A fenti szakaszokban kapcsolatos információkért a lefordított a projekt végső jelentésben szerepelnek [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). A projekt jelentés a használati esetet, modell teljesítmény-mérőszámok, üzembe helyezési és infrastruktúra, amelyre a projekt fejleszteni és üzembe helyezett kapcsolatos további részleteket is tartalmaz.

A projekt jelentés együtt a teljes projektmappáról, és a verziókövetési tárházat kézbesítendő előfordulhat, hogy az ügyfél verzióját.


## <a name="conclusion"></a>Összegzés

Ebben a példában megmutattuk használó TDSP struktúra és a sablonok az Azure Machine Learningben. A dokumentum és összetevő-sablonok segítségével a következőket teheti:
1. Cél és a egy projekt hatókörének megfelelően definiálása
2. Hozzon létre egy projektcsapat elosztott szerepkörökről és feladatokról
3. Struktúra, és hajtsa végre a projektek megfelelően a TDSP életciklusának szakaszai
4. Olyan segédprogramokkal TDSP data science (például a IDEAR adatok adatáttekintési és vizualizációs jelentés) szabványosított jelentéseket fejleszthet.
5. Végső soron az adatok adatelemzési projekt jelentés készítése egy ügyfél letöltéséhez

Reméljük, hogy a szolgáltatás az Azure Machine Learning használata belül az adatok adattudománnyal foglalkozó csapatai szabványügyi szervezet és az együttműködés megkönnyítése érdekében.

## <a name="next-steps"></a>További lépések

Tekintse meg az első lépések az alábbi hivatkozásokat:

[Csoportos adatelemzési folyamat (TDSP) használata az Azure Machine Learningben](https://aka.ms/how-to-use-tdsp-in-aml)

[Csoportos adatelemzési folyamat (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[TDSP projekt sablont az Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Egyesült Államokbeli bevételek adatkészlet UCI ML-adattárból](https://archive.ics.uci.edu/ml/datasets/adult)