---
title: 'PCA-alapú anomáliadetektálás: Modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a PCA-alapú anomáliadetektálási modult a fő összetevőelemzésen (PCA) alapuló anomáliadetektálási modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502983"
---
# <a name="pca-based-anomaly-detection"></a>PCA-alapú anomáliadetektálás

Ez a cikk ismerteti, hogyan használhatja a **PCA-alapú anomáliadetektálási** modul az Azure Machine Learning designer (előzetes verzió), a fő összetevő-elemzés (PCA) alapuló anomáliadetektálási modell létrehozásához.

Ez a modul segít egy modell létrehozásában olyan forgatókönyvekben, ahol könnyen beszerezhető adatokat egy osztályból, például érvényes tranzakciók, de nehéz elegendő mintát beszerezni a célzott anomáliákból. 

Például a csalárd tranzakciók észleléséhez nagyon gyakran nincs elég példa a csalásra, hogy betanítsa, de sok példa van a jó tranzakciókra. A **PCA-alapú anomáliadetektálási** modul a rendelkezésre álló funkciók elemzésével megoldja a problémát, hogy meghatározza, mi minősül "normál" osztálynak, és távolságmérő számokat alkalmaz az anomáliákat képviselő esetek azonosítására. Ez lehetővé teszi a modell betanítását a meglévő kiegyensúlyozatlan adatok használatával.

## <a name="more-about-principal-component-analysis"></a>További információk a fő összetevő-elemzésről

*A fő komponenselemzés*, amelyet gyakran PCA-ra rövidítve, a gépi tanulás egy bevált technikája. A PCA-t gyakran használják feltáró adatelemzésben, mert feltárja az adatok belső szerkezetét, és megmagyarázza az adatok varianciáját.

A PCA több változót tartalmazó adatok elemzésével működik. A változók közötti korrelációkat keresi, és meghatározza az eredmények közötti különbségeket legjobban figyelembe véve az értékek kombinációját. Ezek a kombinált jellemzőértékek a fő összetevőknek nevezett kompaktabb szolgáltatástér *létrehozására szolgálnak.*

Az anomáliadetektáláshoz minden új bemenetet elemez a rendszer, és az anomáliadetektálási algoritmus kiszámítja az eigenvectors vetületét egy normalizált rekonstrukciós hibával együtt. A normalizált hiba az anomáliapontszámként használatos. Minél nagyobb a hiba, annál rendellenesebb a példány.

A PCA működéséről és az anomáliadetektálás megvalósításáról az alábbi dokumentumokban talál további információt:

- [Randomizált algoritmus a fő összetevő elemzéshez.](https://arxiv.org/abs/0809.2274) Rokhlin, Szlan és Tygert

- [A szerkezet véletlenszerűséggel történő megtalálása: Valószínűségi algoritmusok a hozzávetőleges mátrixbomlások létrehozásához](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF letöltés). Halko, Martinsson és Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>A PCA anomáliadetektálás konfigurálása

1. Adja hozzá a **PCA-alapú anomáliadetektálási** modult a tervező ben lévő folyamathoz. Ez a modul az **Anomáliadetektálás** kategóriában található.

2. A **PCA-alapú anomáliadetektálásmodul** jobb paneljén kattintson a **Betanítási mód** beállításra, és adja meg, hogy a modellt egy adott paraméterkészlet használatával szeretné-e betanítani, vagy a legjobb paraméterek megkereséséhez használjon paraméteres söprést.

    - **Egyetlen paraméter**: Akkor válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és argumentumként megadott értékkészletet adjon meg.

3. **A PCA-ban használandó összetevők száma**: Adja meg a kimeneti szolgáltatások vagy összetevők számát.

    A PCA-t használó kísérlettervezés fontos része annak eldöntése, hogy hány összetevőt kell felvenni. Általános útmutatás, hogy ne tartalmazza a pca-összetevők száma, mint vannak változók. Ehelyett néhány kisebb számú összetevővel kell kezdenie, és növelnie kell azokat, amíg bizonyos feltételek nem teljesülnek.

    A legjobb eredmény akkor érhető el, ha a kimeneti összetevők száma **kisebb, mint** az adatkészletben elérhető szolgáltatásoszlopok száma.

4. Adja meg a véletlenszerű PCA-képzés során végrehajtandó túlmintavételezés mértékét. Anomáliadetektálási problémák esetén a kiegyensúlyozatlan adatok megnehezítik a szabványos PCA-technikák alkalmazását. A túlmintavételezés megadásával növelheti a célpéldányok számát.

    Ha 1 értéket ad meg, a végrehajtás nem történik meg. Ha 1-nél nagyobb értéket ad meg, a modell betanításához további minták jönnek létre.

    Két lehetőség van, attól függően, hogy használ-e paraméteres söprést vagy sem:

    - **A randomizált PCA túlmintavételezési paramétere**: Írjon be egyetlen egész számot, amely a kisebbségi osztály túlmintavételezésének arányát jelöli a normál osztályhoz viszonyítva. (Az **egyparaméteres** betanítási módszer használataesetén érhető el.)

    > [!NOTE]
    > A túlmintavételezett adatkészlet nem tekinthető meg. A túlmintavételezés PCA-val való használatáról a [Műszaki megjegyzések](#technical-notes)című témakörben talál további információt.

5. **A bemeneti funkció engedélyezése normalizálást jelent:** Ezzel a beállítással az összes bemeneti jellemző nullára való normalizálásához. A PCA esetében általában ajánlott a normalizálás vagy a nullára méretezés, mivel a PCA célja a változók közötti variancia maximalizálása.

     Alapértelmezés szerint ez a beállítás van kijelölve. Törölje a jelet a jelölőnégyzetből, ha az értékek már normalizáltak egy másik módszerrel vagy léptékkel.

6. Tagged betanítási adatkészlet és az egyik betanítási modul csatlakoztatása:

    - Ha a **Trainer mód létrehozása** beállítást **egy paraméterre**állítja be, használja a [Train Anomaly Detection Model module (Anomáliadetektálási modell betanítása)](train-anomaly-detection-model.md) modult.

7. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

Ha a betanítás befejeződött, mentheti a betanított modellt, vagy csatlakoztathatja a [Score Model](score-model.md) modulhoz az anomáliapontszámok előrejelzéséhez.

Az anomáliadetektálási modell eredményeinek kiértékelése további lépéseket igényel:

1. Annak ellenőrzése, hogy mindkét adatkészletben elérhető-e pontszámoszlop

    Ha megpróbál kiértékelni egy anomáliadetektálási modellt, és a "Nincs összehasonlítható pontozott adatkészletben nincs pontszámoszlop" hibaüzenet jelenik meg, az azt jelenti, hogy egy tipikus kiértékelési adatkészletet használ, amely egy címkeoszlopot tartalmaz, de nincs valószínűségi pontszám. Olyan adatkészletet kell választania, amely megfelel az anomáliadetektálási modellek sémakimenetének, amely tartalmazza **a pontozott címkék** és **a pontozott valószínűség oszlopot.**

2. Annak ellenőrzése, hogy a címkeoszlopok meg vannak-e jelölve

    Előfordulhat, hogy a címkeoszlophoz társított metaadatok törlődnek a folyamatdiagramon. Ha ez történik, amikor a [Modell kiértékelése](evaluate-model.md) modul segítségével összehasonlítja a két anomáliadetektálási modellek eredményeit, előfordulhat, hogy a "Nincs címke oszlop a pontozott adatkészletben", vagy "Nincs címke oszlop a pontozott adatkészlet összehasonlítani".

    Ezt a hibát úgy kerülheti el, hogy a Modell [kiértékelése](evaluate-model.md) modul elé adja a [Metaadatok szerkesztése](edit-metadata.md) modult. Az oszlopkijelölő segítségével válassza ki az osztályoszlopot, és a **Mezők** legördülő listában válassza a **Címke lehetőséget.**

3. A [Python-parancsfájl végrehajtása](execute-python-script.md) parancs segítségével a címkeoszlop-kategóriákat 1(pozitív, normál) és 0(negatív, rendellenes) értékre állíthatja.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technikai megjegyzések

Ez az algoritmus pca-t használ a normál osztályt tartalmazó szubtér közelítésére. A szubteret az adatkovariancia mátrix felső eigenértékeihez társított eigenvectors okán kell kalibrálni. Minden egyes új bemenetnél az anomáliadetektor először kiszámítja a vetületét az eigenvectors, majd kiszámítja a normalizált rekonstrukciós hiba. Ez a hiba az anomáliapontszám. Minél nagyobb a hiba, annál rendellenesebb a példány. A normál terület kiszámításának módjáról a Wikipedia: [Principal Component Analysis (Fő összetevő-elemzés)](https://wikipedia.org/wiki/Principal_component_analysis) című témakörben talál részleteket. 


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

A tervezőmodulokra jellemző hibák listáját [a tervezőhöz tartozó kivételek és hibakódok (előzetes verzió)](designer-error-codes.md) című témakörben tekintheti meg."