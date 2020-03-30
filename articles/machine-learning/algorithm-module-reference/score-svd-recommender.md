---
title: 'Pontszám SVD ajánló: Modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Score SVD recommender modult az Azure Machine Learningben egy adatkészlet relevancia-előrejelzéseinek pontozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455978"
---
# <a name="score-svd-recommender"></a>SVD-ajánló pontozása

Ez a cikk ismerteti, hogyan használhatja a Score SVD ajánló modul az Azure Machine Learning designer (előzetes verzió). Ezzel a modullal előrejelzéseket hozhat létre egy betanított javaslatmodell használatával az egyértékű bomlás (SVD) algoritmus on alapul.

Az SVD-ajánló két különböző típusú előrejelzést tud létrehozni:

- [Adott felhasználó és elem minősítésének előrejelzése](#prediction-of-ratings)
- [Elemek ajánlása a felhasználónak](#recommendations-for-users)

A második típusú előrejelzés létrehozásakor az alábbi módok egyikében működhet:

- **A termelési mód** az összes felhasználót vagy cikket figyelembe veszi. Általában egy webszolgáltatásban használják.

  Az új felhasználók számára nem csak a képzés során látott felhasználók hozhatja létre a pontszámokat. További információt a [technikai megjegyzésekben](#technical-notes)talál. 

- **A kiértékelési mód** a kiértékelhető felhasználók vagy elemek csökkentett készletén működik. Általában a csővezeték-műveletek során használják.

Az SVD ajánlóalgoritmusával kapcsolatos további információkért tekintse meg a [Mátrix faktorizációs technikákat az ajánlórendszerekhez](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)című kutatási tanulmányban.

## <a name="how-to-configure-score-svd-recommender"></a>A Score SVD ajánló konfigurálása

Ez a modul kétféle előrejelzést támogat, amelyek mindegyike különböző követelményekkel. 

###  <a name="prediction-of-ratings"></a>A minősítések előrejelzése

Ha előre jelzi a minősítéseket, a modell kiszámítja, hogy a felhasználó hogyan fog reagálni egy adott elemre, a betanítási adatok alapján. A pontozási bemeneti adatoknak meg kell adniuk mind a felhasználót, mind a díjköteles elemet.

1. Adjon hozzá egy betanított javaslatmodellt a folyamathoz, és csatlakoztassa a **betanított SVD-ajánlóhoz.** Létre kell hoznia a modellt a [Train SVD recommender](train-SVD-recommender.md) modul használatával.

2. Az **Ajánló előrejelzési típusú,** válassza **Értékelés Előrejelzése**. Nincs szükség más paraméterekre.

3. Adja hozzá azokat az adatokat, amelyekhez előrejelzéseket szeretne készíteni, és csatlakoztassa őket **az adatkészlethez a pontozáshoz.**

   Ahhoz, hogy a modell előre jelezze a minősítéseket, a bemeneti adatkészletnek tartalmaznia kell a felhasználói elem párjait.

   Az adatkészlet az első és a második oszlopban tartalmazhatja a felhasználói elempár minősítéseinek opcionális harmadik oszlopát. De a harmadik oszlop figyelmen kívül lesz hagyva az előrejelzés során.

4. Küldje el a folyamatot.

### <a name="results-for-rating-predictions"></a>A minősítési előrejelzések eredményei 

A kimeneti adatkészlet három oszlopot tartalmaz: felhasználókat, elemeket és az egyes bemeneti felhasználók és elemek előre jelzett minősítését.

###  <a name="recommendations-for-users"></a>Javaslatok a felhasználók számára 

Ha elemeket szeretne javasolni a felhasználóknak, adja meg a felhasználók és elemek listáját bemenetként. Ezekből az adatokból a modell a meglévő elemekkel és felhasználókkal kapcsolatos ismereteit felhasználva hozza létre az egyes felhasználók számára valószínűleg vonzó elemek listáját. Testreszabhatja a visszaadott javaslatok számát. És beállíthatja a javaslat létrehozásához szükséges korábbi javaslatok számát.

1. Adjon hozzá egy betanított javaslatmodellt a folyamathoz, és csatlakoztassa a **betanított SVD-ajánlóhoz.**  Létre kell hoznia a modellt a [Train SVD recommender](train-svd-recommender.md) modul használatával.

2. Ha a felhasználók listájához szeretne elemeket ajánlani, állítsa **az Ajánló előrejelzési kedvesét** **a cikkajánlásra.**

3. Az **ajánlott cikk kiválasztása esetén**adja meg, hogy a pontozási modult használja-e éles környezetben vagy modellkiértékeléshez. Válasszon az alábbi értékek közül:

    - **Minden cikk:** Válassza ezt a lehetőséget, ha egy folyamatot állít be egy webszolgáltatásban vagy éles környezetben való használatra.  Ez a beállítás lehetővé teszi *a termelési módot.* A modul ajánlásokat tesz a képzés során látható összes elemből.

    - **A névleges elemek (a modell kiértékelése)**: Válassza ezt a lehetőséget, ha a fejlődő vagy tesztelési modell. Ez a beállítás lehetővé teszi *a kiértékelési módot.* A modul csak a bemeneti adatkészlet ben szereplő, minősített elemekből tesz javaslatokat.
    
    - **A Nem minősített elemek (javasoljon új elemeket a felhasználóknak)**: Válassza ezt a lehetőséget, ha azt szeretné, hogy a modul csak a betanítási adatkészlet azon elemeiből tegyen javaslatokat, amelyek nincsenek minősítve. 

4. Adja hozzá azt az adatkészletet, amelyhez előrejelzéseket szeretne készíteni, és csatlakoztassa **az adatkészlethez a pontszámhoz.**

    - Az **Összes elemből beállításhoz**a bemeneti adatkészletnek egy oszlopból kell állnia. Azon felhasználók azonosítóit tartalmazza, amelyekre vonatkozóan ajánlásokat kell tenni.

      Az adatkészlet tartalmazhat egy további két oszlopot az elemazonosítókból és minősítésekből, de ezt a két oszlopot figyelmen kívül hagyja. 

    - A **Számított elemek (modellkiértékeléshez)** esetében a bemeneti adatkészletnek felhasználói elempárokból kell állnia. Az első oszlopnak tartalmaznia kell a felhasználói azonosítót. A második oszlopnak tartalmaznia kell a megfelelő cikkazonosítókat.

      Az adatkészlet tartalmazhat egy harmadik oszlopot a felhasználói elemek minősítéseiből, de ezt az oszlopot figyelmen kívül hagyja.

    - A **Nem minősített elemek (új elemek et javasolanak a felhasználóknak)** esetében a bemeneti adatkészletnek felhasználói elempárokból kell állnia. Az első oszlopnak tartalmaznia kell a felhasználói azonosítót. A második oszlopnak tartalmaznia kell a megfelelő cikkazonosítókat.

     Az adatkészlet tartalmazhat egy harmadik oszlopot a felhasználói elemek minősítéseiből, de ezt az oszlopot figyelmen kívül hagyja.

5. **A felhasználónak ajánlandó elemek maximális száma**: Adja meg az egyes felhasználók számára visszaküldendő elemek számát. Alapértelmezés szerint a modul öt elemet javasol.

6. **A javaslatkészlet minimális mérete felhasználónként:** Adjon meg egy értéket, amely azt jelzi, hogy hány korábbi javaslatra van szükség. Alapértelmezés szerint ez a paraméter 2-re van állítva, ami azt jelenti, hogy legalább két másik felhasználó ajánlotta az elemet.

   Ezt a beállítást csak akkor használja, ha értékelési módban pontozást szeretne. Ez a beállítás nem érhető el, ha az **Összes elemtől** vagy a Nem minősített elemektől lehetőséget választja **(új elemeket javasol a felhasználóknak).**

7.  A **Nem minősített elemek (új elemek et javasolanak a felhasználóknak)** esetében használja a harmadik bemeneti portot, a Training **Data**nevet, és távolítsa el azokat az elemeket, amelyek már vannak minősítve az előrejelzési eredményekből.

    A szűrő alkalmazásához csatlakoztassa az eredeti betanítási adatkészletet a bemeneti porthoz.

8. Küldje el a folyamatot.

### <a name="results-of-item-recommendation"></a>A cikkre vonatkozó ajánlás eredményei

A Score SVD recommender által visszaadott pontozott adatkészlet felsorolja az egyes felhasználók számára ajánlott elemeket:

- Az első oszlop a felhasználói azonosítókat tartalmazza.
- A **felhasználónak ajánlandó elemek maximális száma**értéktől függően számos további oszlop jön létre. Minden oszlop tartalmaz egy ajánlott elemet (azonosító szerint). A javaslatok a felhasználói elemek affinitása szerint vannak rendezve. A legnagyobb affinitással rendelkező elem az **1.**

> [!WARNING]
> Ezt a pontozott adatkészletet nem tudja kiértékelni az [Ajánló kiértékelése](evaluate-recommender.md) modul használatával.


##  <a name="technical-notes"></a>Technikai megjegyzések

Ha rendelkezik egy folyamat az SVD-ajánló, és helyezze át a modellt éles környezetbe, vegye figyelembe, hogy vannak kulcsfontosságú különbségek az ajánló használata a kiértékelési módban, és éles módban használja.

Az értékelés definíció szerint olyan előrejelzéseket igényel, amelyek egy tesztkészletben ellenőrizhetők a *földi igazsággal* szemben. Az ajánló kiértékelésekor csak a tesztkészletben értékelt elemeket kell előre jeleznie. Ez korlátozza az előre jelzett lehetséges értékeket.

A modell üzembe astaként általában módosítja az előrejelzési módot, hogy az összes lehetséges elem alapján javaslatokat tegyen a legjobb előrejelzések lehetővé tétele érdekében. Sok ilyen jóslatok, nincs megfelelő földi igazság. Így a javaslat pontossága nem ellenőrizhető ugyanúgy, mint a csővezeték-műveletek során.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
