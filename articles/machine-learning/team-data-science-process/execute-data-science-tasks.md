---
title: Adatelemzési feladatok végrehajtása – csoportos adatelemzési folyamat
description: Egy adatelemző projekt nyomon követhető, vezérelt és együttműködő módon végezhető el az adatelemzési projektek végrehajtásában.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477153"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Adatelemzési feladatok végrehajtása: feltárás, modellezés és üzembe helyezés

A tipikus adatelemzési feladatok közé tartozik az adatok feltárása, modellezése és üzembe helyezése. Ez a cikk bemutatja, hogyan használható az interaktív adatelemzési **, elemzési és jelentéskészítési (ideaer-)** és **automatizált modellezési és jelentéskészítési (-)** segédprogramok számos olyan általános adatelemzési feladat végrehajtásához, mint például az interaktív adatok feltárása, az adatok elemzése, a jelentéskészítés és a modellek létrehozása. A modellek éles környezetben történő üzembe helyezésének lehetőségei a következők lehetnek:

- [Azure Machine Learning](../index.yml)
- [SQL-Server ML-szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> feltárás 

Az adattudós különböző módokon végezheti el a feltárást és jelentéskészítést: a Pythonhoz elérhető kódtárak és csomagok (például matplotlib) vagy az R (ggplot vagy Lattice) használatával. Az adatszakértők számára az ilyen kódok testreszabhatók, hogy illeszkedjenek az adatfeltárás igényeihez bizonyos forgatókönyvek esetében. A strukturált adat kezelésére vonatkozó igények különböznek a strukturálatlan adatmennyiségek, például a szöveg vagy a képek esetében. 

Az olyan termékek, mint a Azure Machine Learning [speciális adatelőkészítést](../how-to-create-register-datasets.md) biztosítanak az adathuzavona és-feltáráshoz, beleértve a szolgáltatások létrehozását is. A felhasználónak meg kell határoznia az igényeiknek legmegfelelőbb eszközöket, kódtárakat és csomagokat. 

A fázis végén elkészített termék egy adatfeltárási jelentés. A jelentésnek átfogó képet kell adnia a modellezéshez használt adatmennyiségről és annak felméréséről, hogy az adott információ alkalmas-e a modellezési lépés folytatására. A csoportos adatelemzési, modellezési és jelentéskészítési szakaszban a következő szakaszokban ismertetett TDSP-segédprogramok szabványosított adatfelderítési és-modellezési jelentéseket is biztosítanak. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktív adatelemzési, elemzési és jelentéskészítési funkció a IDEAer segédprogram használatával

Az R Markdown-alapú vagy Python notebook-alapú segédprogram rugalmas és interaktív eszközt biztosít az adathalmazok kiértékeléséhez és megismeréséhez. A felhasználók gyorsan hozhatnak létre jelentéseket az adatkészletből a minimális kódolással. A felhasználók a gombokra kattintva exportálhatók a kutatási eredmények az interaktív eszközben egy végleges jelentésbe, amely az ügyfeleknek továbbítható, vagy a következő modellezési lépésben felhasználható változók létrehozásához használható.

Jelenleg az eszköz csak a memóriában lévő adatkereteken működik. YAML-fájlra van szükség a felderíteni kívánt adatok paramétereinek megadásához. További információ: [ideaer in TDSP Adattudományi segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modellezés

A különböző nyelveken számos eszközkészletet és csomagot kell betanítani a modellekhez. Az adatszakértőknek minden eddiginél szívesebben kell használniuk, ha a pontossággal és késéssel kapcsolatos teljesítménnyel kapcsolatos megfontolások teljesülnek a releváns üzleti használati esetekben és a termelési forgatókönyvekben.

A következő szakasz bemutatja, hogyan használható az R-alapú TDSP segédprogram a félig automatizált modellezéshez. Ez az elválasztó az alapszintű modellek gyors létrehozásához, valamint a jobb teljesítményt biztosító modell kialakításához szükséges paraméterekhez használható.
A következő modellek kezelése szakasz azt mutatja be, hogyan lehet a rendszer a több modell regisztrálására és kezelésére.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modell betanítása: modellezés és jelentéskészítés az a kihasználó eszközzel

Az [automatizált modellezési és jelentéskészítési (](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) a "a") segédprogram egy testreszabható, félig automatizált eszközt biztosít a modell létrehozásához a Hyper-paraméter-megtakarítással és a modellek pontosságának összehasonlításához. 

A modell-létrehozási segédprogram egy olyan R Markdown-fájl, amely képes önálló HTML-kimenetet készíteni a tartalomjegyzékből a különböző részeken található egyszerű navigáláshoz. A rendszer három algoritmust hajt végre a Markdown-fájl futtatásakor (kötött): a glmnet-csomag, a véletlenszerű erdő és a randomForest-csomag használatával végzett, valamint a fák xgboost-csomag használatával történő növelésével a szabályos regressziót. Ezen algoritmusok mindegyike egy betanított modellt hoz létre. Az ilyen modellek pontossága összehasonlítható, és a rendszer a relatív funkciók fontosságát ábrázolja. Jelenleg két segédprogram létezik: az egyik a bináris besorolási feladathoz, a másik pedig egy regressziós feladathoz. A köztük lévő elsődleges különbségek a paraméterek és a pontosság mérőszámai, amelyek az ilyen tanulási feladatokhoz vannak megadva. 

A YAML-fájlok a következő megadására használhatók:

- az adatbevitel (egy SQL-forrás vagy egy R-adat fájl) 
- az adatgyűjtési és a tesztelési célú elemek milyen részét használják a képzéshez
- a futtatandó algoritmusok 
- a modell optimalizálásának vezérlési paramétereinek kiválasztása:
    - több ellenőrzés 
    - bootstrapping
    - kereszt-ellenőrzési munkák
- az egyes algoritmusokhoz tartozó Hyper-paraméter-készletek. 

A YAML fájlban az algoritmusok száma, az optimalizáláshoz szükséges behajtott érték, a Hyper-Parameters és a Hyper-Parameters készletek száma is módosítható a modell gyors futtatásához. Előfordulhat például, hogy alacsonyabb számú KtgE betöltéssel, kisebb számú paraméterrel lehet futtatni. Ha ez indokolt, akkor az is előfordulhat, hogy átfogóbban, nagyobb számú CV-bevezetéssel vagy több paraméter-készlettel is futhatnak.

További információ: [automatizált modellezési és jelentéskészítési segédprogram a TDSP Adattudományi segédprogramokban](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellkezelés
Több modell létrehozása után általában a modellek regisztrálásához és kezeléséhez szükséges rendszernek kell lennie. Általában parancsfájlok vagy API-k, valamint háttér-adatbázis vagy verziószámozási rendszer kombinációja szükséges. A következő felügyeleti feladatokhoz néhány lehetőség közül választhat:

1. [Azure Machine Learning – modell-kezelési szolgáltatás](../index.yml)
2. [ModelDB az MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-kiszolgáló modell-felügyeleti rendszerként](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> üzembe helyezés

Az éles környezetbe állítás lehetővé teszi, hogy egy modell aktív szerepet játsszon a vállalaton belül. Az üzembe helyezett modellből származó előrejelzések üzleti döntések meghozatalára is használhatók.

### <a name="production-platforms"></a>Üzemi platformok
A modellek éles környezetben való üzembe helyezéséhez különböző megközelítések és platformok tartoznak. Íme néhány lehetőség:


- [Modell üzembe helyezése Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Az üzembe helyezés előtt az egyiknek biztosítania kell, hogy a modell pontozásának késése elég alacsony legyen az éles környezetben való használathoz.
>
>

További példákat talál az útmutatókban, amelyek a folyamat összes lépését mutatják be **adott forgatókönyvek**esetén. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához.

> [!NOTE]
> A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B tesztelés
Ha több modell van éles környezetben, hasznos lehet a [/B teszt](https://en.wikipedia.org/wiki/A/B_testing) végrehajtása a modellek teljesítményének összehasonlításához. 

 
## <a name="next-steps"></a>További lépések

Az adatelemzési [projektek előrehaladásának követésével](track-progress.md) megtudhatja, hogy az adattudós hogyan követheti nyomon az adatelemzési projekt állapotát.

A [Model művelet és a CI/CD](ci-cd-flask.md) azt mutatja be, hogyan hajtható végre a CI/CD a kifejlesztett modellekkel.


