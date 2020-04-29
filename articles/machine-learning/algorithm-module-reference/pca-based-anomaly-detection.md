---
title: 'PCA-alapú anomáliák észlelése: modul leírása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a PCA-alapú anomáliák észlelési moduljának használatával hogyan hozhat létre egy anomália-észlelési modellt az elsődleges összetevők elemzése (PCA) alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502983"
---
# <a name="pca-based-anomaly-detection"></a>PCA-alapú rendellenesség-észlelés

Ez a cikk azt ismerteti, hogyan használható a **PCA-alapú anomália-észlelési** modul a Azure Machine learning Designerben (előzetes verzió), amely a fő összetevők elemzése (PEM) alapján anomália-észlelési modellt hoz létre.

Ez a modul segít olyan modellek létrehozásában, amelyekben könnyen beszerezhetők az egyik osztályból származó betanítási adatok, például az érvényes tranzakciók, de nehéz a megcélzó anomáliák megfelelő mintáinak beszerzése. 

Például a csalárd tranzakciók észleléséhez nagyon gyakran nincs elég példa arra, hogy a csalás a betanításhoz, de a jó tranzakciók számos példája van. A **PEM-alapú anomáliák észlelése** modul megoldja a problémát az elérhető szolgáltatások elemzésével, amely meghatározza, hogy mit jelent a "normál" osztály, és milyen távolsági metrikákat alkalmaz a rendellenességeket képviselő esetek azonosítására. Ez lehetővé teszi a modell betanítását meglévő, kiegyensúlyozatlan adatértékek használatával.

## <a name="more-about-principal-component-analysis"></a>További tudnivalók a fő összetevők elemzéséről

A *fő összetevő elemzése*, amely gyakran a PCA-ra van rövidítve, a gépi tanulásban kialakított eljárás. A PCA-t gyakran használják a felderítő adatelemzés során, mert az az adat belső struktúráját mutatja be, és ismerteti az adateltérést.

A PEM a több változót tartalmazó adatok elemzésével működik. A változók közötti korrelációkat keres, és meghatározza, hogy a legjobban milyen értékeket kell rögzíteni a kimenetben. Ezeknek az egyesített szolgáltatásoknak a használatával a rendszer a *fő összetevőknek*nevezett, kompakt szolgáltatási terület létrehozására szolgál.

A anomáliák észlelése esetén minden új bemenet elemzése megtörténik, és az anomáliák észlelési algoritmusa kiszámítja a eigenvectors kivetítését, valamint egy normalizált újraépítési hibát. A normalizált hiba az anomália pontszámként szolgál. Minél nagyobb a hiba, annál több rendellenes a példány.

A PEM működéséről és az anomáliák észlelésének megvalósításáról a következő dokumentumokban talál további információt:

- [Randomizált algoritmus az elsődleges összetevők elemzéséhez](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan és Tygert

- [Véletlenszerű struktúrák keresése: valószínűségi algoritmusok a mátrixok hozzávetőleges összetételének létrehozásához](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF letöltés). Halko, Martinsson és TROPP.

## <a name="how-to-configure-pca-anomaly-detection"></a>A PCA anomália észlelésének konfigurálása

1. Adja hozzá a **PCA-alapú anomália-észlelési** modult a folyamathoz a tervezőben. Ez a modul az **anomáliák észlelése** kategóriában található.

2. A **PCA-alapú anomália észlelési** moduljának jobb oldali paneljén kattintson a **betanítási mód** lehetőségre, és jelezze, hogy a modellt egy adott paraméterek használatával szeretné-e betanítani, vagy egy paraméteres leválasztással keresse meg a legjobb paramétereket.

    - **Egyetlen paraméter**: válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adjon meg egy adott értékeket argumentumként.

3. A **PCA-ben használandó összetevők száma**: adja meg, hogy hány kimeneti funkció vagy összetevő legyen kimenetben.

    Annak a döntése, hogy a rendszer hány összetevőt tartalmaz, a kísérletezés a PCA használatával történő tervezésének fontos részét képezi. Általános útmutató, hogy ne tartalmazzon azonos számú PCA-összetevőt, mivel vannak változók. Ehelyett kisebb számú összetevővel kell kezdődnie, és csak néhány feltétel teljesülése esetén növelheti őket.

    A legjobb eredmények akkor érhetők el, ha a kimeneti összetevők száma **kevesebb, mint** az adatkészletben elérhető funkciók oszlopainak száma.

4. A véletlenszerű PCA-képzés során elvégzendő túlmintavételezési mennyiség meghatározása. Az anomáliák észlelésével kapcsolatos problémák esetén a kiegyensúlyozatlan adatelemzések megnehezítik a szabványos PEM-technikák alkalmazását. Bizonyos mennyiségű túlmintavételezés megadásával növelheti a célként megadott példányok számát.

    Ha az 1 érték van megadva, a rendszer nem végez túlmintavételezést. Ha 1-nél nagyobb értéket ad meg, a modell betanításakor további minták jönnek létre.

    Két lehetőség közül választhat, attól függően, hogy a paramétert használja-e vagy sem:

    - **Túlmintavételezési paraméter a randomizált PCA esetében**: adjon meg egy egész számot, amely a kisebbségi osztály a normál osztályban való túlmintavételezésének arányát jelöli. (Az **egyetlen paraméteres** betanítási módszer használata esetén érhető el.)

    > [!NOTE]
    > A túlmintavételezéses adatkészletet nem lehet megtekinteni. További információ arról, hogyan használják a túlmintavételezést a PCA használatával: [technikai megjegyzések](#technical-notes).

5. A **bemeneti funkció engedélyezése a normalizálás lehetővé tétele**: Ha ezt a beállítást választja, az összes bemeneti funkció egy nulla középértékre normalizálható. A PEM esetében általában a normalizálás vagy a méretezés nullára javasolt, mivel a PCA célja, hogy maximalizálja a változók közötti eltérést.

     Alapértelmezés szerint ez a beállítás van kijelölve. Törölje ezt a beállítást, ha az értékek más módszer vagy méretezés használatával már normalizálva vannak.

6. Címkézett betanítási adatkészlet és az egyik betanítási modul összekötése:

    - Ha az **oktatói mód létrehozása** beállítást **egyetlen paraméterre**állítja be, használja a [Train rendellenesség-észlelési modell](train-anomaly-detection-model.md) modult.

7. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

Ha betanítást végez, mentheti a betanított modellt, vagy a [pontszám modell](score-model.md) modulhoz kapcsolódva megjósolhatja a anomália pontszámait.

Egy anomália-észlelési modell eredményeinek kiértékelése néhány további lépést is igényel:

1. Győződjön meg arról, hogy a pontszám oszlop mindkét adatkészletben elérhető

    Ha egy anomália-észlelési modellt próbál kiértékelni, és a következő hibaüzenetet kapja: "nincs pontszám oszlop az összehasonlított adatkészletben", az azt jelenti, hogy egy olyan tipikus értékelési adatkészletet használ, amely tartalmaz egy címke oszlopot, de nincs valószínűségi pontszám. Ki kell választania egy olyan adatkészletet, amely megfelel az anomáliák észlelési modelljeinek, beleértve a **pontszámmal** ellátható címkéket és a **pontozásos valószínűség** oszlopot.

2. Ügyeljen rá, hogy a címke oszlopai legyenek megjelölve

    Előfordul, hogy a Label oszlophoz társított metaadatokat a folyamat gráfja eltávolítja. Ha ez történik, a [modell kiértékelése](evaluate-model.md) modul használatával összehasonlíthatja a két anomália-észlelési modell eredményét, előfordulhat, hogy a következő hibaüzenet jelenik meg: "nincs felirat oszlop a pontozásos adatkészletben", vagy "nincs felirat oszlop az összehasonlítható adatkészletben".

    A hiba elkerüléséhez adja hozzá a [metaadatok szerkesztése](edit-metadata.md) modult a [modell kiértékelése](evaluate-model.md) modulhoz. Az oszlop kiválasztásával válassza ki az osztály oszlopot, és a **mezők** legördülő listában válassza a **címke**lehetőséget.

3. Használja a [Python-szkript végrehajtása](execute-python-script.md) lehetőséget a Label oszlop kategóriáinak 1 (pozitív, normál) és 0 (negatív, rendellenes) értékre való beállításához.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technikai megjegyzések

Ez az algoritmus PEM használatával közelíti meg a normál osztályt tartalmazó alterületet. Az alterületet az adatszórási mátrix legfelső eigenvalues társított eigenvectors öleli fel. Minden új bemenetnél az anomália detektor először kiszámítja a eigenvectors való leképezését, majd kiszámítja a normalizált újraépítési hibát. Ez a hiba a rendellenesség pontszáma. Minél nagyobb a hiba, annál rendellenesebb a példány. A normál terület kiszámításának részletes ismertetését a wikipedia: [fő összetevő elemzése](https://wikipedia.org/wiki/Principal_component_analysis) című témakörben tekintheti meg. 


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A Designer modulokra vonatkozó hibák listáját [a tervező (előzetes verzió) kivételei és hibakódai](designer-error-codes.md) részben tekintheti meg.