---
title: Gépi tanulási modellek készítése és üzembe helyezése automatizált ML használatával
titleSuffix: Azure Machine Learning service
description: Automatizált gépi tanulási kísérletek létrehozása, kezelése és üzembe helyezése a Azure Portal
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/02/2019
ms.openlocfilehash: 2f6d45613120d02dd96a9fe0a14ce388d20cf0c6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990581"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Automatikus gépi tanulási kísérletek létrehozása, feltárása és üzembe helyezése a Azure Portalban (előzetes verzió)

 Ebből a cikkből megtudhatja, hogyan hozhat létre, vizsgálhat és helyezhet üzembe automatizált gépi tanulási kísérleteket a Azure Portal a kód egyetlen sora nélkül. Az automatizált gépi tanulás automatizálja az adott adataihoz használandó legjobb algoritmus kiválasztásának folyamatát, így gyorsan létrehozhat egy gépi tanulási modellt. [További információ az automatizált gépi tanulásról](concept-automated-ml.md).

 Ha több kód alapú felhasználói élményt szeretne, a Pythonban az [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val is konfigurálhatja az [automatizált gépi tanulási kísérleteket](how-to-configure-auto-train.md) .

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [Azure Machine learning szolgáltatás munkaterületének létrehozása](how-to-manage-workspace.md).

## <a name="get-started"></a>Bevezetés

Navigáljon a munkaterület bal oldali ablaktáblájához. Válassza az automatikus Machine Learning a szerzői műveletek (előzetes verzió) szakaszban.

![Azure Portal navigációs ablaktábla](media/how-to-create-portal-experiments/nav-pane.png)

 Ha első alkalommal végez kísérleteket, látni fogja az **üdvözli az automatikus Machine learning** képernyőt. 

Ellenkező esetben az **automatikus gépi tanulási** irányítópultot láthatja az összes automatizált gépi tanulási kísérlet áttekintésével, beleértve azokat is, amelyeket az SDK-val hoztak létre. Itt szűrheti és megtekintheti a futtatások dátum, kísérlet neve és futás állapota alapján.

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Válassza a **kísérlet létrehozása** lehetőséget, és töltse fel az **új automatikus gépi tanulási kísérlet létrehozása** űrlapot.

1. Adjon meg egy egyedi kísérlet nevét.

1. Válassza ki az adatprofilkészítési és-betanítási feladatokhoz tartozó számítási feladatot. A meglévő számítások listája elérhető a legördülő menüben. Új számítás létrehozásához kövesse a 3. lépésben szereplő utasításokat.

1. Válassza az **új számítás létrehozása** lehetőséget a számítási környezet konfigurálásához a kísérlethez.

    Mező|Leírás
    ---|---
    A számítás neve| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.
    További beállítások| *Minimális csomópont*: Adja meg a számítási csomópontok minimális számát. A pénzmosás-számításhoz használt csomópontok minimális száma 0. Az adatprofilkészítés engedélyezéséhez legalább 1 csomópontnak kell lennie. <br> *Csomópont maximális*száma: Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6 csomópont a pénzmosás-számításokhoz.

      Kattintson a **Létrehozás** gombra. Egy új számítás létrehozása néhány percet is igénybe vehet.

      >[!NOTE]
      > A számítási név azt jelzi, hogy a kiválasztott számítási/létrehozási *profil engedélyezve*van-e. (Lásd a 7b további részleteket az adatok profilkészítésével kapcsolatban.)

1. Válassza ki az adataihoz tartozó Storage-fiókot. 

1. Válasszon egy tárolót.

1. Válasszon ki egy adatfájlt a tárolóból, vagy töltsön fel egy fájlt a helyi számítógépről a tárolóba. A nyilvános előzetes verzió csak a helyi fájlok feltöltését és az Azure Blob Storage fiókokat támogatja.
    >[!Important]
    > A betanítási adatgyűjtésre vonatkozó követelmények:
    >* Az adatokat táblázatos formában kell megadni.
    >* A megjósolni kívánt értéknek (célként megadott oszlopnak) jelen kell lennie az adatsorokban.

    [![Adatfájl kiválasztása](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Az előnézet és a profil lapon további beállításokkal konfigurálhatja a kísérlet adatait.

    1. Az **előnézet** lapon adja meg, hogy az adatai fejléceket tartalmaznak-e, majd válassza ki a szolgáltatásokhoz tartozó funkciókat (oszlopokat) az egyes funkciók oszlopban **szereplő** kapcsoló gombok használatával.

    1. A **profil** lapon megtekintheti az adatprofil [](#profile) funkciót, valamint a terjesztési, a típus és az összefoglalás statisztikáit (középérték, medián, max/min stb.).

        >[!NOTE]
        > A következő hibaüzenet jelenik meg, ha a számítási környezet **nem** profilkészítés engedélyezve: Az *adatok profilkészítése csak olyan számítási célok esetében érhető el, amelyek már futnak*.

1. Válassza ki a betanítási feladatok típusát: besorolás, regresszió vagy előrejelzés.

1. Válassza ki a cél oszlopot; Ez az az oszlop, amelynek a előrejelzéseit el szeretné végezni.

1. Előrejelzés:
    1. Time oszlop kiválasztása: Ez az oszlop a használni kívánt időértékeket tartalmazza.

    1. Előrejelzési horizont kiválasztása: Azt jelzi, hogy hány időegység (perc/óra/nap/hét/hónap/év) várható a modellnek a jövőre nézve. Minél több modellre van szükség a jövőbeli előrejelzéshez, annál kevésbé pontos lesz. [További információ az előrejelzési és előrejelzési horizontról](how-to-auto-train-forecast.md).

1. Választható Speciális beállítások: további beállítások, amelyekkel hatékonyabban vezérelheti a betanítási feladatot.

    Speciális beállítások|Leírás
    ------|------
    Elsődleges metrika| A modell pontozásához használt fő metrika. [További információ a modell metrikáinak használatáról](how-to-configure-auto-train.md#explore-model-metrics).
    Kilépési feltételek| Ha bármelyik feltétel teljesül, a betanítási feladatok a teljes befejezés előtt véget ér. <br> *Tanítási feladatok időpontja (perc)* : Mennyi ideig kell futtatni a betanítási feladatot.  <br> *Ismétlések maximális száma*: A betanítási feladatokban a folyamatok maximális száma (iteráció). A feladattípus nem fog futni a megadott számú iterációnál. <br> *Metrika pontszámának küszöbértéke*:  Az összes folyamat minimális metrikájának pontszáma. Ez biztosítja, hogy ha egy meghatározott cél mérőszámot szeretne elérni, a szükségesnél több időt sem kell megadnia a betanítási feladatra.
    Előfeldolgozás| Ezzel a beállítással engedélyezheti vagy letilthatja az automatikus gépi tanulás által végzett előfeldolgozást. Az előfeldolgozás magában foglalja az automatikus adattisztítást, előkészítést és átalakítást szintetikus funkciók létrehozásához. [További információ az](#preprocess)előfeldolgozásról.
    Érvényesítés| Válassza ki a betanítási feladatokban használni kívánt több ellenőrzési lehetőséget. [További információ a Cross Validation](how-to-configure-auto-train.md)szolgáltatásról.
    Egyidejűség| Válassza ki a multi-core számítások használatakor használni kívánt multi-core korlátozásokat.
    Letiltott algoritmus| Válassza ki azokat az algoritmusokat, amelyeket ki szeretne zárni a betanítási feladatokból.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Adatprofilkészítés & összefoglaló statisztikái

Az adatkészletek széles skáláját veheti igénybe, így ellenőrizheti, hogy az adatkészlete ML-re van-e állítva. A nem numerikus oszlopokhoz csak olyan alapszintű statisztikák tartoznak, mint a min, a Max és a errors. A numerikus oszlopok esetében statisztikai pillanataikat és becsült quantiles is áttekintheti. Az adatprofil a következőket tartalmazza:

>[!NOTE]
> A lényegtelen típusokkal rendelkező funkciók esetében üres bejegyzések jelennek meg.

Statisztika|Leírás
------|------
Funkció| Az összesíteni kívánt oszlop neve.
Profil| Beágyazott vizualizáció a következtetett típus alapján. Például a karakterláncok, a logikai értékek és a dátumok értékének számít, míg a tizedesjegyek (numerikus értékek) közelítő hisztogramtal rendelkeznek. Ez lehetővé teszi az adateloszlás gyors megismerését.
Típus eloszlása| Az oszlopokban lévő típusok soron belüli értékeinek száma. A nullák a saját típusúak, így ez a vizualizáció hasznos lehet a páratlan vagy hiányzó értékek észleléséhez.
Type|Az oszlop késleltetett típusa. A lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyek.
Min| Az oszlop minimális értéke. Az üres bejegyzések olyan szolgáltatások esetében jelennek meg, amelyeknek a típusa nem rendelkezik a bennük rejlő sorrendtel (például logikai értékekkel).
Max| Az oszlop maximális értéke. 
Count| Az oszlop hiányzó és nem hiányzó bejegyzéseinek száma.
Hiányzó darabszám| A hiányzó oszlop bejegyzéseinek száma. Az üres karakterláncok és hibák értékekként vannak kezelve, így nem járulnak hozzá a "nem hiányzó darabszám" értékhez.
Quantiles| Az egyes quantile megközelített értékek biztosítják az adatok terjesztésének érzékét.
középérték| Az oszlop számtani középértéke vagy átlaga.
Standard szórás| Az oszlop adateloszlásának vagy variációjának mértékét.
Variancia| Az oszlop adatainak kiszóródásának mértéke az átlagos értéktől számítva. 
Döntés| Az oszlop adatainak normál eloszlásból való megmérése.
Értékek| Azt méri, hogy az oszlop adatmennyisége milyen mértékben lett összehasonlítva a normál eloszlással.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Speciális előfeldolgozási beállítások

A kísérletek konfigurálásakor engedélyezheti a speciális beállítást `Preprocess`. Ez azt jelenti, hogy a következő adatfeldolgozási és featurization lépések végrehajtása automatikusan történik.

|&nbsp;Lépések előfeldolgozása| Leírás |
| ------------- | ------------- |
|Nagy számosságú dobja el vagy nem eltérés szolgáltatások|Ezeket a betanítási és érvényesítési csoportokból is elhúzhatja, beleértve az összes hiányzó értékkel rendelkező szolgáltatásokat, az összes sorban lévő és a rendkívül magas fokú (például kivonatok, azonosítók vagy GUID azonosítók) értékeket.|
|Hiányzó imputálására|Numerikus funkciók esetében az oszlopban szereplő értékek átlaga.<br/><br/>A kategorikus funkciók esetében a leggyakoribb értékkel kell beszámítani az imputált funkciókat.|
|További funkciók létrehozása|A DateTime-funkciókhoz: Év, hónap, nap, hét napja, év napja, negyedév, év hete, óra, perc, másodperc.<br/><br/>Szöveges funkciók esetén: Unigrams, bi-gramm és Tri-Character-gramm alapján történő kifejezés gyakorisága.|
|Átalakítás és kódolás |A kevés egyedi értékkel rendelkező numerikus funkciók a kategorikus funkciókba lesznek átalakítva.<br/><br/>A rendszer egy gyors kódolást végez az alacsony szintű a magas fokú, egy gyors kivonatolású kódolás.|
|Word-beágyazások|Szöveges Képtulajdonság, amely egy előre betanított modell használatával átalakítja a szöveges tokenek vektorait a mondatokra. A dokumentumokban lévő összes Word beágyazási vektora össze van összesítve egy dokumentum-szolgáltatás vektorának létrehozásához.|
|Cél kódolások|A kategorikus funkciók esetében az egyes kategóriákat a regressziós problémák átlagos céljának megfelelően leképezi, valamint az osztály valószínűségét az egyes osztályok számára a besorolási problémák esetén. A rendszer a gyakoriságon alapuló súlyozást és a k-fold kereszt-ellenőrzést alkalmazza a leképezések és a ritka adatkategóriák által okozott zaj csökkentése érdekében.|
|Szöveges cél kódolása|Szöveges bevitel esetén a rendszer egy halmozott lineáris modellt használ, amelyben a rendszer az egyes osztályok valószínűségét állítja elő.|
|Bizonyítékok súlyozása (jaj)|Kiszámítja a jaj-et a kategorikus oszlopok korrelációja alapján a cél oszlophoz. A számítás az osztályban és az osztályon kívüli valószínűségek arányának naplója. Ez a lépés egy numerikus funkció oszlopát jeleníti meg, és eltávolítja a hiányzó értékek és a kiugró eljárás explicit módon történő bevonásának szükségességét.|
|Fürt távolsága|A k – a csoportosítási modellt jelenti az összes numerikus oszlopon.  Az új funkciók megjelenítése, egy új numerikus szolgáltatás, amely az egyes minták távolságát az egyes fürtök középpontját tartalmazza.|

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatása és eredmények megtekintése

A kísérlet futtatásához kattintson a **Start** gombra. A kísérlet előkészítési folyamata néhány percet vesz igénybe.

### <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

A kísérlet előkészítési fázisának elvégzése után megjelenik a Futtatás részletei képernyő, és megkezdődik a feltöltés. Ez a képernyő teljes listát biztosít a létrehozott modellekről. Alapértelmezés szerint a kiválasztott metrika alapján a legmagasabb pontszám a lista tetején található. Mivel a betanítási feladatok további modelleket próbálnak ki, a rendszer hozzáadja őket az iterációs listához és a diagramhoz. Az iterációs diagram használatával gyorsan összehasonlíthatja az eddig létrehozott modellek metrikáit.

A betanítási feladatok eltarthat egy ideig, amíg az egyes folyamatok futni tudnak.

[![Futtatás részletei irányítópult](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Képzések futtatási részleteinek megtekintése

A kimeneti modellek részletezésével megtekintheti a képzések futtatásának részleteit, például a teljesítménymutatókat és a terjesztési diagramokat. [További információ a diagramokról](how-to-understand-automated-ml.md).

![Iteráció részletei](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Miután megadta a legjobb modellt, itt az ideje, hogy webszolgáltatásként telepítse azt az új adatforrások előrejelzéséhez.

Az automatikus ML a kód írása nélkül segíti a modell üzembe helyezését:

1. Van néhány lehetőség a telepítéshez. 

    + 1\. lehetőség: A legjobb modell üzembe helyezéséhez (az Ön által meghatározott metrikai feltételek alapján) válassza a legjobb modell telepítése lehetőséget a Futtatás részletei lapon.

    + 2\. lehetőség: Ha egy adott modell-iterációt szeretne üzembe helyezni ebből a kísérletből, a modell lefúrásával nyissa meg a Futtatás részletei lapot, és válassza a modell üzembe helyezése lehetőséget.
1. Töltse fel a **modell üzembe helyezése** panelt,

    Mező| Value
    ----|----
    Üzemelő példány neve| Adja meg a központi telepítés egyedi nevét.
    Központi telepítés leírása| Adja meg a leírását, hogy jobban azonosítható legyen a központi telepítés.
    Pontozási parancsfájl| Saját pontozási fájl automatikusan generált vagy feltölthető. [További információ a pontozási parancsfájlról](how-to-deploy-and-where.md#script)
    Környezeti parancsfájl| Saját környezeti fájl automatikusan generált vagy feltölthető.
    >[!Important]
    > A fájlneveknek 32 karakternél rövidebbnek kell lenniük, és betűkkel kell kezdődnie és végződnie. Kötőjeleket, aláhúzásokat, pontokat és alfanumerikus elemeket tartalmazhat. Szóközök használata nem engedélyezett.

1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés körülbelül 20 percet vesz igénybe.

    A telepítés sikeres befejeződése után a következő üzenet jelenik meg.

    ![A telepítés befejeződött](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Most már rendelkezik egy olyan operatív webszolgáltatással, amely előrejelzéseket készít!

## <a name="next-steps"></a>További lépések

* Próbálja ki a végpontok közötti oktatóanyagot, [amellyel létrehozhatja az első AUTOMATIZÁLT ml-kísérletet Azure Machine learning](tutorial-first-experiment-automated-ml.md)használatával. 
* [További információ az automatikus gépi tanulásról és a](concept-automated-ml.md) Azure Machine Learningról.
* Az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md).
* [Ismerje meg, hogyan](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)használhat webszolgáltatásokat.
