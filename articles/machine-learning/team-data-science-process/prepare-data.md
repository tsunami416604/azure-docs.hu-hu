---
title: Az ML Studio adatainak előkészítése (klasszikus) - Csapatadat-elemzési folyamat
description: Előfeldolgozás és tiszta adatok előkészítése hatékonyan használható gépi tanuláshoz.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720044"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Az adatok bővített gépi tanulásra való előkészítésének feladatai
Az előfeldolgozási és tisztítási adatok fontos feladatok, amelyeket el kell végezni, mielőtt egy adatkészlet et modellbetanításhoz használhatna. A nyers adatok gyakran zajosak és megbízhatatlanok, és előfordulhat, hogy hiányoznak az értékek. Az ilyen adatok modellezésre való felhasználása félrevezető eredményeket hozhat. Ezek a feladatok a Team Data Science Process (TDSP) részét képezik, és általában egy szükséges előfeldolgozás felderítéséhez és megtervezéséhez használt adatkészlet kezdeti feltárását követik. A TDSP-folyamattal kapcsolatos részletesebb útmutatásért tekintse meg a [Csapatadat-elemzési folyamat lépéseit.](overview.md)

Az előfeldolgozási és tisztítási feladatok, például az adatfeltárási feladat, számos környezetben elvégezhetők, például az SQL vagy a Hive vagy az Azure Machine Learning Studio (klasszikus), valamint különböző eszközökkel és nyelvekkel, például R vagy Python nal, attól függően, hogy hol vannak az adatok és hogyan van formázva. Mivel a TDSP iteratív jellegű, ezek a feladatok a folyamat munkafolyamatának különböző lépéseivel is elhelyezhetők.

Ez a cikk bemutatja a különböző adatfeldolgozási fogalmak és feladatok, amelyek az Azure Machine Learning Studio (klasszikus) adatok betöltése előtt vagy után ellátható feladatokat.

Az Azure Machine Learning Studio (klasszikus) adatfeltárási és előfeldolgozási feldolgozásának példáját az [előzetes feldolgozási videó](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) ban.

## <a name="why-pre-process-and-clean-data"></a>Miért érdemes előre feldolgozni és letisztítani az adatokat?
A valós adatok különböző forrásokból és folyamatokból származnak, és szabálytalanságokat vagy sérült adatokat tartalmazhatnak, amelyek veszélyeztetik az adatkészlet minőségét. A felmerülő tipikus adatminőségi problémák a következők:

* **Hiányos**: Az adatokból hiányoznak az attribútumok, vagy hiányzó értékeket tartalmaznak.
* **Zajos**: Az adatok hibás rekordokat vagy kiugró értékeket tartalmaznak.
* **Inkonzisztens**: Az adatok ütköző rekordokat vagy eltéréseket tartalmaznak.

A minőségi adatok a minőség-prediktív modellek előfeltételei. A "szemét, a szemét kitakarítása" és az adatminőség javítása, és ezáltal a modellteljesítmény ének elkerülése érdekében elengedhetetlen az adatállapot-képernyő korai felismerése és a megfelelő adatfeldolgozási és tisztítási lépések eldöntése.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Melyek a tipikus adatállapot-képernyők, amelyek et alkalmaznak?
Az adatok általános minőségét az adatok ellenőrzésével ellenőrizhetjük:

* A **rekordok**száma .
* Az **attribútumok** (vagy **jellemzők)** száma .
* Az attribútum **adattípusai** (névleges, ordinális vagy folyamatos).
* A **hiányzó értékek**száma .
* **Jól formált** adatok.
  * Ha az adatok TSV-ben vagy CSV-ben vannak, ellenőrizze, hogy az oszlopelválasztók és a vonalelválasztók mindig helyesen választják-e el az oszlopokat és a vonalakat.
  * Ha az adatok HTML vagy XML formátumúak, ellenőrizze, hogy az adatok jól vannak-e formázva a megfelelő szabványok alapján.
  * Elemzésre is szükség lehet annak érdekében, hogy strukturált információkat nyerjenek ki félig strukturált vagy strukturálatlan adatokból.
* **Inkonzisztens adatrekordok**. Ellenőrizze, hogy az értékek tartománya engedélyezett-e. Ha például az adatok diákátlagot (évfolyampont-átlagot) tartalmaznak, ellenőrizze, hogy a gpa a kijelölt tartományban van-e, mondja 0~4.

Ha adatokkal kapcsolatos problémákat talál, **feldolgozási lépésekre** van szükség, amelyek gyakran magukban foglalják a hiányzó értékek, az adatok normalizálását, a diszkretizálást, a szövegfeldolgozást az adatok igazítását befolyásoló beágyazott karakterek eltávolításához és/vagy cseréjéhez, a vegyes adattípusokat a közös mezőkben és másokat.

**Az Azure Machine Learning jól formázott táblázatos adatokat használ fel.**  Ha az adatok már táblázatos formában vannak, az adatok előfeldolgozása közvetlenül az Azure Machine Learning Studio (klasszikus) használatával végezhető el a Machine Learningben.  Ha az adatok nem táblázatos formában vannak, tegyük fel, hogy XML formátumban vannak, elemzésre lehet szükség az adatok táblázatos formává alakításához.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Melyek az adatok előzetes feldolgozásának főbb feladatai?
* **Adattisztítás:** Töltse ki a hiányzó értékeket, észlelje és távolítsa el a zajos adatokat és kiugró értékeket.
* **Adatátalakítás**: Az adatok normalizálása a méretek és a zaj csökkentése érdekében.
* **Adatcsökkentés**: Mintaadatrekordok vagy -attribútumok a könnyebb adatkezelés érdekében.
* **Adatelkedés:** A folyamatos attribútumok átalakítása kategorikus attribútumokká bizonyos gépi tanulási módszerek kel való egyszerű használat érdekében.
* **Szövegtisztítás:** távolítsa el azokat a beágyazott karaktereket, amelyek az adatok eltolódását okozhatják, például a tabulátorral elválasztott adatfájlbeágyazott tabulátorait, például beágyazott új sorokat, amelyek megdönthetik a rekordokat.

Az alábbi szakaszok részletesen ismertetik az adatfeldolgozási lépéseket.

## <a name="how-to-deal-with-missing-values"></a>Hogyan kezeljük a hiányzó értékeket?
A hiányzó értékek kezeléséhez a legjobb, ha először azonosítja a hiányzó értékek okát a probléma jobb kezelése érdekében. A tipikus hiányzó értékkezelési módszerek a következők:

* **Törlés**: Hiányzó értékekkel rendelkező rekordok eltávolítása
* **Dummy helyettesítés**: A hiányzó értékeket egy próbaértékre cseréljük: pl. *ismeretlen* a kategorikus, vagy 0 a numerikus értékek esetében.
* **Átlagos helyettesítés**: Ha a hiányzó adatok számszerűek, cserélje ki a hiányzó értékeket a középértékre.
* **Gyakori helyettesítés**: Ha a hiányzó adatok kategorikusak, cserélje ki a hiányzó értékeket a leggyakoribb elemre
* **Regresszióhelyettesítés**: Regressziós módszerrel helyettesítheti a hiányzó értékeket a regressziós értékekkel.  

## <a name="how-to-normalize-data"></a>Hogyan lehet normalizálni az adatokat?
Az adatok normalizálása átméretezi a numerikus értékeket egy megadott tartományra. Népszerű adatnormalizálási módszerek a következők:

* **Min-Max normalizálás:** Lineárisan átalakítja az adatokat egy tartományba, mondjuk 0 és 1 között, ahol a min érték 0-ra, a maximális érték pedig 1-re van méretezve.
* **Z-score Normalizálás**: Az adatok méretezése középérték és szórás alapján: az adatok és a középérték közötti különbség felosztása a szórással.
* **Decimális méretezés:** Az adatok méretezése az attribútumérték tizedespontjának mozgatásával.  

## <a name="how-to-discretize-data"></a>Hogyan lehet eldiscretize adatok?
Az adatok a folyamatos értékek névleges attribútumokká vagy intervallumokká alakításával diszkrétek. Ennek néhány módja a következő:

* **Egyenlő szélességű binning**: Egy attribútum összes lehetséges értékének tartományát azonos méretű N csoportokra osztja, és hozzárendeli az azonos méretű raktárhelyre hulló értékeket.
* **Egyenlő magasságú binning**: Oszd el az attribútum összes lehetséges értékének tartományát N csoportokra, amelyek mindegyike azonos számú példányt tartalmaz, majd rendelje hozzá a raktárhelyen eső értékeket a raktárhely számával.  

## <a name="how-to-reduce-data"></a>Hogyan lehet csökkenteni az adatokat?
Az adatkezelés megkönnyítése érdekében különböző módszerek kelnek az adatméret csökkentésére. Az adatok méretétől és a tartománytól függően a következő módszerek alkalmazhatók:

* **Rekordmintavétel:** Mintavétel az adatrekordok, és csak válassza ki a reprezentatív részhalmazt az adatokból.
* **Attribútummintavétel:** Az adatok közül csak a legfontosabb attribútumok egy részét válassza ki.  
* **Összesítés:** Ossza fel az adatokat csoportokra, és tárolja az egyes csoportok számait. Például egy étteremlánc napi bevételi száma idoben az elmúlt 20 évben az adatok méretének csökkentése érdekében a havi bevételhez számíthat.  

## <a name="how-to-clean-text-data"></a>Hogyan tisztítsa meg a szöveges adatokat?
**A táblázatos adatok szövegmezői** tartalmazhatnak olyan karaktereket, amelyek befolyásolják az oszlopok igazítását és/vagy a rekord határokat. Például a tabulátorral elválasztott fájl beágyazott lapjai oszlopeltolódást okoznak, a beágyazott új sorkarakterek pedig megszakítják a rekordsorokat. A szöveg írása vagy olvasása közbeni nem megfelelő szövegkódolás adatvesztéshez, olvashatatlan karakterek (például nullkarakterek) véletlen bevezetéséhez vezet, és hatással lehet a szövegelemzésre is. Gondos elemzésre és szerkesztésre lehet szükség a szövegmezők megfelelő igazításához és/vagy strukturált adatok kinyeréséhez strukturálatlan vagy félig strukturált szöveges adatokból.

**Az adatok feltárása** korai betekintést nyújt az adatokba. Ebben a lépésben számos adatprobléma fedezhető fel, és a megfelelő módszerek alkalmazhatók a problémák megoldására.  Fontos olyan kérdéseket feltenni, mint például, hogy mi a probléma forrása, és hogyan kerülhetett a probléma bevezetésére. Ez a folyamat segít eldönteni, hogy mely adatfeldolgozási lépéseket kell tenni a megoldásukhoz. A végső felhasználási esetek és a személyiségek azonosítása az adatfeldolgozási erőfeszítések rangsorolására is használható.

## <a name="references"></a>Referencia
> *Adatbányászat: Koncepciók és technikák*, Harmadik kiadás, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber és Jian Pei
> 
> 

