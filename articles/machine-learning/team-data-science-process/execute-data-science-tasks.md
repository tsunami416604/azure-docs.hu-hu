---
title: Adatelemzési műveletek – csoportos adatelemzési folyamat végrehajtása
description: Hogyan értenie az adatokhoz is végre lehet hajtani egy adatelemzési projektjéhez trackable, szabályozott verziót, és remek.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 984b03288b8dae644fc04a2cd78fb03a2e027f62
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722203"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Adatelemzési műveletek végrehajtása: feltárás, modellezés és üzembe helyezés

Jellemzően milyen adattudományos feladatokat tartalmazza az adatfeltárás, modellezés és üzembe helyezés. Ez a cikk bemutatja, hogyan használható az interaktív adatelemzési **, elemzési és jelentéskészítési (ideaer-)** és **automatizált modellezési és jelentéskészítési (-)** segédprogramok számos olyan általános adatelemzési feladat végrehajtásához, mint például az interaktív adatok feltárása, az adatok elemzése, a jelentéskészítés és a modellek létrehozása. A modellek éles környezetben történő üzembe helyezésének lehetőségei a következők lehetnek:

- [Azure Machine Learning](../index.yml)
- [SQL-Server ML-szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> feltárás 

Adatszakértő hajthat végre feltárására és a egy számos különböző módon reporting: Python (a példában a matplotlib) kódtárak és az elérhető csomagok használatával vagy az r nyelv (ggplot vagy például rácsos). Az adatszakértők ilyen programkódot, bizonyos forgatókönyvek esetén az adatok feltárása az igényei szerint testre szabhatja. Strukturált adatok kezelésére vonatkozó igényeinek különböznek, például szöveg vagy képek strukturálatlan adatok számára. 

Az olyan termékek, mint a Azure Machine Learning [speciális adatelőkészítést](../how-to-create-register-datasets.md) biztosítanak az adathuzavona és-feltáráshoz, beleértve a szolgáltatások létrehozását is. A felhasználónak meg kell határoznia az eszközöket, könyvtárakat és csomagok, amelyek a legjobb suite a saját igényeinek megfelelő. 

Ebben a fázisban a végén a termék egy olyan adatok feltárása jelentés. A jelentés modellezési használható az adatok viszonylag átfogó képet és értékeléseket, hogy az adatok megfelelő a modellezési lépésben kell biztosítania. A csoportos adatelemzési folyamat (TDSP) parancssori segédeszközöket a félig automatikus feltárása a következő szakaszban tárgyalt modellezés, és jelentéskészítés nyújt szabványos adatfeltárás és modellezés a jelentések. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktív adatfeltárás, elemzés és jelentéskészítés a IDEAR segédprogrammal

Ez a markdown-alapú R vagy Python-Jegyzetfüzet-alapú segédprogram kiértékeléséhez, és Fedezze fel az adatkészletek rugalmas és interaktív eszközt biztosít. Felhasználók gyorsan adatkészletből a minimális kódolási jelentések készítése. Felhasználók végleges jelentés, amely ügyfelek felé, és megfontoltabb döntéseket hozhat a melyik változókat az ezt követő modellezési lépésben használt eszköz interaktív feltárása eredményez exportálhatja is gombokkal.

Jelenleg az eszköz csak a működik a memóriában adatkeretek. Adja meg a paramétereket, az adatkészlet vizsgálhatók, szükség van egy YAML-fájlt. További információ: [ideaer in TDSP Adattudományi segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> modellezés

Nincsenek számos Eszközkészletek és csomagok különböző nyelveken képzési modellek esetén. Az adatszakértők kell nyugodtan használhatja, amelyen azokat mindaddig, amíg a teljesítménnyel kapcsolatos megfontolások pontosság és késéssel kapcsolatos fontos üzleti teljesülnek ügyfélszolgáltatást, azok használatieset-forgatókönyveit és a termelési forgatókönyvekhez.

Ez a szakasz bemutatja, hogyan egy R-alapú TDSP segédprogram használata a félig automatikus modellezéshez. Ez AMAR segédprogram segítségével hozzon létre base sor modellek gyors, valamint modellezheti a paramétereket kell beállítani, hogy jobban végrehajtása meg.
A következő modell felügyeleti szakasz mutatja be, a rendszer regisztrálja, és több modell kezelésére.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Tanítási modell: modellezéséhez és jelentéskészítés a AMAR segédprogrammal

Az [automatizált modellezési és jelentéskészítési (](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) a "a") segédprogram egy testreszabható, félig automatizált eszközt biztosít a modell létrehozásához a Hyper-paraméter-megtakarítással és a modellek pontosságának összehasonlításához. 

A modell-létrehozási segédprogram egy R Markdown-fájl, amely a különböző részlegei keresztül könnyen navigációs tartalomjegyzék önálló HTML-kimenet létrehozására is futtathatók. Három algoritmusok tevékenységében kell futtatásakor a Markdown-fájlban (knit): a csomag rendeződik regressziót a glmnet véletlenszerű erdő a randomForest csomagot használ, és az xgboost csomaggal fák kiemelési). Ezek az algoritmusok mindegyike hoz létre a betanított modell. Ezek a modellek pontossága majd össze, és a szolgáltatás relatív fontosságát grafikon jelentik. Jelenleg két segédprogramok: egyet az egy bináris osztályozási feladatot és egy regressziós feladat. Az elsődleges különbségeiket módon paramétereit, és pontossága metrikák tanulási feladatok vannak megadva. 

Adjon meg egy YAML-fájlt használatos:

- a bevitt adatok (egy R-adatfájl vagy egy SQL-forrás) 
- az adatok milyen része képzési kell használni, mely részét teszteléshez
- milyen algoritmusokat futtatása 
- a kiválasztott modell optimalizálás paraméterek:
    - kereszt-ellenőrzés 
    - rendszerindítása
    - a kereszt-ellenőrzési modellrész
- a hyper-paraméter esetében minden egyes algoritmus állítja be. 

Algoritmusok, optimalizálás modellrészt, a hyper-paraméterek száma, és a hyper-paraméterkészlettel Szögtartomány keresztül történő számát is módosíthatja a modellek gyors futtatása a Yaml fájlban. Ha például futtathatók CV modellrész, kevesebb paraméterkészlettel alacsonyabb számú. Ha indokolt, is futtathatók átfogóbban egy magasabb CV modellrészt vagy több paraméterkészlettel.

További információ: [automatizált modellezési és jelentéskészítési segédprogram a TDSP Adattudományi segédprogramokban](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellkezelés
Több modell készített, miután általában szüksége lesz egy rendszer a regisztrálás és a modellek kezelése. Általában szkriptek vagy az API-k és a háttérrendszer adatbázis vagy versioning kombinációját kell. Néhány lehetőség, amely a felügyeleti feladatokhoz érdemes lehet a következők:

1. [Azure Machine Learning – modell-kezelési szolgáltatás](../index.yml)
2. [ModelDB az MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-kiszolgáló modell-felügyeleti rendszerként](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> üzembe helyezés

Éles környezet lehetővé teszi, hogy egy aktív szerepet játszanak egy üzleti modell. A telepített modell előrejelzéses használható üzleti döntéseket hozhasson.

### <a name="production-platforms"></a>Éles rendszerek
Nincsenek különböző módszerek és platformok modellek éles üzembe helyezhető. Az alábbiakban néhány lehetőséget:


- [Modell üzembe helyezése Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Telepítés előtt egy rendelkezik elég alacsony-e az éles környezetben használt modell pontozása késését biztosítja.
>
>

További példákat talál az útmutatókban, amelyek a folyamat összes lépését mutatják be **adott forgatókönyvek**esetén. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására.

> [!NOTE]
> A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B tesztelés
Ha több modell van éles környezetben, hasznos lehet a [/B teszt](https://en.wikipedia.org/wiki/A/B_testing) végrehajtása a modellek teljesítményének összehasonlításához. 

 
## <a name="next-steps"></a>Következő lépések

Az adatelemzési [projektek előrehaladásának követésével](track-progress.md) megtudhatja, hogy az adattudós hogyan követheti nyomon az adatelemzési projekt állapotát.

A [Model művelet és a CI/CD](ci-cd-flask.md) azt mutatja be, hogyan hajtható végre a CI/CD a kifejlesztett modellekkel.


