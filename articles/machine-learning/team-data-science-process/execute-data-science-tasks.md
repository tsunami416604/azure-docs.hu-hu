---
title: Adatok tudományos műveleteket - Azure Machine Learning |} Microsoft Docs
description: Hogyan egy adatok tudósok is végre lehet hajtani egy adatok tudományos projektet egy trackable szabályozott verzióját és együttműködési módot.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: f0e0c04e48a551db6d7d7cd221a35e267dc0a63f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Tudományos feladatok végrehajtása: feltárása, a modellezési és a központi telepítés

Jellemző adatok tudományos feladatok közé tartozik, az adatok feltárása, modellezési és központi telepítését. Ez a cikk bemutatja, hogyan használható a **interaktív adatok feltárása, elemzési és jelentéskészítési (IDEAR)** és **automatikus modellezéséhez és jelentéskészítési (AMAR)** segédprogramok közös adatok tudományos több feladat végrehajtására például az adatok interaktív áttekintését, adatelemzés, jelentéskészítési és modell létrehozását. Azt is ismerteti, üzembe helyezéséhez egy modell segítségével számos különféle eszközök gazdag és adatok platformokon, például a következő üzemi környezetbe beállítások:

- [Azure Machine Learning](../service/index.yml)
- [SQL-kiszolgáló az ML-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Feltárása 

Egy adatok tudósok végezheti feltárására és az sokféleképpen reporting: függvénytár vagy csomag elérhető a Python (például matplotlib) vagy r (ggplot vagy például rácsszerkezetű). Adatszakértőkön testre szabhatja az ilyen kódot, hogy az adott forgatókönyveket adatok feltárása igényeinek. Eltérőek a strukturált adatok kezelésével funkciókra van szüksége, amely a strukturálatlan adatok, például szöveget vagy képeket. 

Például az Azure Machine Learning-munkaterület is kínálnak [adatok előkészítése speciális](../desktop-workbench/tutorial-bikeshare-dataprep.md) wrangling adatokat és feltárása, beleértve a szolgáltatás létrehozása. A felhasználó határoznia kell az eszközöket, könyvtárakat és csomagok, amelyek az ajánlott suite igényeiknek megfelelően. 

A termék ebben a fázisban végén az adatok feltárása jelentést. A jelentés viszonylag átfogó képet kaphat az adatok modellezési használandó és a megfelelő a modellezési lépésben-e az adatok értékelése kell biztosítania. A csapat adatok tudományos folyamat (TDSP) segédprogramok félig automatizált feltárási a következő szakaszban tárgyalt, modellezési és a reporting is biztosít szabványosított adatok feltárása és jelentések modellezési. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Adatok interaktív áttekintését, elemzési és jelentéskészítési IDEAR segédprogram használatával

A markdown-alapú R vagy Python notebook alapú segédprogram értékelje ki és felfedezés adatkészletek rugalmas és interaktív eszközt biztosít. Felhasználók gyorsan előállíthat olyan jelentéseket a minimális kódolási adatkészletből. Felhasználók rákattinthatnak gombok eszköz interaktív adatkutatási eredményez exportálhat egy végső jelentést, amely ügyfelek felé vagy használ a döntést arról, mely tartalmazza az ezt követő modellezési lépésben változókat.

Ilyenkor az eszközt csak akkor működik a memóriában adatkeretek. Az adatkészlet vizsgálják paramétereket egy YAM fájl szükséges. További információkért lásd: [TDSP adatok tudományos segédprogramok IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modellezési

Számos eszközök gazdag és a különböző nyelveken tanítási modell csomagjai vannak. Adatszakértőkön kell szabadon használhatja, amelyet Melyik néhányat a meglévők közül, amíg a teljesítménnyel kapcsolatos szempontok pontosság és a késés kapcsolódó üzleti teljesülnek, a Feladatkezelő esetek és éles forgatókönyvek használja.

A következő szakasz bemutatja, hogyan használható az R-alapú TDSP segédprogram félig automatizált modellezési. A AMAR segédprogram segítségével készítése talál sor modellek gyorsan, valamint a paramétereket, így jobban végrehajtása kell hangolni kell a modell.
A következő modell felügyeleti szakasz mutatja be, a rendszer regisztrálja, és több modellek kezeléséhez.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>A modell betanítási: modellezéséhez és jelentéskészítés a AMAR segédprogrammal

A [automatikus modellezéséhez és jelentéskészítési (AMAR) segédprogram](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) modell létrehozását a hyper-paraméter abszolút végrehajtásához, és hasonlítsa össze ezeket a modell pontosságát testre szabható, félig automatizált eszközt biztosít. 

A modell létrehozása segédprogram egy R Markdown-fájl, amely a különböző szakaszok egyszerű kezelhetőség tartalomjegyzék önálló HTML kimenet előállításához futtatható. Három algoritmusok végrehajtásának futtatásakor a Markdown-fájl (knit): a glmnet használatával rendeződik regressziós csomag, a randomForest csomag használata, és a xgboost csomaggal fák kiemelése véletlenszerű erdő). Ezek az algoritmusok mindegyikének hoz létre a modell betanítását. Ezek a modellek pontosságát majd képest, és a szolgáltatás relatív fontosságát előkészítésére jelentik. Jelenleg nincsenek két segédprogramok: egyet az bináris osztályozási feladat és egy regressziós feladat. Egymás közötti elsődleges különbségek módon paramétereit, és pontossága metrikák tanulási feladatok is meg van adva. 

Egy YAM fájl használatával adja meg:

- a bemeneti adatokhoz (egy R-adatfájl vagy egy SQL-forrás) 
- az adatok milyen része képzési használja, és milyen arányban teszteléshez
- Futtassa mely algoritmusokat 
- a modell optimalizálásához paraméterek kiválasztása:
    - kereszt-ellenőrzési 
    - rendszerindítása
    - a kereszt-ellenőrzési modellrészt
- a hyper-paraméter állandóként állítja be, minden egyes algoritmushoz. 

Algoritmusok, optimális modellrészt számát, a hyper-paraméterek számát, és a hyper-paraméterkészletei keresztül továbbítsa számát is módosíthatja a modellek gyorsan futtatásához Yam-fájlban. Például azok is futtatható KtgE modellrészt, kevesebb paraméterkészletei kevesebb. Ha indokolt, azokat is futtatható több átfogó KtgE modellrészt nagyobb számú vagy paraméterkészletei nagyobb számú.

További információkért lásd: [automatikus modellezési és segédprogram Reporting TDSP adatok tudományos segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellkezelés
Után több modellek készített, általában regisztrációja és kezelése a modellek rendelkeznie kell. Általában parancsfájlok vagy az API-k és a háttérrendszer adatbázis vagy versioning van szüksége. A felügyeleti feladatok is figyelembe venni néhány lehetőségek közül választhat:

1. [Az Azure Machine Learning - modell felügyeleti szolgáltatás](../service/index.yml)
2. [A MIT ModelDB](https://mitdbg.github.io/modeldb/) 
3. [SQL-seerver modell felügyeleti rendszert](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>központi telepítés

Éles környezet lehetővé teszi, hogy a modell aktív szerepet játszanak üzleti. Egy telepített modell által használható üzleti döntéseket hozhat.

### <a name="production-platforms"></a>Éles platformok
Nincsenek különböző szempontok és platformokat a modellek üzembe helyezésre. Íme néhány lehetőség:


- [Az Azure Machine Learning modell telepítése](../desktop-workbench/model-management-overview.md)
- [Központi telepítési modell SQL-kiszolgálón](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Megjegyzés: Telepítés előtt egy kifejezetten a Tiltás késése a modell pontozása túl alacsonyak ahhoz éles környezetben használandó biztosítja.
>

További példákat is találhatók forgatókönyvek, amelyek azt mutatják, a folyamat lépései **meghatározott forgatókönyvek**. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása.

Megjegyzés: a központi telepítés Azure Machine Learning Studio használatával, lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>A / B tesztelés
Ha több modellek éles környezetben, ez hasznos lehet a végrehajtásához [A / B tesztelés](https://en.wikipedia.org/wiki/A/B_testing) teljesítmény modell összehasonlítására. 

 
## <a name="next-steps"></a>További lépések

[Adatok tudományos projektek előrehaladását úgy követheti nyomon](track-progress.md) bemutatja, hogyan egy adatok tudósok nyomon követheti a adatok tudományos projekt.
 


