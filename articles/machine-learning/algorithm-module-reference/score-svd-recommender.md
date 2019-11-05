---
title: 'Pontszám SVD ajánló: modul referenciája'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a score SVD ajánló modul a Azure Machine Learning szolgáltatásban az adathalmazokra vonatkozó javaslatok előrejelzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517916"
---
# <a name="score-svd-recommender"></a>Pontszám SVD ajánló

Ez a cikk azt ismerteti, hogyan használható a **score SVD ajánló** modul a Azure Machine learning Designerben (előzetes verzió). Ezzel a modullal előrejelzések hozhatók létre a SVD (Single Value dekompozíció) algoritmus alapján betanított javaslati modellel.

A SVD ajánló két különböző típusú előrejelzést tud előállítani:

- [Egy adott felhasználó és tétel minősítésének előrejelzése](#predict-ratings)

- [Elemek ajánlása egy adott felhasználónak](#recommend)

A második típusú előrejelzések létrehozásakor akár *üzemi módban* , akár *kiértékelési módban*is működhet.

- Az **éles üzemmód** minden felhasználót vagy elemet figyelembe vesz, és általában egy webszolgáltatásban használatos.

    Új felhasználók számára is létrehozhat pontszámokat, nem csak a betanítás során látott felhasználókat. További információkért tekintse meg [ezt a szakaszt](#technical-notes). 

- A **kiértékelési mód** a felhasználók vagy a kiértékelhető elemek csökkentett készletén működik, és általában a folyamat során használatos.

A SVD ajánló algoritmussal kapcsolatos további információkért tekintse meg a következő tanulmányt: [Matrix faktorizációs Techniques for ajánló Systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>A score SVD ajánló konfigurálása

Ez a modul különböző típusú ajánlásokat támogat, amelyek mindegyike eltérő követelményeket támaszt. Kattintson a kívánt adattípusra mutató hivatkozásra, valamint a létrehozni kívánt javaslat típusára.

+ [Értékelések előrejelzése](#predict-ratings)
+ [Elemek ajánlása](#recommend)

###  <a name="predict-ratings"></a>Értékelések előrejelzése

A minősítések előrejelzése során a modell kiszámítja, hogy egy adott felhasználó hogyan reagál egy adott elemre a betanítási adatok miatt. Ezért a pontozás bemeneti adatának meg kell adnia egy felhasználót és a díjszabást is.

1. Adjon hozzá egy betanított javaslatot a folyamathoz, és kapcsolódjon a **betanított SVD-ajánlóhoz**.  A modellt a [Train SVD ajánló](train-SVD-recommender.md)használatával kell létrehoznia.

2. **Ajánlói előrejelző típus**: válassza a **minősítési előrejelzés**lehetőséget. Nincs szükség további paraméterekre.

3. Adja hozzá azokat az adatokat, amelyekhez előrejelzéseket kíván készíteni, majd a **pontszámhoz kapcsolódjon az adatkészlethez**.

    A minősítések előrejelzéséhez a bemeneti adatkészletnek tartalmaznia kell a felhasználó-elem párokat.

    Az adatkészlet az első és a második oszlopban lévő felhasználói elem párokra vonatkozóan opcionális harmadik oszlopot is tartalmazhat, de a harmadik oszlop figyelmen kívül lesz hagyva az előrejelzés során.

4. A folyamat futtatása.

### <a name="results-for-rating-predictions"></a>A minősítési előrejelzések eredményei 

A kimeneti adatkészlet három oszlopot tartalmaz, amelyek tartalmazzák a felhasználót, az elemeket, valamint az egyes bemeneti felhasználók és elemek előre jelzett minősítését.

###  <a name="recommend"></a>Ajánlom 

A felhasználókra vonatkozó elemek ajánlásához adja meg a felhasználók és az elemek listáját bemenetként. Ezekből az adatokból a modell a meglévő elemek és felhasználók ismereteit használja az egyes felhasználókra vonatkozó, valószínűleg fellebbezéssel rendelkező elemek listájának létrehozásához. Testreszabhatja a visszaadott javaslatok számát, és beállíthat egy küszöbértéket a javaslat létrehozásához szükséges korábbi javaslatok számára.

1. Adjon hozzá egy betanított javaslatot a folyamathoz, és kapcsolódjon a **betanított SVD-ajánlóhoz**.  A modellt a [Train SVD ajánló](train-svd-recommender.md)használatával kell létrehoznia.

2. Ha a felhasználók egy adott listájához szeretne elemeket ajánlani, állítsa az **Ajánlói előrejelzési típust** **elemre**.

3. **Javasolt elem kiválasztása**: jelezze, hogy éles környezetben vagy modell kiértékeléséhez használja-e a pontozási modult:

    - **Minden elemből**: akkor válassza ezt a lehetőséget, ha egy webszolgáltatásban vagy éles környezetben használni kívánt folyamatot állít be.  Ez a beállítás engedélyezi az **éles üzemmódot**, és a modul javaslatokat tesz a betanítás során látható összes elemről.

    - **A névleges elemek közül (a modell kiértékeléséhez)** : válassza ezt a lehetőséget, ha modellt fejleszt vagy tesztel. Ez a beállítás lehetővé teszi a **kiértékelési módot**, és a modul csak a megadott bemeneti adatkészletben lévő elemekről tesz javaslatokat.
    
    - Nem **értékelt elemektől (új elemek felhasználók számára történő javaslata)** : válassza ezt a lehetőséget, és a modul csak a betanítási adatkészlet azon elemeire vonatkozó javaslatokat tesz, amelyek nem lettek értékelve. 

4. Adja hozzá azt az adatkészletet, amelyhez előrejelzéseket kíván készíteni, majd a pontszámhoz kapcsolódjon az **adatkészlethez**.

    - **Minden elem**esetében a bemeneti adatkészletnek egy oszlopból kell állnia, amely tartalmazza azon felhasználók azonosítóit, akik számára javaslatokat kíván tenni.

        Az adatkészlet tartalmazhat további két oszlopot az elem-azonosítók és a minősítések között, de ezt a két oszlopot figyelmen kívül hagyja a rendszer. 

    - A **névleges elemektől (a modell kiértékeléséhez)** a bemeneti adatkészletnek a **felhasználó-elem párokból**kell állnia. Az első oszlopnak tartalmaznia kell a **felhasználói** azonosítót. A második oszlopnak tartalmaznia kell a megfelelő **elemek** azonosítóit.

        Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.

    - A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** a bemeneti adatkészletnek **felhasználói elemből álló párokból**kell állnia. Az első oszlopnak tartalmaznia kell a **felhasználói** azonosítót. A második oszlopnak tartalmaznia kell a megfelelő **elemek** azonosítóit.

        Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.

5. A **felhasználó számára ajánlott elemek maximális száma**: írja be az egyes felhasználók számára visszaadni kívánt elemek számát. Alapértelmezés szerint öt elem ajánlott.

6. **Az ajánlási készlet minimális mérete felhasználónként**: adjon meg egy értéket, amely azt jelzi, hogy hány előzetes javaslat szükséges.  Alapértelmezés szerint ez a paraméter 2 értékre van állítva, ami azt jelenti, hogy az elemnek legalább két másik felhasználó által ajánlottnak kell lennie.

    Ezt a beállítást csak akkor kell használni, ha próbaverziós módban van. A beállítás nem érhető el, ha az **összes elemből** vagy **a nem értékelt elemek közül a lehetőséget választja (új elemeket javasol a felhasználóknak)** .

7.  A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** használja a **betanítási adatok**nevű harmadik bemeneti portot, hogy eltávolítsa azokat az elemeket, amelyek már a jóslat eredményeiből lettek kiértékelve.

    A szűrő alkalmazásához az eredeti betanítási adatkészletet a bemeneti porthoz kell kapcsolni.

8. A folyamat futtatása.

### <a name="results-of-item-recommendation"></a>Az elemek javaslatának eredményei

A **pontszám SVD** által visszaadott pontozásos adatkészlet felsorolja az egyes felhasználók ajánlott elemeit.

- Az első oszlop tartalmazza a felhasználói azonosítókat.
- Számos további oszlop jön létre, attól függően, hogy milyen értékre van beállítva a **felhasználó számára ajánlott elemek maximális száma**. Minden oszlop egy javasolt (azonosítóval rendelkező) tételt tartalmaz. Az ajánlásokat a felhasználó-elem affinitása alapján rendezi a rendszer, a legmagasabb affinitású elemmel pedig az **1. tételt**.

> [!WARNING]
> Ezt a pontszámmal rendelkező adatkészletet nem lehet kiértékelni a [kiértékelést ajánló](evaluate-recommender.md) modul használatával.


##  <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz válaszokat tartalmaz néhány gyakori kérdésre, amelyekkel az ajánló használatával hozhat létre előrejelzéseket.  

###  <a name="production-use-of-the-svd-recommender"></a>A SVD ajánló üzemi használata

Ha rendelkezik egy, a SVD ajánlóval rendelkező folyamattal, és a modellt éles környezetbe helyezi át, vegye figyelembe a legfontosabb különbségeket, amikor az ajánlót kiértékelési módban és éles módban használja:

- A kiértékeléshez a definíció szerint olyan előrejelzések szükségesek, amelyeket a rendszer egy tesztelési készletben ellenőrizheti a *terepi igazságban* . Ezért az ajánló kiértékelése során csak azokat az elemeket kell megjósolni, amelyek a tesztelési készletben lettek értékelve. Ez szükségszerűen korlátozza az előre jelzett lehetséges értékeket.

    A modell működővé tenni azonban általában úgy módosítja az előrejelzési módot, hogy az összes lehetséges elem alapján javaslatokat tegyen, hogy a lehető legjobb előrejelzések legyenek. Az előrejelzések többsége nem rendelkezik a megfelelő indokokkal, így az ajánlás pontossága nem ellenőrizhető ugyanúgy, mint a folyamat során.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
