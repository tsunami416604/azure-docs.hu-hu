---
title: Modellek létrehozása a autoML használatával & üzembe helyezése
titleSuffix: Azure Machine Learning
description: Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése Azure Machine Learningokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 9999d74bf6bef3e8351460add7efc8bdbfcd1045
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127096"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre, vizsgálhat és helyezhet üzembe automatizált gépi tanulási modelleket anélkül, hogy a Azure Machine Learning Studio felületén egyetlen soros kódot kellene létrehoznia. Az automatizált gépi tanulás az a folyamat, amelyben az adott adataihoz használandó legjobb gépi tanulási algoritmus van kiválasztva. Ez a folyamat lehetővé teszi a gépi tanulási modellek gyors létrehozását. [További információ az automatizált gépi tanulásról](concept-automated-ml.md).
 
A végpontok közötti példaként próbálja ki az [oktatóanyagot a besorolási modell létrehozásához Azure Machine learning automatikus ml-felületével](tutorial-first-experiment-automated-ml.md). 

Python-kód alapú felhasználói felület esetén [konfigurálja az automatizált gépi tanulási kísérleteket](how-to-configure-auto-train.md) az Azure Machine learning SDK-val.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy **nagyvállalati kiadású**Azure Machine learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).  Ha egy meglévő munkaterületet nagyvállalati kiadásra szeretne frissíteni, tekintse meg [a frissítés az Enterprise Edition](how-to-manage-workspace.md#upgrade)rendszerre című

## <a name="get-started"></a>Első lépések

1. Jelentkezzen be Azure Machine Learningra https://ml.azure.comcímen. 

1. Válassza ki az előfizetést és a munkaterületet. 

1. Navigáljon a bal oldali panelre. A **Szerző** szakaszban válassza az **automatikus ml** lehetőséget.

[![Azure Machine Learning Studio navigációs panelje](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Ha első alkalommal végez kísérleteket, egy üres lista jelenik meg, amely a dokumentációra mutató hivatkozásokat tartalmaz. 

Ellenkező esetben megjelenik a legújabb gépi tanulási kísérletek listája, beleértve azokat is, amelyeket az SDK-val hoztak létre. 

## <a name="create-and-run-experiment"></a>Kísérlet létrehozása és futtatása

1. Válassza az **+ új AUTOMATIZÁLT ml Futtatás** lehetőséget, és töltse ki az űrlapot.

1. Válasszon ki egy adatkészletet a tárolóból, vagy hozzon létre egy új adatkészletet. Az adatkészletek helyi fájlokból, webes URL-címekből, adattárolóból vagy Azure Open-adatkészletből hozhatók létre. 

    >[!Important]
    > A betanítási adatgyűjtésre vonatkozó követelmények:
    >* Az adatokat táblázatos formában kell megadni.
    >* A megjósolni kívánt értéknek (célként megadott oszlopnak) jelen kell lennie az adatsorokban.

    1. Ha új adatkészletet szeretne létrehozni a helyi számítógépen található fájlból, válassza a **Tallózás** lehetőséget, majd válassza ki a fájlt. 

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. Kattintson a **tovább** gombra az **adattár és a fájl kiválasztása űrlap**megnyitásához. Ezen az űrlapon választhatja ki az adatkészlet feltöltésének helyét; a munkaterülettel automatikusan létrehozott alapértelmezett tároló, vagy válasszon egy, a kísérlethez használni kívánt tárolót. 

    1. A pontosság érdekében tekintse át a **beállításokat és az előnézet** űrlapot. Az űrlap intelligensen van feltöltve a fájl típusa alapján. 

        Mező| Leírás
        ----|----
        Fájl formátuma| Meghatározza a fájlban tárolt adatelrendezést és-típust.
        Elválasztó karakter| Egy vagy több karakter, amely egy egyszerű szövegben vagy más adatfolyamban található különálló, egymástól független régiók között határozza meg a határt.
        Encoding| Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.
    
        Kattintson a **Tovább** gombra.

    1. A **séma** űrlapja intelligens módon van feltöltve a **beállítások és az előnézet** űrlapon megadott beállítások alapján. Itt konfigurálhatja az egyes oszlopok adattípusát, áttekintheti az oszlopnevek nevét, és kiválaszthatja, hogy mely oszlopok **ne szerepeljenek** a kísérletben. 
            
        Válassza a **tovább lehetőséget.**

    1. A **részletek megerősítése** űrlap az **alapszintű információ** és **beállítások és az előnézet** űrlapon korábban feltöltött információk összegzése. Lehetősége van arra is, hogy adatprofilt hozzon létre az adatkészlethez egy profilkészítést engedélyező számítás használatával. További információ az [adatprofilkészítésről](#profile).

        Kattintson a **Tovább** gombra.
1. Válassza ki az újonnan létrehozott adatkészletet, amint megjelenik. Emellett megtekintheti az adatkészlet és a minta statisztikáinak előnézetét is. 

1. A **Run (Futtatás** ) űrlapon adja meg a kísérlet egyedi nevét.

1. Válasszon ki egy cél oszlopot; Ez az az oszlop, amelynek a előrejelzéseit el szeretné végezni.

1. Válassza ki az adatprofilkészítési és-betanítási feladatokhoz tartozó számítási feladatot. A meglévő számítások listája elérhető a legördülő menüben. Új számítás létrehozásához kövesse a 7. lépésben szereplő utasításokat.

1. Válassza az **új számítás létrehozása** lehetőséget a számítási környezet konfigurálásához a kísérlethez.

    Mező|Leírás
    ---|---
    Számítási név| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.
    Csomópontok minimális/maximális száma (speciális beállításokban)| A profilhoz legalább 1 csomópontot kell megadnia. Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6 csomópont a pénzmosás-számításokhoz.
    
    Kattintson a **Létrehozás** gombra. Egy új számítás létrehozása néhány percet is igénybe vehet.

    >[!NOTE]
    > A számítási név azt jelzi, hogy a kiválasztott számítási/létrehozási *profil engedélyezve*van-e. (További részletekért lásd az [adatok profilkészítési](#profile) szakaszát).

    Kattintson a **Tovább** gombra.

1. A feladattípus **és beállítások** űrlapon válassza ki a feladattípust: besorolás, regresszió vagy előrejelzés. További információért lásd: tevékenységtípusok [definiálása](how-to-define-task-type.md) .

    1. A besoroláshoz is engedélyezheti a mély tanulást, amelyet a Text featurizations is használhat.

    1. Előrejelzés:
        1. Válassza ki az Time oszlopot: ez az oszlop a használni kívánt időértékeket tartalmazza.

        1. Előrejelzési horizont kiválasztása: azt jelzi, hogy a modell hány időegységet (perc/óra/nap/hét/hónap/év) fog tudni a jövőben előre jelezni. Minél több modellre van szükség a jövőbeli előrejelzéshez, annál kevésbé pontos lesz. [További információ az előrejelzési és előrejelzési horizontról](how-to-auto-train-forecast.md).

1. Választható További konfigurációs beállítások megtekintése: további beállítások, amelyekkel hatékonyabban vezérelheti a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza. 

    További beállítások|Leírás
    ------|------
    Elsődleges metrika| A modell pontozásához használt fő metrika. [További információ a modell metrikáinak használatáról](how-to-configure-auto-train.md#explore-model-metrics).
    Automatikus featurization| Ezzel a beállítással engedélyezheti vagy letilthatja az automatikus gépi tanulás által végzett előfeldolgozást. Az előfeldolgozás magában foglalja az automatikus adattisztítást, előkészítést és átalakítást szintetikus funkciók létrehozásához. Az idősorozat-előrejelzés feladattípusa nem támogatott. [További információ az előfeldolgozásról](#featurization). 
    A legjobb modell ismertetése | Az engedélyezés vagy a Letiltás lehetőség kiválasztásával megjelenítheti a javasolt legjobb modell magyarázatát
    Letiltott algoritmus| Válassza ki azokat az algoritmusokat, amelyeket ki szeretne zárni a betanítási feladatokból.
    Kilépési feltétel| Ha bármelyik feltétel teljesül, a betanítási feladatok leállnak. <br> *Képzési idő (óra)* : meddig kell futtatni a betanítási feladatot. <br> *Metrika pontszámának küszöbértéke*: minimális metrikai pontszám minden folyamathoz. Ez biztosítja, hogy ha egy meghatározott cél mérőszámot szeretne elérni, a szükségesnél több időt sem kell megadnia a betanítási feladatra.
    Ellenőrzés| Válassza ki a betanítási feladatokban használni kívánt több ellenőrzési lehetőséget. [További információ a Cross Validation](how-to-configure-auto-train.md)szolgáltatásról.
    Egyidejűség| *Maximális párhuzamos ismétlések*: a betanítási feladatokban a folyamatok maximális száma (iteráció). A feladattípus nem fog futni a megadott számú iterációnál.

1. Választható Featurization-beállítások megtekintése: Ha úgy dönt, hogy engedélyezi az **automatikus featurization** a **további konfigurációs beállítások** űrlapon, ezen az űrlapon adhatja meg, hogy mely oszlopok hajtják végre ezeket a featurizations, és válassza ki, hogy melyik statisztikai értéket kívánja használni a hiányzó érték imputations.

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
Típus|Az oszlop késleltetett típusa. A lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyek.
Min| Az oszlop minimális értéke. Az üres bejegyzések olyan szolgáltatások esetében jelennek meg, amelyeknek a típusa nem rendelkezik a bennük rejlő sorrendtel (például logikai értékekkel).
Max| Az oszlop maximális értéke. 
Darabszám| Az oszlop hiányzó és nem hiányzó bejegyzéseinek száma.
Hiányzó darabszám| A hiányzó oszlop bejegyzéseinek száma. Az üres karakterláncok és hibák értékekként vannak kezelve, így nem járulnak hozzá a "nem hiányzó darabszám" értékhez.
Quantiles| Az egyes quantile megközelített értékek biztosítják az adatok terjesztésének érzékét.
középérték| Az oszlop számtani középértéke vagy átlaga.
Szórás| Az oszlop adateloszlásának vagy variációjának mértékét.
Variancia| Az oszlop adatainak kiszóródásának mértéke az átlagos értéktől számítva. 
Döntés| Az oszlop adatainak normál eloszlásból való megmérése.
Értékek| Azt méri, hogy az oszlop adatmennyisége milyen mértékben lett összehasonlítva a normál eloszlással.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Speciális featurization beállítások

Az automatizált gépi tanulás automatikusan biztosítja az előfeldolgozást és az guardrails, így könnyebben azonosíthatja és kezelheti az adataival kapcsolatos lehetséges problémákat. 

### <a name="preprocessing"></a>Előfeldolgozás

|&nbsp;lépések előfeldolgozása| Leírás |
| ------------- | ------------- |
|Nagy számosságú dobja el vagy nem eltérés szolgáltatások|Ezeket a betanítási és érvényesítési csoportokból is elhúzhatja, beleértve az összes hiányzó értékkel rendelkező szolgáltatásokat, az összes sorban lévő és a rendkívül magas fokú (például kivonatok, azonosítók vagy GUID azonosítók) értékeket.|
|Hiányzó imputálására|Numerikus funkciók esetében az oszlopban szereplő értékek átlaga.<br/><br/>A kategorikus funkciók esetében a leggyakoribb értékkel kell beszámítani az imputált funkciókat.|
|További funkciók létrehozása|Dátum és idő funkciók: év, hónap, nap, hét napja, nap, év, negyedév, az év, óra, perc, másodperc.<br/><br/>A szöveges funkciókhoz: unigrams, bi-gramm és Tri-Character-gramm alapján történő kifejezés gyakorisága.|
|Átalakítás és kódolás |A kevés egyedi értékkel rendelkező numerikus funkciók a kategorikus funkciókba lesznek átalakítva.<br/><br/>A rendszer egy gyors kódolást végez az alacsony szintű a magas fokú, egy gyors kivonatolású kódolás.|
|Word-beágyazások|Szöveges Képtulajdonság, amely egy előre betanított modell használatával átalakítja a szöveges tokenek vektorait a mondatokra. A dokumentumokban lévő összes Word beágyazási vektora össze van összesítve egy dokumentum-szolgáltatás vektorának létrehozásához.|
|Cél kódolások|A kategorikus funkciók esetében az egyes kategóriákat a regressziós problémák átlagos céljának megfelelően leképezi, valamint az osztály valószínűségét az egyes osztályok számára a besorolási problémák esetén. A rendszer a gyakoriságon alapuló súlyozást és a k-fold kereszt-ellenőrzést alkalmazza a leképezések és a ritka adatkategóriák által okozott zaj csökkentése érdekében.|
|Szöveges cél kódolása|Szöveges bevitel esetén a rendszer egy halmozott lineáris modellt használ, amelyben a rendszer az egyes osztályok valószínűségét állítja elő.|
|Bizonyítékok súlyozása (jaj)|Kiszámítja a jaj-et a kategorikus oszlopok korrelációja alapján a cél oszlophoz. A számítás az osztályban és az osztályon kívüli valószínűségek arányának naplója. Ez a lépés egy numerikus funkció oszlopát jeleníti meg, és eltávolítja a hiányzó értékek és a kiugró eljárás explicit módon történő bevonásának szükségességét.|
|Fürt távolsága|A k – a csoportosítási modellt jelenti az összes numerikus oszlopon.  Az új funkciók megjelenítése, egy új numerikus szolgáltatás, amely az egyes minták távolságát az egyes fürtök középpontját tartalmazza.|

### <a name="data-guardrails"></a>Az adatguardrails

A rendszer automatikusan alkalmazza az adatok guardrails, hogy azonosítsa az adataival kapcsolatos lehetséges problémákat (például a hiányzó értékeket, az osztály kiegyensúlyozatlanságát), és segítsen a továbbfejlesztett eredmények kijavításában. Számos ajánlott eljárás érhető el, és a megbízható eredmények elérésére is alkalmazható. 

A következő táblázat ismerteti a jelenleg támogatott guardrails, valamint azokat a társított állapotokat, amelyeket a felhasználók a kísérlet elküldésekor megkaphatnak.

Guardrail|status|&nbsp;trigger feltétele&nbsp;
---|---|---
Hiányzó&nbsp;érték&nbsp;imputálási |**Telt** <br> <br> **Rögzített**|    Nincs hiányzó érték a bemeneti&nbsp;oszlopaiban <br> <br> Egyes oszlopokban hiányoznak értékek
Keresztellenőrzés|**Tenni**|Ha nincs megadva explicit ellenőrzési készlet
Magas&nbsp;kardinális&nbsp;funkció&nbsp;észlelése|    **Telt** <br> <br>**Tenni**|    A rendszer nem észlelt magas szintű sarkalatos funkciókat <br><br> A rendszer a magas kardinális bemeneti oszlopokat észlelte
Osztályok egyenlegének észlelése    |**Telt** <br><br><br>**Riasztást kap** |Az osztályok a betanítási adatként vannak kiegyenlítettek; Az adatkészletek akkor tekinthetők kiegyensúlyozottnak, ha az egyes osztályok jó ábrázolással rendelkeznek az adatkészletben, a minták számának és arányának alapján mérve <br> <br> A betanítási adatkészletek osztályai kiegyensúlyozva vannak
Időbeli adatsorozat-konzisztencia|**Telt** <br><br><br><br> **Rögzített** |<br> A kiválasztott {Horizon, lag, Rolling Window} érték elemzése megtörtént, és a rendszer nem észlelt memóriabeli problémákat. <br> <br>A kiválasztott {Horizon, lag, Rolling Window} értékek elemzése megtörtént, és a kísérlet valószínűleg kifogy a memóriából. A késés vagy a legördülő ablak ki van kapcsolva.

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatása és eredmények megtekintése

A kísérlet futtatásához kattintson a **Befejezés** gombra. A kísérlet előkészítése akár 10 percet is igénybe vehet. A betanítási feladatok és az egyes folyamatok futtatásának befejezése további 2-3 percet is igénybe vehet.

### <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

Megnyílik a **Futtatás részletei** képernyő a **részletek** lapon. Ez a képernyő a kísérlet futtatásának összegzését jeleníti meg, beleértve a futtatási szám melletti állapotsort is. 

A **Modellek** lapon a létrehozott modellek listája található metrikaérték szerint rendezve. Alapértelmezés szerint az a modell, amely a kiválasztott metrika alapján a legmagasabb értékű, a lista tetején jelenik meg. A betanítási feladat több modellt is kipróbál, amelyek sorra felkerülnek a listára. Ennek segítségével gyorsan összehasonlíthatja az addig létrehozott modellek metrikáját.

[![Futtatás részletei irányítópult](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Képzések futtatási részleteinek megtekintése

Az összes befejezett modell részletezésével megtekintheti a képzések futtatásának részleteit, például a **modell részletei** lapon vagy a **vizualizációk** lapon lévő teljesítmény diagramokon található mérőszámok futtatása című témakört. További információ a [diagramokról](how-to-understand-automated-ml.md).

[![iteráció részletei](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Miután megadta a legjobb modellt, itt az ideje, hogy webszolgáltatásként telepítse azt az új adatforrások előrejelzéséhez.

Az automatikus ML a kód írása nélkül segíti a modell üzembe helyezését:

1. Van néhány lehetőség a telepítéshez. 

    + 1\. lehetőség: a legjobb modell üzembe helyezéséhez (az Ön által meghatározott metrikai feltételek alapján) válassza a **legjobb modell telepítése** gombot a **részletek** lapon.

    + 2\. lehetőség: egy adott modell iterációjának üzembe helyezése ebből a kísérletből, részletezés a modellben a modell **részletei** lap megnyitásához, majd válassza a **modell üzembe helyezése**lehetőséget.

1. Töltse ki a **modell üzembe helyezése** ablaktáblát.

    Mező| Érték
    ----|----
    Name (Név)| Adja meg a központi telepítés egyedi nevét.
    Leírás| Adja meg a leírását, hogy jobban azonosítható legyen a központi telepítés.
    Számítási típus| Válassza ki a telepíteni kívánt végpont típusát: *Azure Kubernetes Service (ak)* vagy *Azure Container instance (ACI)* .
    Számítási név| *Csak ak-ra vonatkozik:* Válassza ki a telepíteni kívánt AK-fürt nevét.
    Hitelesítés engedélyezése | Ezzel a beállítással engedélyezheti a jogkivonat-alapú vagy a kulcs alapú hitelesítést.
    Egyéni központi telepítési eszközök használata| Engedélyezze ezt a funkciót, ha fel szeretné tölteni a saját pontozási parancsfájlját és a környezeti fájlját. [További információ a pontozási parancsfájlokról](how-to-deploy-and-where.md#script).

    >[!Important]
    > A fájlneveknek 32 karakternél rövidebbnek kell lenniük, és betűkkel kell kezdődnie és végződnie. Kötőjeleket, aláhúzásokat, pontokat és alfanumerikus elemeket tartalmazhat. Szóközök használata nem engedélyezett.

    A *speciális* menü olyan alapértelmezett központi telepítési funkciókat kínál, mint például [az adatgyűjtés](how-to-enable-app-insights.md) és az erőforrás-kihasználtsági beállítások. Ha ezeket az alapértelmezett értékeket szeretné felülbírálni ebben a menüben.

1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés körülbelül 20 percet vesz igénybe.

Most már rendelkezik egy olyan operatív webszolgáltatással, amely előrejelzéseket készít! Az előrejelzések teszteléséhez a szolgáltatást a [Power bi beépített Azure Machine learning-támogatásával](how-to-consume-web-service.md#consume-the-service-from-power-bi)lehet lekérdezni.

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan használhat webszolgáltatásokat](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* Az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md).
* [További információ az automatikus gépi tanulásról és a](concept-automated-ml.md) Azure Machine Learningról.
