---
title: Adatelemzési feladatok végrehajtása – Csapatadat-elemzési folyamat
description: Hogyan végezhet egy adatelemzési projekt egy nyomon követhető, verzióvezérelt és együttműködésen alapuló módon.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477153"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Adatelemzési feladatok végrehajtása: feltárás, modellezés és üzembe helyezés

A tipikus adatelemzési feladatok közé tartozik az adatok feltárása, modellezése és üzembe helyezése. Ez a cikk bemutatja, hogyan használhatja az **interaktív adatfeltárás, elemzés és jelentéskészítés (IDEAR)** és **az automatikus modellezési és jelentéskészítési (AMAR)** segédprogramok számos gyakori adatelemzési feladat elvégzéséhez, például interaktív adatfeltárás, adatelemzés, jelentéskészítés és modellkészítés. A modell éles környezetbe való üzembe helyezésének lehetőségei a következők lehetnek:

- [Azure Machine Learning](../index.yml)
- [SQL-Server gépi tanulási szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Feltárás 

Az adattudós többféleképpen végezhet feltárást és jelentést: a Pythonhoz elérhető könyvtárak és csomagok (például matplotlib) vagy R (például ggplot vagy rács) használatával. Az adatszakértők testre szabhatják az ilyen kódot, hogy azok megfeleljenek az adatok feltárásának bizonyos forgatókönyvekhez való igazításához. A strukturált adatok kezelésének igénye eltér a strukturálatlan adatok, például a szöveg vagy a képek esetében. 

Az olyan termékek, mint például az Azure Machine Learning, az adatok összevonásához és feltárásához [speciális adatelőkészítést](../how-to-create-register-datasets.md) is biztosítanak, beleértve a funkciók létrehozását is. A felhasználónak el kell döntenie, hogy az eszközök, könyvtárak és csomagok, amelyek a legjobban suite igényeiknek. 

A fázis végén a végbejutott egy adatfeltárási jelentés. A jelentésnek meglehetősen átfogó képet kell adnia a modellezéshez használandó adatokról, és értékelnie kell, hogy az adatok alkalmasak-e a modellezési lépés folytatására. A csapat adatelemzési folyamat (TDSP) segédprogramok tárgyalt a következő szakaszokban a félig automatizált feltárása, modellezése és a jelentéskészítés is szabványosított adatfeltárási és modellezési jelentések. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktív adatfeltárás, -elemzés és -jelentés az IDEAR segédprogrammal

Ez az R markdown-alapú vagy Python notebook-alapú segédprogram rugalmas és interaktív eszközt biztosít az adatkészletek kiértékeléséhez és feltárásához. A felhasználók minimális kódolással gyorsan készíthetnek jelentéseket az adatkészletből. A felhasználók a gombokra kattintva exportálhatják az interaktív eszköz feltárási eredményeit egy zárójelentésbe, amely et kézbesítenek az ügyfeleknek, vagy meghozhatják, hogy milyen változókat vegyen fel a következő modellezési lépésbe.

Jelenleg az eszköz csak a memóriában lévő adatkereteken működik. A YAML fájl szükséges, hogy adja meg a paramétereket az adatkészletet kell vizsgálni. További információ: [IDEAR in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modellezés

Számos eszköztár és csomag áll a képzési modellekhez a legkülönfélébb nyelveken. Az adatszakértők nek nyugodtan használhatják azokat, amelyek et kényelmesen használják, feltéve, hogy a pontossággal és a késéssel kapcsolatos teljesítményszempontok teljesülnek az adott üzleti használati esetekben és az éles forgatókönyvekben.

A következő szakasz bemutatja, hogyan használható egy R-alapú TDSP segédprogram félautomata modellezéshez. Ez az AMAR segédprogram használható az alapvonali modellek gyors létrehozásához, valamint azokat a paramétereket, amelyeket be kell hangolni, hogy jobban teljesítő modellt biztosítsanak.
A következő modellkezelési szakasz bemutatja, hogyan lehet egy több modell regisztrálására és kezelésére szolgáló rendszerrel.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modelloktatás: modellezés és jelentéskészítés az AMAR segédprogrammal

Az [automatikus modellezési és jelentéskészítési (AMAR) segédprogram](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) egy testreszabható, félautomata eszközt biztosít a modelllétrehozásához hiperparaméter-söpréssel, és összehasonlíthatja ezeknek a modelleknek a pontosságát. 

A modellkészítő segédprogram egy R Markdown fájl, amely futtatható, hogy önálló HTML-kimenetet készítsen egy tartalomjegyzékkel a különböző szakaszokon való egyszerű navigáció érdekében. A Markdown-fájl futtatásakor három algoritmus kerül végrehajtásra (kötött): szabályosregresszió a glmnet csomag használatával, véletlenszerű erdő a randomForest csomagot használva, és a fák fazekadása az xgboost csomag használatával). Ezen algoritmusok mindegyike egy betanított modellt hoz létre. A modell pontosságát ezután összehasonlítja, és jelenti a relatív jellemzőfontossági telkeket. Jelenleg két segédprogram van: az egyik egy bináris besorolási feladathoz, a másik pedig a regressziós feladathoz tartozik. Az elsődleges különbségek közöttük az, ahogyan a vezérlő paraméterek és a pontossági mutatók vannak megadva ezekhez a tanulási feladatokhoz. 

A YAML-fájl a következők megadására szolgál:

- az adatbevitel (SQL-forrás vagy R-Data fájl) 
- az adatok melyik részét használják fel az edzéshez, és melyik részt
- milyen algoritmusokat kell futtatni 
- a modelloptimalizálás vezérlőparamétereinek kiválasztása:
    - keresztérvényesítés 
    - bootstrapping
    - a keresztérvényesítés redői
- az egyes algoritmusok hiperparaméter-készletei. 

Az algoritmusok száma, az optimalizáláshoz való ráncok száma, a hiperparaméterek és a hyper-parameter készletek száma is módosítható a Yaml fájlban a modellek gyors futtatásához. Például futtathatók kisebb számú CV-redőkkel, alacsonyabb számú paraméterkészlettel. Ha ez indokolt, akkor is futtatható átfogóbban nagyobb számú CV redők vagy nagyobb számú paraméter készletek.

További információ: [Automated Modeling and Reporting Utility in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellkezelés
Miután több modell tősúját építették, általában szükség van egy rendszerre a modellek regisztrálásához és kezeléséhez. Általában parancsfájlok vagy API-k, valamint egy háttéradatbázis vagy verziószámozási rendszer kombinációjára van szükség. Néhány lehetőség, amelyet figyelembe vehet az ilyen felügyeleti feladatokhoz:

1. [Azure Machine Learning – modellkezelési szolgáltatás](../index.yml)
2. [ModelDB az MIT-től](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server mint modellkezelő rendszer](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Telepítés

Éles környezetben lehetővé teszi, hogy a modell aktív szerepet játsszon egy vállalkozásban. Az üzembe helyezett modellből származó előrejelzések üzleti döntésekhez használhatók.

### <a name="production-platforms"></a>Termelési platformok
Vannak különböző megközelítések és platformok, hogy terjesszen modellek termelésbe. Íme néhány lehetőség:


- [Modelltelepítés az Azure Machine Learningben](../how-to-deploy-and-where.md)
- [Modell telepítése az SQL-kiszolgálón](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> A telepítés előtt biztosítani kell, hogy a modell pontozási késése elég alacsony ahhoz, hogy éles környezetben használható.
>
>

További példák a forgatókönyvekben találhatók, amelyek bemutatják a folyamat összes lépését **az egyes forgatókönyvekhez**. Ezek a példa forgatókönyvek cikkben szerepelnek a miniatűrleírásokkal, és azokhoz [kapcsolódnak.](walkthroughs.md) Bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatba vagy folyamatba egy intelligens alkalmazás létrehozásához.

> [!NOTE]
> Az Azure Machine Learning Studio használatával történő telepítésről [az Azure Machine Learning webszolgáltatás telepítése.](../studio/deploy-a-machine-learning-web-service.md)
>
>

### <a name="ab-testing"></a>A/B vizsgálat
Ha több modell van élesben, hasznos lehet a modellek teljesítményének összehasonlítása a modellek összehasonlításához [A/B tesztelés.](https://en.wikipedia.org/wiki/A/B_testing) 

 
## <a name="next-steps"></a>További lépések

[Az adatelemzési projektek előrehaladásának nyomon követése](track-progress.md) megmutatja, hogy egy adatelemző hogyan követheti nyomon egy adatelemzési projekt előrehaladását.

[A modell működése és a CI/CD](ci-cd-flask.md) megmutatja, hogyan hajtható végre a CI/CD a fejlett modellekkel.


