---
title: Modellek létrehozása a AutoML használatával & üzembe helyezése
titleSuffix: Azure Machine Learning
description: Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése Azure Machine Learningokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 429471c2a24b90f14241bf54197c4baecb27e5c0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660430"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre, vizsgálhat és helyezhet üzembe automatizált gépi tanulási modelleket a Azure Machine Learning Studióban.

>[!IMPORTANT]
> Az Azure Machine learning Studióban az automatikus ML-élmény előzetes verzióban érhető el. Előfordulhat, hogy bizonyos funkciók nem támogatottak vagy korlátozott képességekkel rendelkeznek.

 Az automatizált gépi tanulás az a folyamat, amelyben az adott adataihoz használandó legjobb gépi tanulási algoritmus van kiválasztva. Ez a folyamat lehetővé teszi a gépi tanulási modellek gyors létrehozását. [További információ az automatizált gépi tanulásról](concept-automated-ml.md).
 
A végpontok közötti példaként próbálja ki az [oktatóanyagot a besorolási modell létrehozásához Azure Machine learning automatikus ml-felületével](tutorial-first-experiment-automated-ml.md). 

Python-kód alapú felhasználói felület esetén [konfigurálja az automatizált gépi tanulási kísérleteket](how-to-configure-auto-train.md) az Azure Machine learning SDK-val.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy **nagyvállalati kiadású**Azure Machine learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).  Ha egy meglévő munkaterületet nagyvállalati kiadásra szeretne frissíteni, tekintse meg [a frissítés az Enterprise Edition](how-to-manage-workspace.md#upgrade)rendszerre című

## <a name="get-started"></a>Bevezetés

1. Jelentkezzen be Azure Machine Learning a következő címen: https://ml.azure.com . 

1. Válassza ki az előfizetést és a munkaterületet. 

1. Navigáljon a bal oldali panelre. A **Szerző** szakaszban válassza az **automatikus ml** lehetőséget.

[![Azure Machine Learning Studio navigációs panelje](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Ha első alkalommal végez kísérleteket, egy üres lista jelenik meg, amely a dokumentációra mutató hivatkozásokat tartalmaz. 

Ellenkező esetben megjelenik a legújabb gépi tanulási kísérletek listája, beleértve azokat is, amelyeket az SDK-val hoztak létre. 

## <a name="create-and-run-experiment"></a>Kísérlet létrehozása és futtatása

1. Válassza az **+ új AUTOMATIZÁLT ml Futtatás** lehetőséget, és töltse ki az űrlapot.

1. Válasszon ki egy adatkészletet a tárolóból, vagy hozzon létre egy új adatkészletet. Az adatkészletek helyi fájlokból, webes URL-címekből, adattárolóból vagy Azure Open-adatkészletből hozhatók létre. További információ az [adatkészletek létrehozásáról](how-to-create-register-datasets.md).  

    >[!Important]
    > A betanítási adatgyűjtésre vonatkozó követelmények:
    >* Az adatokat táblázatos formában kell megadni.
    >* A megjósolni kívánt értéknek (célként megadott oszlopnak) jelen kell lennie az adatsorokban.

    1. Ha új adatkészletet szeretne létrehozni a helyi számítógépen lévő fájlból, válassza az **+ adatkészlet létrehozása** lehetőséget, majd válassza **a helyi fájlból**lehetőséget. 

    1. Az **alapszintű információ** űrlapon adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. Kattintson a **tovább** gombra az **adattár és a fájl kiválasztása űrlap**megnyitásához. Ezen az űrlapon választhatja ki az adatkészlet feltöltésének helyét; a munkaterülettel automatikusan létrehozott alapértelmezett tároló, vagy válasszon egy, a kísérlethez használni kívánt tárolót. 
    
        1. Ha az adatai egy virtuális hálózat mögött vannak, engedélyeznie kell az **érvényesítési funkció kihagyását** annak biztosításához, hogy a munkaterület hozzáférhessen az adataihoz. További információ: [Azure Machine learning Studio használata Azure-beli virtuális hálózaton](how-to-enable-studio-virtual-network.md). 
    
    1. Válassza a **Tallózás** lehetőséget az adatkészlet adatfájljának feltöltéséhez. 

    1. A pontosság érdekében tekintse át a **beállításokat és az előnézet** űrlapot. Az űrlap intelligensen van feltöltve a fájl típusa alapján. 

        Mező| Leírás
        ----|----
        Fájlformátum| Meghatározza a fájlban tárolt adatelrendezést és-típust.
        Elválasztó| Egy vagy több karakter, amely egy egyszerű szövegben vagy más adatfolyamban található különálló, egymástól független régiók között határozza meg a határt.
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
    Virtuális gép prioritása| Az alacsony prioritású virtuális gépek olcsóbbak, de nem garantálják a számítási csomópontokat. 
    Virtuális gép típusa| Válassza a CPU vagy a GPU lehetőséget a virtuális gép típusához.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz.
    Csomópontok minimális/maximális száma| A profilhoz legalább 1 csomópontot kell megadnia. Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6 csomópont a pénzmosás-számításokhoz.
    Speciális beállítások | Ezek a beállítások lehetővé teszik a felhasználói fiók és a meglévő virtuális hálózat konfigurálását a kísérlethez. 
    
    Kattintson a **Létrehozás** gombra. Egy új számítás létrehozása néhány percet is igénybe vehet.

    >[!NOTE]
    > A számítási név azt jelzi, hogy a kiválasztott számítási/létrehozási *profil engedélyezve*van-e. (További részletekért lásd az [adatok profilkészítési](#profile) szakaszát).

    Kattintson a **Tovább** gombra.

1. A feladattípus **és beállítások** űrlapon válassza ki a feladattípust: besorolás, regresszió vagy előrejelzés. További információért lásd: [támogatott](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) feladattípusok.

    1. **Besorolás**esetén a Deep learning is engedélyezhető.
    
        Ha a Deep learning engedélyezve van, az érvényesítés _train_validation felosztásra_korlátozódik. [További információ az ellenőrzési lehetőségekről](how-to-configure-cross-validation-data-splits.md).


    1. Az **előrejelzéshez** használhatja a következőt: 
    
        1. Mély tanulás engedélyezése.
    
        1. Válassza ki az *Time oszlopot*: ez az oszlop a használni kívánt időértékeket tartalmazza.

        1. *Előrejelzési horizont*kiválasztása: azt jelzi, hogy a modell hány időegységet (perc/óra/nap/hét/hónap/év) fog tudni a jövőben előre jelezni. Minél több modellre van szükség a jövőbeli előrejelzéshez, annál kevésbé pontos lesz. [További információ az előrejelzési és előrejelzési horizontról](how-to-auto-train-forecast.md).

1. Választható További konfigurációs beállítások megtekintése: további beállítások, amelyekkel hatékonyabban vezérelheti a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza. 

    További konfigurációk|Description
    ------|------
    Elsődleges metrika| A modell pontozásához használt fő metrika. [További információ a modell metrikáinak használatáról](how-to-configure-auto-train.md#primary-metric).
    A legjobb modell ismertetése | Az engedélyezés vagy a Letiltás lehetőség kiválasztásával megjelenítheti a javasolt legjobb modell magyarázatait. <br> Ez a funkció jelenleg nem érhető el [bizonyos előrejelzési algoritmusokhoz](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Letiltott algoritmus| Válassza ki azokat az algoritmusokat, amelyeket ki szeretne zárni a betanítási feladatokból. <br><br> Az algoritmusok engedélyezése csak [SDK-kísérletekhez](how-to-configure-auto-train.md#supported-models)érhető el. <br> Tekintse meg az egyes feladattípusok [támogatott modelljeit](https://docs.microsoft.com/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels?view=azure-ml-py&preserve-view=true).
    Kilépési feltétel| Ha bármelyik feltétel teljesül, a betanítási feladatok leállnak. <br> *Képzési idő (óra)*: meddig kell futtatni a betanítási feladatot. <br> *Metrika pontszámának küszöbértéke*: minimális metrikai pontszám minden folyamathoz. Ez biztosítja, hogy ha egy meghatározott cél mérőszámot szeretne elérni, a szükségesnél több időt sem kell megadnia a betanítási feladatra.
    Érvényesítés| Válassza ki a betanítási feladatokban használni kívánt több ellenőrzési lehetőséget. <br> [További információ a Cross Validation](how-to-configure-cross-validation-data-splits.md#prerequisites)szolgáltatásról.<br> <br>Az előrejelzés csak a k-fold kereszt-ellenőrzést támogatja.
    Egyidejűség| *Maximális párhuzamos ismétlések*: a betanítási feladatokban a folyamatok maximális száma (iteráció). A feladattípus nem fog futni a megadott számú iterációnál.

1. Választható Featurization-beállítások megtekintése: Ha úgy dönt, hogy engedélyezi az **automatikus featurization** a **további konfigurációs beállítások** űrlapon, a rendszer az alapértelmezett featurization technikákat alkalmazza. A **featurization-beállítások megtekintése** lehetőséggel módosíthatja ezeket az alapértelmezett értékeket, és ennek megfelelően testreszabhatja azokat. Megtudhatja, hogyan [szabhatja testre a featurizations](#customize-featurization). 

    ![Azure Machine Learning Studio-feladattípus űrlapja](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Adatprofilkészítés & összefoglaló statisztikái

Az adatkészletek széles skáláját veheti igénybe, így ellenőrizheti, hogy az adatkészlete ML-re van-e állítva. A nem numerikus oszlopokhoz csak olyan alapszintű statisztikák tartoznak, mint a min, a Max és a errors. A numerikus oszlopok esetében statisztikai pillanataikat és becsült quantiles is áttekintheti. Az adatprofil a következőket tartalmazza:

>[!NOTE]
> A lényegtelen típusokkal rendelkező funkciók esetében üres bejegyzések jelennek meg.

Statisztikai adatok|Description
------|------
Jellemző| Az összesíteni kívánt oszlop neve.
Profil| Beágyazott vizualizáció a következtetett típus alapján. Például a karakterláncok, a logikai értékek és a dátumok értékének számít, míg a tizedesjegyek (numerikus értékek) közelítő hisztogramtal rendelkeznek. Ez lehetővé teszi az adateloszlás gyors megismerését.
Típus eloszlása| Az oszlopokban lévő típusok soron belüli értékeinek száma. A nullák a saját típusúak, így ez a vizualizáció hasznos lehet a páratlan vagy hiányzó értékek észleléséhez.
Típus|Az oszlop késleltetett típusa. A lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyek.
Min| Az oszlop minimális értéke. Az üres bejegyzések olyan szolgáltatások esetében jelennek meg, amelyeknek a típusa nem rendelkezik a bennük rejlő sorrendtel (például logikai értékekkel).
Max| Az oszlop maximális értéke. 
Darabszám| Az oszlop hiányzó és nem hiányzó bejegyzéseinek száma.
Hiányzó darabszám| A hiányzó oszlop bejegyzéseinek száma. Az üres karakterláncok és hibák értékekként vannak kezelve, így nem járulnak hozzá a "nem hiányzó darabszám" értékhez.
Kvantilisek| Az egyes quantile megközelített értékek biztosítják az adatok terjesztésének érzékét.
Középérték| Az oszlop számtani középértéke vagy átlaga.
Szórás| Az oszlop adateloszlásának vagy variációjának mértékét.
Variance| Az oszlop adatainak kiszóródásának mértéke az átlagos értéktől számítva. 
Ferdeség| Az oszlop adatainak normál eloszlásból való megmérése.
Csúcsosságát| Azt méri, hogy az oszlop adatmennyisége milyen mértékben lett összehasonlítva a normál eloszlással.

## <a name="customize-featurization"></a>Featurization testreszabása

A **Featurization** űrlapon engedélyezheti vagy letilthatja az automatikus Featurization, és testre szabhatja a kísérlet automatikus Featurization beállításait. Az űrlap megnyitásához tekintse meg a [kísérlet létrehozása és futtatása](#create-and-run-experiment) című szakasz 10. lépését. 

A következő táblázat összefoglalja a studión keresztül jelenleg elérhető testreszabásokat. 

Oszlop| Testreszabás
---|---
Tartalmazza | Meghatározza, hogy mely oszlopok szerepeljenek a betanításhoz.
Szolgáltatás típusa| Módosítsa a kijelölt oszlop értékének típusát.
Imputált| Válassza ki, hogy milyen értékkel kell bevennie a hiányzó értékeket az adatokkal.

![Azure Machine Learning Studio-feladattípus űrlapja](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatása és eredmények megtekintése

A kísérlet futtatásához kattintson a **Befejezés** gombra. A kísérlet előkészítése akár 10 percet is igénybe vehet. A betanítási feladatok és az egyes folyamatok futtatásának befejezése további 2-3 percet is igénybe vehet.

### <a name="view-experiment-details"></a>A kísérlet részleteinek megtekintése

Megnyílik a **Futtatás részletei** képernyő a **részletek** lapon. Ez a képernyő a kísérlet futtatásának összegzését jeleníti meg, beleértve a futtatási szám melletti állapotsort is. 

A **Modellek** lapon a létrehozott modellek listája található metrikaérték szerint rendezve. Alapértelmezés szerint az a modell, amely a kiválasztott metrika alapján a legmagasabb értékű, a lista tetején jelenik meg. A betanítási feladat több modellt is kipróbál, amelyek sorra felkerülnek a listára. Ennek segítségével gyorsan összehasonlíthatja az addig létrehozott modellek metrikáját.

[![Futtatás részletei irányítópult](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Képzések futtatási részleteinek megtekintése

A befejezett modellek részletezésével megtekintheti a képzések futtatásának részleteit, **például a modell lapon vagy** a **mérőszámok** lapon lévő teljesítmény-mérőszám diagramokon a modell összefoglalását. További információ a [diagramokról](how-to-understand-automated-ml.md).

[![Iteráció részletei](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Ha már kéznél van a legjobb modell, új adatok előrejelzéséhez helyezze üzembe webszolgáltatásként.

Az automatizált ML segít programozás nélkül üzembe helyezni a modellt:

1. Van néhány lehetőség a telepítéshez. 

    + 1. lehetőség: a legjobb modell üzembe helyezése az Ön által meghatározott metrikai feltételek alapján. 
        1. A kísérlet befejezése után navigáljon a szülő futtatása lapra a képernyő felső részén található **1. Futtatás** lehetőség kiválasztásával. 
        1.  Válassza ki a modellt a **legjobb modell összegzése** szakaszban. 
        1. Válassza a **telepítés** lehetőséget az ablak bal felső részén. 

    + 2. lehetőség: egy adott modell iterációjának üzembe helyezése ebből a kísérletből.
        1. Válassza ki a kívánt modellt a **Modellek** lapról
        1. Válassza a **telepítés** lehetőséget az ablak bal felső részén.

1. Töltse ki a **modell üzembe helyezése** ablaktáblát.

    Mező| Érték
    ----|----
    Név| Adja meg a központi telepítés egyedi nevét.
    Description| Adja meg a leírását, hogy jobban azonosítható legyen a központi telepítés.
    Számítási típus| Válassza ki a telepíteni kívánt végpont típusát: *Azure Kubernetes Service (ak)* vagy *Azure Container instance (ACI)*.
    Számítási név| *Csak ak-ra vonatkozik:* Válassza ki a telepíteni kívánt AK-fürt nevét.
    Hitelesítés engedélyezése | Ezzel a beállítással engedélyezheti a jogkivonat-alapú vagy a kulcs alapú hitelesítést.
    Egyéni központi telepítési eszközök használata| Engedélyezze ezt a funkciót, ha fel szeretné tölteni a saját pontozási parancsfájlját és a környezeti fájlját. [További információk a pontozószkriptekről](how-to-deploy-and-where.md).

    >[!Important]
    > A fájlneveknek 32 karakternél rövidebbnek kell lenniük, és betűkkel kell kezdődnie és végződnie. A neveken belül használhat gondolatjeleket, aláhúzásjeleket, pontokat és alfanumerikus karaktereket. A szóköz nem engedélyezett.

    A *Speciális* menü olyan alapértelmezett üzembehelyezési funkciókat kínál, mint például az [adatgyűjtési](how-to-enable-app-insights.md) és az erőforrás-használati beállítások. Ha felül kívánja írni ezeket az alapértelmezett beállításokat, akkor ebben a menüben megteheti.

1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés nagyjából 20 percet vesz igénybe.
    Az üzembe helyezés megkezdése után megjelenik a **Modell összegzése** lap. Az üzembe helyezés folyamatát az **Üzembe helyezés állapota** szakaszban követheti nyomon. 

Ezzel használatba állított egy előrejelzéseket készítő webszolgáltatást! Az előrejelzések teszteléséhez kérdezze le a szolgáltatást a [Power BI beépített Azure Machine Learning-támogatásából](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan használhat webszolgáltatásokat](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* Az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md).
* [További információ az automatikus gépi tanulásról és a](concept-automated-ml.md) Azure Machine Learningról.
