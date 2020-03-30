---
title: N-Gram-jellemzők kinyerése a szövegmodul hivatkozásából
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az N-Gram kinyerése modult az Azure Machine Learning szöveges adatok featurize.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477612"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>N-Gram-jellemzők kinyerése a szövegmodul hivatkozásából

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió). Az N-Gram-szolgáltatások kinyerése szövegből modulsegítségével strukturálatlan szöveges adatokat *ábrázol.* 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Az N-Gram-jellemzők kinyerése a szövegmodulból

A modul a következő forgatókönyveket támogatja az n-gram szótár használatával:

* [Hozzon létre egy új n-gram szótárat](#create-a-new-n-gram-dictionary) egy szabad szövegoszlopból.

* A szabad szövegoszlop hoz egy [meglévő szövegjellemzőkészletet.](#use-an-existing-n-gram-dictionary)

* N-grammot használó [modell pontozása vagy közzététele.](#score-or-publish-a-model-that-uses-n-grams)

### <a name="create-a-new-n-gram-dictionary"></a>Új n-gram szótár létrehozása

1.  Adja hozzá az N-Gram-szolgáltatások kibontása szövegmodulból a folyamathoz, és csatlakoztassa a feldolgozni kívánt szöveget tartalmazó adatkészletet.

1.  **A Szöveg oszlopban** olyan karakterlánctípusú oszlopot választhat, amely a kibontani kívánt szöveget tartalmazza. Mivel az eredmények részletesek, egyszerre csak egy oszlopot dolgozhat fel.

1. Állítsa **a Szókincs módot** **létrehozásra,** jelezve, hogy új listát hoz létre az n-gram funkciókról. 

1. Állítsa be **az N-Gramm méretet,** hogy jelezze az n-gramm *maximális* méretét a kivonathoz és a tároláshoz. 

    Ha például 3-at ad meg, akkor egygrammos, bigram és trigramok jönnek létre.

1. **A Weighting funkció** határozza meg, hogyan lehet a dokumentumjellemző vektort felépíteni, és hogyan lehet kinyerni a szókincset a dokumentumokból.

    * **Bináris súly**: Bináris jelenléti értéket rendel a kivont n-grammokhoz. Az egyes n-gramok értéke 1, ha a dokumentumban is szerepel, és 0 egyébként.

    * **TF súly:** A kivont n-grammokhoz egy kifejezésgyakorisági (TF) pontszámot rendel. Az egyes n grammok értéke a dokumentumban lévő előfordulási gyakoriság.

    * **IDF súly:** Inverz dokumentumfrekvencia-(IDF) pontszám hozzárendelése a kivont n-grammokhoz. Az egyes n grammok értéke a corpus méretű napló és annak előfordulási gyakorisága osztva az egész korpuszban.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF súly:** Kifejezés gyakorisága/inverz dokumentumgyakoriság (TF/IDF) pontszámát rendeli hozzá a kivont n-grammhoz. Az egyes n grammok értéke a TF pontszám és az IDF pontszám szorzata.

1. Állítsa be **a Minimális szóhosszt** az n-gramm bármely *szavában* használható betűk minimális számára.

1. A **Szóhosszának maximális** használatával állíthatja be, hogy az n-grammban egyetlen *szóban* legfeljebb hány betű használható.

    Alapértelmezés szerint legfeljebb 25 karakter szónként vagy jogkivonatonként megengedett.

1. Használja **a Minimális n-gram dokumentum abszolút gyakoriságát** az n-gram szótárba való felvételéhez szükséges minimális előfordulások beállításához. 

    Ha például az alapértelmezett 5 értéket használja, minden n grammnak legalább ötször meg kell jelennie a corpusban ahhoz, hogy az n-gram szótárba kerüljön. 

1.  Állítsa be **a Maximális n-gram dokumentumarányt** az adott n grammot tartalmazó sorok maximális arányára a teljes korpusz sorainak számához képest.

    Például az 1 arány azt jelzi, hogy még akkor is, ha egy adott n-gram van jelen minden sorban, az n-gram hozzáadható az n-gram szótárhoz. Jellemzőbb, hogy egy szó, amely minden sorban előfordul, zajszónak minősül, és eltávolításra kerül. A tartományfüggő zajszavak kiszűréséhez próbálja meg csökkenteni ezt az arányt.

    > [!IMPORTANT]
    > Az egyes szavak előfordulási aránya nem egységes. Ez dokumentumonként változik. Ha például egy adott termékkel kapcsolatos vásárlói megjegyzéseket elemez, a termék neve nagyon magas gyakoriságú és egy zajszóhoz közeli lehet, de más környezetekben jelentős kifejezés.

1. Válassza ki az **n-gram jellemzővektorok normalizálása** lehetőséget a jellemzővektorok normalizálásához. Ha ez a beállítás engedélyezve van, minden n-gram jellemzővektort eloszt az L2 norma.

1. Küldje el a folyamatot.

### <a name="use-an-existing-n-gram-dictionary"></a>Meglévő n-gram szótár használata

1.  Adja hozzá az N-Gram-szolgáltatások kibontása szövegmodulból a folyamathoz, és csatlakoztassa **Dataset** a feldolgozni kívánt szöveget tartalmazó adatkészletet az adatkészletporthoz.

1.  **A Szöveg oszlopban** jelölje ki a ábrázolni kívánt szöveget tartalmazó szövegoszlopot. Alapértelmezés szerint a modul kijelöli a **karakterlánc típusoszlopait.** A legjobb eredmény érdekében egyszerre csak egy oszlopot dolgozzon fel.

1. Adja hozzá a korábban létrehozott n-gram szótárat tartalmazó mentett adatkészletet, és csatlakoztassa a **beviteli szókincsporthoz.** Az N-Gram-jellemzők kinyerése szövegből modul egy upstream példányának **eredmény szókincskimenetét** is csatlakoztathatja.

1. **Szókincs módban**válassza a **Csak olvasási** frissítés lehetőséget a legördülő listából.

   A **Csak olvasási** beállítás a bemeneti szókincs bemeneti korpuszát jelöli. Ahelyett, hogy az új szöveges adatkészletből (a bal oldali bemeneten) a kifejezésgyakoriságokat számolna be, a bemeneti szókincs n grammos súlyait a program a következőképpen alkalmazza.

   > [!TIP]
   > Akkor használja ezt a beállítást, ha szövegosztályozót pontoz.

1.  Az összes többi lehetőséget lásd az [előző](#create-a-new-n-gram-dictionary)szakasztulajdonság-leírásában.

1.  Küldje el a folyamatot.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>N-grammot használó modell pontozása vagy közzététele

1.  Másolja az **N-Gram-szolgáltatások kinyerése a szövegmodulból** a betanítási adatfolyamból a pontozási adatfolyamba.

1.  Csatlakoztassa az **eredményszókincs** kimenetét a betanítási adatfolyamból a pontozási adatfolyam **beviteli szókincséhez.**

1.  A pontozási munkafolyamatban módosítsa az N-Gram-szolgáltatások kinyerése szövegből modult, és állítsa a **Szókincs mód** paramétert **Csak olvasásra.** Minden mást hagysz ugyanúgy.

1.  A folyamat közzétételéhez mentse **az Eredményszókincset** adatkészletként.

1.  Csatlakoztassa a mentett adatkészletet az N-Gram-funkciók kinyerése szövegből modulhoz a pontozási grafikonon.

## <a name="results"></a>Results (Eredmények)

Az N-Gram-jellemzők kivonása a szövegből modul kétféle kimenetet hoz létre: 

* **Eredményadatkészlet**: Ez a kimenet az elemzett szöveg és a kivont n-grammok összegzése. Azok az oszlopok, amelyeket nem választott ki a **Szöveg oszlopban,** átkerülnek a kimenetre. Minden elemzendő szövegoszlophoz a modul a következő oszlopokat hozza létre:

  * **N-gram előfordulások mátrixa**: A modul az összes corpusban található minden n grammhoz létrehoz egy oszlopot, és minden oszlopban hozzáad egy pontszámot, amely jelzi az adott sor n-gramjának súlyát. 

* **Eredmény szókincs**: A szókincs tartalmazza a tényleges n-gram szótár, valamint a kifejezés frekvencia pontszámok, amelyek az elemzés részeként keletkeznek. Az adatkészletet mentheti, hogy más bemenetekkel újra felhasználhassa, vagy egy későbbi frissítéshez. Azt is újra a szókincs modellezés és pontozási.

### <a name="result-vocabulary"></a>Eredmény szókincs

A szókincs tartalmazza az n-gram szótára kifejezés gyakorisága pontszámok, amelyek az elemzés részeként keletkezik. A DF és az IDF pontszámok más beállításoktól függetlenül jönnek létre.

+ **Azonosító**: Minden egyes egyedi n grammhoz létrehozott azonosító.
+ **NGram**: Az n-gramm. A szóközök et vagy más szóelválasztókat az aláhúzás jelmondat váltja fel.
+ **DF**: Az eredeti korpuszna n grammjának gyakorisági pontszáma.
+ **IDF**: Az eredeti korpuszna n grammjának inverz dokumentumgyakorisági pontszáma.

Ezt az adatkészletet manuálisan is frissítheti, de hibákat okozhat. Példa:

* Hiba lép fel, ha a modul ismétlődő sorokat talál ugyanazzal a kulccsal a beviteli szókincsben. Győződjön meg arról, hogy a szókincs egyetlen két sorában sem ugyanaz a szó.
* A szókincs-adatkészletek bemeneti sémájának pontosan egyeznie kell, beleértve az oszlopneveket és az oszloptípusokat is. 
* Az **Azonosító** oszlopnak és a **DF** oszlopnak egész típusúnak kell lennie. 
* Az **IDF** oszlopnak úszó típusúnak kell lennie.

> [!Note]
> Ne csatlakoztassa közvetlenül az adatkimenetet a Betanítási modell modulhoz. A szabad szöveges oszlopokat el kell távolítania, mielőtt betáplálnák őket a betanítási modellbe. Ellenkező esetben a szabad szöveges oszlopok kategorikus jellemzőkként lesznek kezelve.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md)
