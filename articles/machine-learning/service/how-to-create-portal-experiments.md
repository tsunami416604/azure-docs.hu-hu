---
title: Hozzon létre, és ismerje meg a portál kísérletek
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre és kezelhet automatizált machine learning-kísérletek portálon
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 96abef29c5290770d296fb5053007e36d1eaf537
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035448"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Hozzon létre, és Fedezze fel az automatikus machine learning-kísérletek az Azure Portalon (előzetes verzió)

 Ebből a cikkből megismerheti, hogyan hozhat létre, futtassa, és Fedezze fel az automatikus machine learning-kísérletek nélkül egyetlen sornyi programkódot az Azure Portalon. Automatizált gépi tanulási automatizálja a folyamat használatához az adott adatok, így gyorsan létrehozhat egy gépi tanulási modellt a legjobb algoritmus kiválasztása. [További információ az automatikus gépi tanulási](concept-automated-ml.md).

 Egy több kódalapú felület igény szerint is [konfigurálása az automatikus machine learning-példakísérleteket a Pythonban](how-to-configure-auto-train.md) együtt a [Azure Machine Learning-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Bevezetés

Keresse meg a munkaterület a bal oldali panelen. A szerzői műveletek (előzetes verzió) szakaszban válassza ki a Machine Learning automatikus.

![Az Azure portal navigációs ablaktábla](media/how-to-create-portal-experiments/nav-pane.png)

 Ha most először ennek során bármilyen automatikus Machine Learning-kísérletek, látni fogja a következőket:

![Az Azure portal kísérlet kezdőlapja](media/how-to-create-portal-experiments/landing-page.png)

Ellenkező esetben látni fogja az alkalmazások automatikus machine learning irányítópulton áttekinthetők az automatizált machine learning-példakísérleteket, beleértve a futtassa az SDK-val. Itt szűrhet és Fedezze fel a futtatások dátum szerint, kísérletezhet a nevét, és a futtatási állapota.

![Az Azure portal kísérlet irányítópult](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Válassza ki a kísérlet létrehozása gombra való feltöltéséhez a következő formátumot követi.

![Kísérlet űrlap létrehozása](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Adja meg a kísérlet neve.

1. Válassza ki a profilkészítési adatok és a betanítási feladatot egy számítási. A meglévő számítási erőforrások listája érhető el a legördülő listában. Hozzon létre egy új számítás, kövesse az utasításokat a 3. lépésben.

1. Válassza ki a hozzon létre egy új számítási gombra az alábbi ablaktáblán, és konfigurálja a számítási környezet ehhez a kísérlethez.

    ![Új számítási kísérlet létrehozása](media/how-to-create-portal-experiments/create-new-compute.png)

    Mező|Leírás
    ---|---
    A számítás neve| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezet.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számítási.
    További beállítások| *Minimális csomópont*: Adja meg a számítási csomópontok minimális száma. Az AML számítási csomópontok minimális száma: 0. Engedélyezi a profilkészítési adatok, 1 vagy több csomópontot kell rendelkeznie. <br> *Maximális csomópont*: Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6-AML számítási csomópontokat.

      Az új számítási létrehozásának elindításához válassza **létrehozás**. Ez néhány percet is igénybe vehet.

      >[!NOTE]
      > A számítás-neve lesz látható, hogy a számítási, válassza ki vagy hozzon létre *engedélyezve van a profilkészítés*. (7b további részletekért lásd a profilkészítési adatokat).

1. Válassza ki az adatok egy tárfiókot. Nyilvános előzetes verziója csak a helyi fájlok feltöltése és az Azure Blob Storage-fiókokat támogatja.

1. Válassza ki a tárolót.

1. Válasszon ki egy fájlt a storage-tároló, vagy feltölthet egy fájlt a tárolóba a helyi számítógépről.

    ![Válassza ki a kísérlethez adatfájlt](media/how-to-create-portal-experiments/select-file.png)

1. Az előzetes verzió és a profil lapon használja az adatok ehhez a kísérlethez további konfigurálásához.

    1. Az előzetes verzió lapon azt jelzik, ha az adatok tartalmazzák-e a fejlécek, és válassza ki a szolgáltatásokat (oszlop) a képzés a **csomagban foglalt** gombok váltson minden funkció oszlopban.

        ![Adatelőnézet](media/how-to-create-portal-experiments/data-preview.png)

    1. A profil lapon megtekintheti a [adatprofil](#profile) funkció, valamint a terjesztési, típusa és összefoglaló statisztika (átlag, medián, percenként maximális száma és így tovább) az egyes.

        ![Az adatok profil lapon](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > A következő hibaüzenet jelenik meg, ha a számítási környezet **nem** profilkészítés engedélyezve: *Profilkészítési adatok csak akkor érhető el a már futó számítási célokhoz*.

1. Válassza ki a betanítási feladat típusa: besorolási, regressziós vagy előrejelzéseket.

1. Válassza ki a céloszlop. Az oszlop, amely, amelyet szeretne az előrejelzések.

1. Az előrejelzési:
    1. Idő típusú oszlop kiválasztása: Ez az oszlop használható idő adatokat tartalmazza.
    1. Előrejelzési horizon kiválasztása: Adja meg, hány mértékegységét (perc/óra/nap vagy hét/hónap/év) a modell lesz előre tudják jelezni a jövőbeli. A további a modell előre jelezni az elkövetkező a kevésbé pontos válik szükséges. [További információ előrejelzés és előrejelzést horizon](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Nem kötelező) Speciális beállítások: segítségével jobban kézben a betanítási feladat további beállításokat.

    Speciális beállítások|Leírás
    ------|------
    Elsődleges metrika| A modell pontozása használt fő metrikát. [További információk a metrikákról modell](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Kilépési feltételek| Ha ezek a feltételek bármelyike teljesül, a betanítási feladat befejeződik, teljes befejezése előtt. <br> *Betanítási feladat idő (percekben)*: Mennyi ideig, hogy a betanítási feladat futtatásához.  <br> *Az ismétlések maximális száma*: (Az ismétlések) folyamatok tesztelése a betanítási feladat az a maximális számát. A feladat nem fut. több, mint a megadott számú ismétlését. <br> *A metrika pontszám küszöbérték*:  Minimális metrika pontszám összes folyamatokhoz. Ez biztosítja, hogy ha egy meghatározott célhoz metrika szeretne elérni, akkor nem több időt az szükséges, mint a betanítási feladat.
    Előfeldolgozása| Válassza ki az engedélyezni vagy letiltani az előfeldolgozási automatizált gépi tanulás által végzett. Előfeldolgozási tartalmaz Adattisztítás automatikus, előkészítése és átalakítási szintetikus szolgáltatások létrehozásához. [Tudjon meg többet az előfeldolgozási](#preprocess).
    Ellenőrzés| Válassza ki a keresztellenőrzési beállítások használata a betanítási feladat. [Tudjon meg többet közötti érvényesítési](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Egyidejűség| Válassza ki a Többmagos számítási használatakor szeretne Többmagos korlátokat.
    Letiltott algoritmus| Válassza ki a betanítási feladat a kizárni kívánt algoritmusokat.

   ![Speciális beállítások képernyő](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> A mezőkre vonatkozó további információkért kattintson az információk elemleírás.

<a name="profile"></a>

### <a name="data-profiling"></a>Profilkészítési adatok

Összefoglaló statisztikák hatalmas számos különböző ellenőrizni, hogy az adatkészlet ML használatra kész, az adatkészlet kérheti le. Nem numerikus oszlopokhoz mint például a min, max és hibák száma csak alapszintű statisztikákat tartalmazzák. A numerikus oszlopok a statisztikai percet és becsült quantiles is áttekintheti. Pontosabban az adatok profil tartalmazza:

* **A szolgáltatás**: az oszlop, amely az az összegzett neve.

* **Profil**: egy beágyazott Vizualizációk következtetni típusa alapján. Például karakterláncok, logikai és a dátumok lesz értékek száma lehetőségre, amíg a tizedesjegyek (numerics) rendelkezik közelíteni hisztogramok. Ez lehetővé teszi, hogy az adatok eloszlása egy gyors ismereteket szerezhet.

* **Írja be a terjesztési**: egy beágyazott értékek száma típusok egy oszlopban. Null értékek a saját típusát, így a ezt a vizualizációt akkor hasznos, ha páratlan vagy hiányzó értékek észlelése.

* **Típus**: következtetett típusú oszlop. Lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyre.

* **Min**: az oszlop minimális értékét. Üres bejegyzések jelennek meg, amelynek típusa nincs velejáró sorrend (például a logikai értékek) funkciók.

* **Maximális**: az oszlop maximális értéke. Például a "minimum", üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Száma**: hiányzó és a nem hiányzó bejegyzések a oszlop teljes száma.

* **Nem hiányzik a szám**: az oszlopban lévő bejegyzéseket, amelyek nem hiányzik a számát. Vegye figyelembe, hogy üres karakterláncok és a hibák kezelik az értékeket, így azok nem járul hozzá a "nem hiányzó száma."

* **Quantiles** (időközönként 0.1-es frissítés, 1, 5, 25, 50, 75, 95, 99 és 99,9 %-os): az egyes ki osztóérték biztosít az adatok eloszlása megismerje a közelítő-értékeket. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Mean**: az oszlop a számtani közepét. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Szórás**: az oszlop értékét. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Eltérés**: az oszlop varianciáját. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Eltéréseket**: az oszlop az eltéréseket. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

* **Értékek**: az értékek az oszlop. Üres bejegyzések irreleváns típusú funkciók jelennek meg.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Speciális előfeldolgozása

A kísérletek konfigurálásakor a speciális beállítás engedélyezésével `Preprocess`. Ennek során tehát azt jelenti, hogy az adatok előfeldolgozása és featurization lépések végrehajtása automatikusan történik.

|Előfeldolgozási&nbsp;lépések| Leírás |
| ------------- | ------------- |
|Nagy számosságú dobja el vagy nem eltérés szolgáltatások|Dobja el ezeket a képzés és érvényesítési csoportok, többek között a szolgáltatások az összes érték hiányzik, ugyanazt az értéket az összes sor vagy a rendkívül nagy számosságú (például kivonatokat, azonosítók vagy GUID-azonosítói).|
|Hiányzó imputálására|Numerikus funkciók imputálására az oszlopban szereplő értékek átlaga.<br/><br/>Kategorikus funkciók imputálására leggyakoribb értékkel.|
|További funkciók létrehozása|A dátum és idő jellemzői: Év, hónap, nap, hét napja, nap, év, negyedév, az év, óra, perc, másodperc.<br/><br/>Az SMS jellemzők: Kifejezés gyakorisága unigrams, bi-gramokat és tri-karakter-gramokat alapján.|
|Átalakítás és kódolása |Kevés egyedi értékkel rendelkező numerikus funkciók kategorikus funkciók be lesznek átalakítva.<br/><br/>Egy gyakori kódolási végez a kategorikus; alacsony számossága a nagy számosságú egy gyakran használt adatok rétegére kivonatoló kódolást.|
|Szóbeágyazásokból|Szöveg featurizer, amely szöveges jogkivonatokat vektorok alakítja át mondat vektorok előre betanított modell használatával. Minden egyes szó beágyazási vektor dokumentumban összesített értéket jelenít meg együtt a dokumentum funkció vektor előállításához.|
|Cél kódolásokat|Kategorikus funkciók a maps átlagolt célérték regressziós problémákat, és az egyes osztályok osztályozási problémák osztály mekkora valószínűséggel az egyes kategóriák. Gyakoriság-alapú súlyozási és k lépésből keresztellenőrzés feletti méretezés a leképezési és a ritka adatkategóriák okozta zaj csökkentésére van alkalmazva.|
|Cél kódování textu|Szövegbevitel tulajdonságcsomag-az-szavakat halmozott lineáris modellt használunk a valószínűsége annak, minden egyes osztály létrehozása.|
|Súly bizonyítékok (WoE)|Kiszámítja a WoE érték, a céloszlop kategorikus oszlopok összekapcsolását. A napló a kategória legjobbjának vs ki az osztály a valószínűségek aránya, akkor a program. Ez a lépés osztály egy céloszlop oszlop megjeleníti, és explicit módon a hiányzó értékeket, és a kiugró kezelés imputálására nem szükséges.|
|Fürt távolság|Betanítja összes numerikus oszlopot a k-közép csoportosító modell.  Kimenetek k új funkciókat is, minden egyes fürt az középpontját minta távolságát a tartalmazó fürtönként egy új numerikus funkció.|

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatásához és az eredmények megtekintése

A kísérlet futtatásához kattintson a Start gombra. A kísérlet előkészítése folyamat néhány percet vesz igénybe.

### <a name="view-experiment-details"></a>Kísérlet részleteinek megtekintése

Miután megtörtént a kísérlet előkészítési fázisban, látni fogja a Futtatás részletes nézetű képernyő. Ez lehetővé teszi a létrehozott modellek teljes listáját. Alapértelmezés szerint a modellt, amely a legnagyobb pontszámmodell paraméterek alapján, a rendszer a lista tetején. A betanítási feladat próbálja ki további modelleket, mivel azok hozzáadódnak az iteráció listáját és a diagram. Használja az iteráció diagram eddig létrehozott modellek gyors összehasonlítása a metrikák beolvasásához.

Betanítási feladatok futása befejeződik, minden egyes folyamat esetében egy ideig is eltarthat.

![Futtassa a részleteket megjelenítő irányítópult](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Futtatás részletei nézet képzés

Részletezés bármely, a kimeneti modellek betanítása futtatási adatok, például a teljesítmény-mérőszámokat és a terjesztési diagramok megtekintéséhez. [További tudnivalók a diagramok](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Iteráció részletei](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="next-steps"></a>További lépések

* [További információ az automatikus gépi tanulási](concept-automated-ml.md) és az Azure Machine Learning alkalmazásával.
