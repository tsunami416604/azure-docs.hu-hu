---
title: Adatelemzési műveletek – csoportos adatelemzési folyamat végrehajtása
description: Hogyan értenie az adatokhoz is végre lehet hajtani egy adatelemzési projektjéhez trackable, szabályozott verziót, és remek.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 51d76e16e93ccffc1a069e64808563d62f428dbf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476349"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Adatelemzési műveletek végrehajtása: feltárás, modellezés és üzembe helyezés

Jellemzően milyen adattudományos feladatokat tartalmazza az adatfeltárás, modellezés és üzembe helyezés. Ez a cikk bemutatja, hogyan használhatja a **interaktív Adatkutatási, elemzési és jelentéskészítési (IDEAR)** és **automatikus modellezési és jelentéskészítési (AMAR)** segédprogramok számos gyakori adatelemzési feladatok végrehajtásához például az interaktív adatkutatási, adatelemzés, jelentés és modell létrehozásához. Azt is ismerteti lehetőségei a különböző eszközökre és az adatok platformok, például a következő ad éles környezetben üzembe helyezéséhez:

- [Azure Machine Learning](../service/index.yml)
- [SQL-Server ML szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Feltárása 

Adatszakértő hajthat végre feltárására és a egy számos különböző módon reporting: Python (a példában a matplotlib) kódtárak és az elérhető csomagok használatával vagy az r nyelv (ggplot vagy például rácsos). Az adatszakértők ilyen programkódot, bizonyos forgatókönyvek esetén az adatok feltárása az igényei szerint testre szabhatja. Strukturált adatok kezelésére vonatkozó igényeinek különböznek, például szöveg vagy képek strukturálatlan adatok számára. 

Például az Azure Machine Learning szolgáltatás is kínálnak [fejlett adat-előkészítés](../service/how-to-transform-data.md) adatok konvertálását és feltárásában, beleértve a szolgáltatás létrehozása. A felhasználónak meg kell határoznia az eszközöket, könyvtárakat és csomagok, amelyek a legjobb suite a saját igényeinek megfelelő. 

Ebben a fázisban a végén a termék egy olyan adatok feltárása jelentés. A jelentés modellezési használható az adatok viszonylag átfogó képet és értékeléseket, hogy az adatok megfelelő a modellezési lépésben kell biztosítania. A csoportos adatelemzési folyamat (TDSP) parancssori segédeszközöket a félig automatikus feltárása a következő szakaszban tárgyalt modellezés, és jelentéskészítés nyújt szabványos adatfeltárás és modellezés a jelentések. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktív adatfeltárás, elemzés és jelentéskészítés a IDEAR segédprogrammal

Ez a markdown-alapú R vagy Python-Jegyzetfüzet-alapú segédprogram kiértékeléséhez, és Fedezze fel az adatkészletek rugalmas és interaktív eszközt biztosít. Felhasználók gyorsan adatkészletből a minimális kódolási jelentések készítése. Felhasználók végleges jelentés, amely ügyfelek felé, és megfontoltabb döntéseket hozhat a melyik változókat az ezt követő modellezési lépésben használt eszköz interaktív feltárása eredményez exportálhatja is gombokkal.

Jelenleg az eszköz csak a működik a memóriában adatkeretek. Adja meg a paramétereket, az adatkészlet vizsgálhatók, szükség van egy YAML-fájlt. További információkért lásd: [TDSP Data Science segédprogramok IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modellezés

Nincsenek számos Eszközkészletek és csomagok különböző nyelveken képzési modellek esetén. Az adatszakértők kell nyugodtan használhatja, amelyen azokat mindaddig, amíg a teljesítménnyel kapcsolatos megfontolások pontosság és késéssel kapcsolatos fontos üzleti teljesülnek ügyfélszolgáltatást, azok használatieset-forgatókönyveit és a termelési forgatókönyvekhez.

Ez a szakasz bemutatja, hogyan egy R-alapú TDSP segédprogram használata a félig automatikus modellezéshez. Ez AMAR segédprogram segítségével hozzon létre base sor modellek gyors, valamint modellezheti a paramétereket kell beállítani, hogy jobban végrehajtása meg.
A következő modell felügyeleti szakasz mutatja be, a rendszer regisztrálja, és több modell kezelésére.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Tanítási modell: modellezéséhez és jelentéskészítés a AMAR segédprogrammal

A [automatikus modellezési és jelentéskészítési (AMAR) segédprogram](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) modell létrehozásához a hyper-paraméter kezdik és hasonlítsa össze ezeket a modell pontosságát a testre szabható, félig automatikus eszközt biztosít. 

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

További információkért lásd: [automatikus modellezési és segédprogram Reporting TDSP Data Science segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellkezelés
Több modell készített, miután általában szüksége lesz egy rendszer a regisztrálás és a modellek kezelése. Általában szkriptek vagy az API-k és a háttérrendszer adatbázis vagy versioning kombinációját kell. Néhány lehetőség, amely a felügyeleti feladatokhoz érdemes lehet a következők:

1. [Az Azure Machine Learning - modell felügyeleti szolgáltatás](../service/index.yml)
2. [Az MIT ModelDB](https://mitdbg.github.io/modeldb/) 
3. [SQL-kiszolgáló egy modell felügyeleti rendszer](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>központi telepítés

Éles környezet lehetővé teszi, hogy egy aktív szerepet játszanak egy üzleti modell. A telepített modell előrejelzéses használható üzleti döntéseket hozhasson.

### <a name="production-platforms"></a>Éles rendszerek
Nincsenek különböző módszerek és platformok modellek éles üzembe helyezhető. Az alábbiakban néhány lehetőséget:


- [Modell-üzembehelyezés az Azure Machine Learning szolgáltatás](../service/how-to-deploy-and-where.md)
- [Egy modellt az SQL-kiszolgáló telepítése](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Telepítés előtt egy rendelkezik elég alacsony-e az éles környezetben használt modell pontozása késését biztosítja.
>
>

További példák érhetők el, amelyek bemutatják, a folyamat összes lépését forgatókönyvek **meghatározott forgatókönyvek**. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására.

> [!NOTE]
> Az Azure Machine Learning Studio használatával olyan központi telepítésre, [egy Azure Machine Learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B tesztelés
Ha több modell éles környezetben, végrehajtásához hasznos lehet [A / B tesztelés](https://en.wikipedia.org/wiki/A/B_testing) a modellek teljesítményének összehasonlítását. 

 
## <a name="next-steps"></a>További lépések

[Adatelemzési projektek előrehaladását úgy követheti nyomon](track-progress.md) bemutatja, hogyan értenie az adatokhoz is előrehaladását úgy követheti nyomon az adatelemzési projektjéhez.

[Művelet és a CI/CD modell](ci-cd-flask.md) bemutatja, hogyan végezhető a CI/CD a fejlett modellek.


