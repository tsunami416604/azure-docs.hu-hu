---
title: Az autoML használatával modelleket hozhat létre & üzembe helyezéséhez
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning használatával automatizált gépi tanulási modelleket hozhat létre, véleményezhet és helyezhet üzembe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257232"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése az Azure Machine Learning segítségével
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hozhat létre, fedezhet fel és helyezhet üzembe automatizált gépi tanulási modelleket egyetlen kódsor nélkül az Azure Machine Learning stúdiófelületén. Az automatizált gépi tanulás egy olyan folyamat, amelyben a kiválasztott legjobb gépi tanulási algoritmus tválasztja ki az Ön számára az adott adatokhoz. Ez a folyamat lehetővé teszi a gépi tanulási modellek gyors generálását. [További információ az automatizált gépi tanulásról.](concept-automated-ml.md)
 
Példának a végéig próbálkozzon az [oktatóanyaggal, amely az Azure Machine Learning automatikus gépi tanulási felületével hoz létre besorolási modellt.](tutorial-first-experiment-automated-ml.md) 

A Python-kódalapú élményhez [konfigurálja az automatikus gépi tanulási kísérleteket](how-to-configure-auto-train.md) az Azure Machine Learning SDK-val.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Azure Machine Learning-munkaterület **nagyvállalati**kiadástípussal. Lásd: [Azure Machine Learning-munkaterület létrehozása.](how-to-manage-workspace.md)  Meglévő munkaterület Enterprise edition re való frissítéséhez olvassa el [a Frissítés nagyvállalati kiadásra című témakört.](how-to-manage-workspace.md#upgrade)

## <a name="get-started"></a>Bevezetés

1. Jelentkezzen be az Azure https://ml.azure.comMachine Learning beadására a itt. 

1. Válassza ki az előfizetést és a munkaterületet. 

1. Keresse meg a bal oldali ablaktáblát. Válassza **az Automatikus ml lehetőséget** a **Szerző** szakaszban.

[![Az Azure Machine Learning stúdió navigációs ablaktáblája](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Ha ez az első alkalom, hogy kísérleteket végez, megjelenik egy üres lista és a dokumentációra mutató hivatkozások. 

Ellenkező esetben megjelenik a legutóbbi automatikus gépi tanulási kísérletek listája, beleértve az SDK-val létrehozottakat is. 

## <a name="create-and-run-experiment"></a>Kísérlet létrehozása és futtatása

1. Válassza a **+ Új automatikus ml-futtatás lehetőséget,** és feltöltse az űrlapot.

1. Jelöljön ki egy adatkészletet a tárolótárolóból, vagy hozzon létre egy új adatkészletet. Az adatkészletek helyi fájlokból, webes URL-címekből, adattárakból vagy Azure-ban nyitott adatkészletekből hozhatók létre. 

    >[!Important]
    > A képzési adatokra vonatkozó követelmények:
    >* Az adatoknak táblázatos formában kell lenniük.
    >* Az előre jelezni kívánt értéknek (céloszlop) jelen kell lennie az adatokban.

    1. Ha új adatkészletet szeretne létrehozni a helyi számítógépen lévő fájlból, válassza a **Tallózás** gombot, majd jelölje ki a fájlt. 

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy nem kötelező leírást. 

    1. Válassza a **Tovább** gombot az Adattár és a **fájlválasztó űrlap**megnyitásához. Ezen az űrlapon kiválaszthatja, hogy hová szeretné feltölteni az adatkészletet; az alapértelmezett tároló, amely automatikusan létrejön a munkaterülettel, vagy válasszon egy tárolótárolót, amelyet a kísérlethez használni szeretne. 

    1. Tekintse át a **Beállítások és előnézeti** űrlapot a pontosságérdekében. Az űrlap a fájltípus alapján intelligensen van feltöltve. 

        Mező| Leírás
        ----|----
        Fájlformátum| A fájlban tárolt adatok elrendezését és típusát határozza meg.
        Elválasztó| Egy vagy több karakter a különálló, független területek közötti határ megadásához egyszerű szövegben vagy más adatfolyamokban.
        Encoding| Azt határozza meg, hogy milyen karaktersématáblát használjon az adatkészlet olvasásához.
        Oszlopfejlécek| Azt jelzi, hogy az adatkészlet fejléceit (ha vannak ilyenek) hogyan kezeli a rendszer.
        Sorok kihagyása | Azt jelzi, hogy hány, ha van ilyen sor kimarad az adatkészletből.
    
        Válassza a **Tovább lehetőséget.**

    1. A **Séma** űrlap intelligens en a Beállítások **és az előnézet** i. Itt konfigurálja az egyes oszlopok adattípusát, tekintse át az oszlopneveket, és válassza ki, hogy mely **oszlopokat szeretné nem szerepelni** a kísérlethez. 
            
        Válassza a **Tovább gombot.**

    1. A **Részletek megerősítése** űrlap az **Alapszintű adatok** és beállítások és **előnézeti** űrlapokon korábban feltöltött információk összegzése. Azt is beállíthatja, hogy hozzon létre egy adatprofilt az adatkészlet hez egy profilkészítési engedélyezett számítási használatával. További információ az [adatprofilozásról.](#profile)

        Válassza a **Tovább lehetőséget.**
1. Jelölje ki az újonnan létrehozott adatkészletet, ha megjelenik. Megtekintheti az adatkészlet és a mintastatisztikák előnézetét is. 

1. A **Futtatás konfigurálása** képernyőn adjon meg egy egyedi kísérletnevet.

1. Jelöljön ki egy céloszlopot; ez az az oszlop, amelyen előrejelzéseket szeretne tenni.

1. Válasszon ki egy számítási feladatot az adatprofilkészítési és betanítási feladathoz. A meglévő számítási feladatok listája a legördülő listában érhető el. Új számítás létrehozásához kövesse a 7.

1. Válassza **az Új számítás létrehozása** lehetőséget a kísérlet számítási környezetének konfigurálásához.

    Mező|Leírás
    ---|---
    Számítási név| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási.
    Min / Max csomópontok (speciális beállítások)| A profiladatok hoz meg kell adnia 1 vagy több csomópontot. Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6 csomópont egy AML-számításhoz.
    
    Kattintson a **Létrehozás** gombra. Egy új számítás létrehozása eltarthat néhány percig.

    >[!NOTE]
    > A számítási név jelzi, ha a kiválasztott/létrehozott számítás engedélyezve van a *profilkészítés.* (További részletekért lásd az [adatprofilkészítés](#profile) szakaszt).

    Válassza a **Tovább lehetőséget.**

1. A **Feladat típusa és beállításai** képernyőn válassza ki a feladat típusát: besorolás, regresszió vagy előrejelzés. További [információ: Tevékenységtípusok definiálása](how-to-define-task-type.md) további információkért.

    1. A besoroláshoz engedélyezheti a mélytanulást is, amelyet a szöveg jellemzőinkhez használnak.

    1. Előrejelzéshez:
        1. Időoszlop kiválasztása: Ez az oszlop a használandó időadatokat tartalmazza.

        1. Előrejelzési horizont kiválasztása: Adja meg, hogy a modell hány időegységet (perc/óra/nap/hét/hónap/év) tud előre jelezni a jövőre nézve. Minél tovább van szükség a modell megjósolni a jövőben, annál kevésbé lesz pontos lesz. [További információ az előrejelzési és előrejelzési horizontról.](how-to-auto-train-forecast.md)

1. (Nem kötelező) További konfigurációs beállítások megtekintése: további beállítások, amelyekkel jobban szabályozhatja a betanítási feladatot. Ellenkező esetben az alapértelmezett értékek a kísérlet kiválasztása és az adatok alapján kerülnek alkalmazásra. 

    További konfigurációk|Leírás
    ------|------
    Elsődleges mutató| A modell pontozásához használt fő metrika. [További információ a modellmutatókról.](how-to-configure-auto-train.md#explore-model-metrics)
    Automatikus featurization| Jelölje be, ha engedélyezni vagy letiltani szeretné az automatikus gépi tanulás által végzett előfeldolgozást. Az előfeldolgozás magában foglalja az automatikus adattisztítást, előkészítést és átalakítást a szintetikus funkciók létrehozásához. Nem támogatott az idősorozat-előrejelzési tevékenység típusában. [További információ az előfeldolgozásról.](#featurization) 
    Magyarázza el a legjobb modellt | Válassza az engedélyezéshez vagy a letiltáshoz, hogy megmutathassa az ajánlott legjobb modell magyarázhatóságát
    Blokkolt algoritmus| Válassza ki azokat az algoritmusokat, amelyeket ki szeretne zárni a betanítási feladatból.
    Kilépési feltétel| Ha a feltételek bármelyike teljesül, a képzési feladat leáll. <br> *Képzési feladat ideje (óra)*: Mennyi ideig, hogy a képzési feladat futtatásához. <br> *Metrika pontszám küszöbértéke:* Az összes folyamat minimális metrikapontszáma. Ez biztosítja, hogy ha egy meghatározott célmetrikát szeretne elérni, nem tölt több időt a képzési feladat, mint szükséges.
    Ellenőrzés| Válassza ki a képzési feladatban használni kívánt keresztellenőrzési lehetőségek egyikét. [További információ a keresztérvényesítésről.](how-to-configure-auto-train.md)
    Egyidejűség| *Maximális egyidejű ismétlések:* A képzési feladatban tesztelésre alkalmas folyamatok (iterációk) maximális száma. A feladat nem fog futni a megadott számú ismétlésnél többet.

1. (Nem kötelező) Jellemzőbeállítások megtekintése: ha engedélyezi az **Automatikus jellemző funkciót** a További **konfigurációs beállítások** képernyőn, ezen az űrlapon adhatja meg, hogy mely oszlopokon hajtsa végre a featurizations-eket, és válassza ki, hogy melyik statisztikai értéket használja a hiányzó értékimputálásokhoz.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Adatprofilkészítés i. & összefoglaló statisztikái

Az adathalmazban számos összefoglaló statisztikát kaphat annak ellenőrzéséhez, hogy az adatkészlet ml-kompatibilis-e. A nem numerikus oszlopok esetében csak az olyan alapvető statisztikákat tartalmazzák, mint a min, a max és a hibaszám. Numerikus oszlopok esetén megtekintheti a statisztikai pillanataikat és a becsült kvantitatív elemeket is. Adatprofilunk konkrétan a következőket tartalmazza:

>[!NOTE]
> Üres bejegyzések jelennek meg a nem releváns típusú szolgáltatásoknál.

Statisztika|Leírás
------|------
Szolgáltatás| Az összegzett oszlop neve.
Profil| A kikövetkeztetett típuson alapuló in-line megjelenítés. Például a karakterláncok, a logikai értékek és a dátumok értékszámmal rendelkeznek, míg a tizedesjegyek (numerikusszámok) hozzávetőleges histogramokat. Ez lehetővé teszi az adatok terjesztésének gyors megértését.
Típuselosztás| Az oszlopon belüli típusok sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba sorba A null értékek a saját típusuk, ezért ez a vizualizáció a páratlan vagy hiányzó értékek észleléséhez hasznos.
Típus|Az oszlop kikövetkeztetett típusa. A lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyek.
Min| Az oszlop minimális értéke. Üres bejegyzések jelennek meg olyan szolgáltatásoknál, amelyek típusa nem tartalmaz belső sorrendet (pl. logikai).
Max| Az oszlop maximális értéke. 
Darabszám| Az oszlopban lévő hiányzó és nem hiányzó bejegyzések teljes száma.
Nem hiányzik a darabszám| Az oszlopban nem hiányzó bejegyzések száma. Az üres karakterláncokat és hibákat a rendszer értékként kezeli, így azok nem járulnak hozzá a "nem hiányzik számhoz".
Kvantitúk| Az adatok eloszlásának érzékelése érdekében minden kvantilisnél hozzávetőleges értékek.
Középérték| Az oszlop számtani középértékének vagy átlagának.
Szórás| Az oszlop adatainak diszperziós mennyiségének vagy változásának mérése.
Eltérés| Annak mérése, hogy az oszlop adatai milyen mértékben terjednek el az átlagos értéktől. 
Ferdeség| Annak mérése, hogy az oszlop adatai mennyire különböznek a normál eloszlástól.
Kurtózis| Annak mérése, hogy az oszlop adatai milyen erősen vannak összemérve a normál eloszlással.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Speciális funkciók beállításai

Az automatizált gépi tanulás automatikusan kínál előfeldolgozást és adatkorlátokat, hogy segítsen azonosítani és kezelni az adatokkal kapcsolatos esetleges problémákat. 

### <a name="preprocessing"></a>Előfeldolgozás

> [!NOTE]
> Ha az automatikusan létrehozott ML modelleket [ONNX-modellbe](concept-onnx.md)kívánja exportálni, az ONNX formátumban csak a *-val jelzett featurization beállítások támogatottak. További információ a [modellek ONNX-re való konvertálásáról.](concept-automated-ml.md#use-with-onnx) 

|Lépések&nbsp;előfeldolgozása| Leírás |
| ------------- | ------------- |
|Nagy számosság eldobása vagy varianciajellemzők nélkül* |Ezeket a betanítási és érvényesítési készletekből, beleértve az okat a funkciókat, amelyek minden értékhiányzik, azonos értéket az összes sorban, vagy rendkívül magas számosságú (például kivék, azonosítók vagy GUID-k).|
|Imputált hiányzó értékek* |Numerikus jellemzők esetén az oszlopban lévő értékek átlagával kell impeskedni.<br/><br/>A kategorikus funkciók, impimpa a leggyakoribb érték.|
|További funkciók létrehozása* |DateTime funkciók esetén: Év, Hónap, Nap, Hét napja, Év napja, Negyedév, Az év hete, Óra, Perc, Másodperc.<br/><br/>Szöveg jellemzők: Kifejezés gyakorisága alapján unigramm, két gramm, és három karakter-gramm.|
|Átalakítás és kódolás *|A kevés egyedi értékkel rendelkező numerikus jellemzők kategorikus jellemzőkké alakulnak át.<br/><br/>Az egy forró kódolás alacsony számossági kategorikus; a nagy számosság, egy forró hash kódolás.|
|Word beágyazások|Szöveg featurizer, amely átalakítja vektorok szöveges tokenek mondatvektorok egy előre betanított modell használatával. A program összesíti a dokumentumba beágyazó vektort, hogy dokumentumjellemző-vektort hozlétre.|
|Célkódolások|A kategorikus jellemzők esetében az egyes kategóriákat a regressziós problémák átlagos célértékével, valamint az egyes osztályok osztályvalószínűségével vannak leképezve a besorolási problémákhoz. A ritka adatkategóriák által okozott feltérképezés és zaj túlzott felszerelésének csökkentése érdekében frekvenciaalapú súlyozást és k-szeres keresztellenőrzést alkalmaznak.|
|Szöveges célkódolás|A szövegbevitelhez egy halmozott lineáris modellt használ szótáskával az egyes osztályok valószínűségének létrehozásához.|
|A bizonyítékok súlya (WoE)|Kiszámítja a WoE-t a kategorikus oszlopok és a céloszlop korrelációjának mértékén. Kiszámítása az osztályon kívüli és osztályon kívüli valószínűségek arányának naplójaként kerül kiszámításra. Ez a lépés osztályonként egy numerikus jellemzőoszlopot ad ki, és szükségtelenül csökkenti a hiányzó értékek és a kiugró értékű kezelés kifejezett imputálásának szükségességét.|
|Fürt távolsága|K-means fürtözési modellt képez be az összes numerikus oszlopban.  K új szolgáltatásokat, fürtenként egy új numerikus funkciót ad ki, amely tartalmazza az egyes minták távolságát az egyes fürtök centroidjával.|

### <a name="data-guardrails"></a>Adatkorlátok

Az adatkorlátok akkor kerülnek alkalmazásra, ha az automatikus jellemzőbeállítás engedélyezve van, vagy az érvényesítés automatikusra van állítva. Az adatkorlátok segítségével azonosíthatja az adatokkal kapcsolatos lehetséges problémákat (pl. hiányzó értékek, osztálykiegyensúlyozatlanság), és segít a korrekciós intézkedések elvégzésében a jobb eredmények érdekében. 

A felhasználók áttekinthetik az adatkorlátokat a stúdióban egy automatikus ml-futtatás **adatkorlátlapján** belül, vagy beállíthatják, ```show_output=True``` amikor egy kísérletet küld a Python SDK használatával. 

#### <a name="data-guardrail-states"></a>Adatvédő sínállamok

Az adatkorlátok a következő három állapot egyikét jelenítik meg: **Átadott**, **Kész**vagy **Riasztás.**

Állapot| Leírás
----|----
Átment| A rendszer nem észlelt adatproblémákat, és nincs szükség felhasználói műveletre. 
Kész| A program módosításokat alkalmazott az adatokon. Javasoljuk a felhasználóknak, hogy tekintsék át az automatizált ml korrekciós intézkedéseket annak biztosítása érdekében, hogy a módosítások igazodjanak a várt eredményekhez. 
Figyelmeztették| A rendszer nem pótolható adatproblémát észlelt. Javasoljuk a felhasználóknak, hogy vizsgálják felül és oldják meg a problémát. 

>[!NOTE]
> Az automatikus ml-kísérletek korábbi verziói egy negyedik állapotot jelenítek meg: **Javítva**. Az újabb kísérletek nem jelenítik meg ezt az állapotot, és a **Rögzített** állapotot megjelenítő összes korlát ekkor a **Kész**.   

Az alábbi táblázat ismerteti a jelenleg támogatott adatkorlátokat és a kapcsolódó állapotokat, amelyekkel a felhasználók a kísérlet elküldésekor találkoznak.

Védőkorlát|status|Az&nbsp;&nbsp;eseményindító feltétele
---|---|---
Hiányzó jellemzőértékek imputálása |**Átment** <br><br><br> **Kész**| A program nem észlelt hiányzó jellemzőértékeket a betanítási adatokban. További információ a [hiányzó értékimputálásról.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Hiányzó jellemzőértékek észlelése a betanítási adatok és imputed.
Nagy számosságú funkciókezelés |**Átment** <br><br><br> **Kész**| A bemenetek elemzése történt, és nem észleltek magas számossági jellemzőket. További információ a [magas számosságú funkciók észleléséről.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> A rendszer nagy számossági jellemzőket észlelt a bemeneteken, és kezelte őket.
Érvényesítési felosztás kezelése |**Kész**| *Az érvényesítési konfiguráció "auto" volt beállítva, és a betanítási adatok **kevesebb** mint 20 000 sort tartalmaztak.* <br> A betanított modell minden egyes iterációja keresztellenőrzéssel lett érvényesítve. További információ az [érvényesítési adatokról.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *Az érvényesítési konfiguráció "auto" volt beállítva, és a betanítási adatok **több** mint 20 000 sort tartalmaztak.* <br> A bemeneti adatok at egy betanítási adatkészletre és egy érvényesítési adatkészletre osztották fel a modell érvényesítéséhez.
Osztálykiegyensúlyozás észlelése |**Átment** <br><br><br><br> **Figyelmeztették** | A bemenetek elemzése, és minden osztály kiegyensúlyozott a betanítási adatok. Egy adatkészlet akkor tekinthető kiegyensúlyozottnak, ha minden osztály megfelelő reprezentatimával rendelkezik az adatkészletben, a minták számával és arányával mérve. <br><br><br> A rendszer kiegyensúlyozatlan osztályokat észlelt a bemenetekben. A modell torzításának javítása a kiegyensúlyozási probléma megoldásához. További információ a [kiegyensúlyozatlan adatokról.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Memóriaproblémák észlelése |**Átment** <br><br><br><br> **Kész** |<br> A program elemezte a kiválasztott {horizont, lag, gördülő ablak} értéket, és nem észlelt memóriabeli problémákat. További információ az [idősorozat-előrejelzési konfigurációkról.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>A program elemezte a kiválasztott {horizon, lag, rolling window} értékeket, és a kísérlet memóriafogytását okozhatja. A késés vagy a gördülő ablakkonfigurációk ki vannak kapcsolva.
Frekvenciaészlelés |**Átment** <br><br><br><br> **Kész** |<br> A rendszer elemezte az idősorokat, és az összes adatpont az észlelt frekvenciához igazodik. <br> <br> A rendszer elemezte az idősorokat, és olyan adatpontokat észlelt, amelyek nem illeszkednek az észlelt frekvenciához. Ezeket az adatpontokat eltávolították az adatkészletből. További információ [az idősorozat-előrejelzésre való felkészülésről.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatása és eredmények megtekintése

A kísérlet futtatásához válassza a **Befejezés** lehetőséget. A kísérlet előkészítése akár 10 percet is igénybe vehet. A betanítási feladatok és az egyes folyamatok futtatásának befejezése további 2-3 percet is igénybe vehet.

### <a name="view-experiment-details"></a>A kísérlet részleteinek megtekintése

A **Részletek futtatása** képernyő megnyílik a **Részletek** lapon. Ez a képernyő a kísérletfuttatásának összegzését jeleníti meg, beleértve a futtatási szám melletti állapotsort is. 

A **Modellek** lapon a létrehozott modellek listája található metrikaérték szerint rendezve. Alapértelmezés szerint az a modell, amely a kiválasztott metrika alapján a legmagasabb értékű, a lista tetején jelenik meg. A betanítási feladat több modellt is kipróbál, amelyek sorra felkerülnek a listára. Ennek segítségével gyorsan összehasonlíthatja az addig létrehozott modellek metrikáját.

[![Részletes irányítópult futtatása](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Az edzésfuttatás részleteinek megtekintése

Részletezze a befejezett modellek bármelyikét, és tekintse meg a betanítási futtatás részleteit, például a **Modell részletei** lapon lévő metrikák futtatását vagy a Képi megjelenítések lap **teljesítménydiagramjait.** [További információ a diagramokról.](how-to-understand-automated-ml.md)

[![Iteráció részletei](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Miután a legjobb modell kéznél, itt az ideje, hogy üzembe, mint egy webszolgáltatás megjósolni az új adatok.

Az automatikus ml kód írása nélkül segíti a modell üzembe helyezését:

1. Van néhány lehetőség a telepítésre. 

    + 1. lehetőség: A legjobb modell üzembe helyezéséhez (a megadott metrikafeltételek szerint), válassza ki a **Telepítés a legjobb modell** gombot a **Részletek** lapon.

    + 2. lehetőség: Ha egy adott modell iterációt szeretne telepíteni ebből a kísérletből, részletezze a modellt a **Modell részletei** lap megnyitásához, és válassza a **Modell telepítése**lehetőséget.

1. A Modell telepítése ablaktábla **feltöltése.**

    Mező| Érték
    ----|----
    Név| Adja meg a központi telepítés egyedi nevét.
    Leírás| Adjon meg egy leírást, hogy jobban azonosíthassa, mire szolgál ez a központi telepítés.
    Számítási típus| Válassza ki a telepíteni kívánt végpont típusát: *Az Azure Kubernetes-szolgáltatás (AKS)* vagy az *Azure Container Instance (ACI).*
    Számítási név| *Csak az AKS-re vonatkozik:* Válassza ki annak az AKS-fürtnek a nevét, amelybe telepíteni szeretné.
    Hitelesítés engedélyezése | Jelölje be, ha lehetővé szeretné tenni a jogkivonat- vagy kulcsalapú hitelesítést.
    Egyéni központi telepítési eszközök használata| Engedélyezze ezt a funkciót, ha saját pontozási parancsfájlt és környezeti fájlt szeretne feltölteni. [További információ a parancsfájlok pontozásáról.](how-to-deploy-and-where.md#script)

    >[!Important]
    > A fájlneveknek 32 karakternél kisebbnek kell lenniük, és alfanumerikus értékkel kell kezdődniük és végződniük. Lehetnek kötőjelek, aláhúzások, pontok és alfanumerikusak között. Szóközök nem engedélyezettek.

    A *Speciális* menü alapértelmezett telepítési funkciókat kínál, például [adatgyűjtési](how-to-enable-app-insights.md) és erőforrás-kihasználtsági beállításokat. Ha felül szeretné írni ezeket az alapértelmezett értékeket, ezt ebben a menüben tegye meg.

1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés körülbelül 20 percet is igénybe vehet.

Most már van egy operatív webszolgáltatás generálni előrejelzések! Az előrejelzéseket a [Power BI beépített Azure Machine Learning-támogatásából](how-to-consume-web-service.md#consume-the-service-from-power-bi)származó szolgáltatás lekérdezésével tesztelheti.

## <a name="next-steps"></a>További lépések

* [További információ a webszolgáltatások felhasználásáról.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
* [Ismerje meg az automatizált gépi tanulási eredményeket.](how-to-understand-automated-ml.md)
* [További információ az automatizált gépi tanulásról](concept-automated-ml.md) és az Azure Machine Learningről.
