---
title: Előkészítési adat ML Studio (klasszikus) – csoportos adatelemzési folyamat
description: Adatok előkészítése, hogy hatékonyan használható a machine Learning előzetes feldolgozása és.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720044"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Az adatok bővített gépi tanulásra való előkészítésének feladatai
Az adatok előfeldolgozása és tisztítása olyan fontos feladatok, amelyeket el kell végezni ahhoz, hogy adatkészletet lehessen használni a modell betanításához. Nyers adatokat gyakran zajos és nem megbízható, és esetleg hiányzik a értékeket. Az ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ezek a feladatok tartozik, a csoportos adatelemzési folyamat (TDSP), és általában hajtsa végre egy kezdeti feltárásával egy adatkészlet felderítésére, és tervezze meg a szükséges előzetes feldolgozás segítségével. A TDSP folyamatával kapcsolatos részletesebb útmutatásért tekintse meg a [csoportos adatelemzési folyamat](overview.md)lépéseit.

Az előfeldolgozási és tisztítási feladatok, például az adatfeltárási feladat számos különböző környezetben, például az SQL-ben vagy a kaptárban, illetve a Azure Machine Learning Studio (klasszikus), valamint különböző eszközökkel és nyelvekkel (például R vagy Python) végezhetők el, attól függően, hogy hol találhatók az adatok tárolása és formázása. TDSP meghatározási jellegűek, mivel ezek a feladatok is igénybe vehet a munkafolyamatban a folyamat számos lépés helyén.

Ez a cikk különféle adatfeldolgozási fogalmakat és feladatokat mutat be, amelyek az adatoknak a Azure Machine Learning Studioba (klasszikus) történő betöltése előtt vagy után hajthatók végre.

Az Azure Machine Learning Studio (klasszikus) szolgáltatásban végzett adatfeltárásra és előfeldolgozásra például az [adatfeldolgozást előkészítő](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videóban olvashat.

## <a name="why-pre-process-and-clean-data"></a>Miért előzetes feldolgozása és az adatok?
Különböző forrásokból származó valós adatok megtörtént, és folyamatokat, és tartalmazhat szabálytalanságok vagy sérült adatokat, hogy az adatkészlet. A tipikus adatfolyamat minőségi problémák merülnek fel a következők:

* **Hiányos**: az adatok nem tartalmaznak attribútumokat, vagy hiányoznak a hiányzó értékek.
* **Zajos**: az adatok hibás rekordokat vagy kiugró adatokat tartalmaznak.
* **Inkonzisztens**: az adatok ütköző rekordokat vagy eltéréseket tartalmaznak.

Minőségi data quality prediktív modelleket előfeltétele. "Szemétgyűjtési szemétgyűjtési ki a" elkerülése és adatok minőség javítása, és ezért a teljesítmény modellezheti, amihez elengedhetetlen elvégzésére adat problémák korai azonosításához, és döntse el, a megfelelő adatokat feldolgozó és tisztítási lépéseket adatok állapota képernyő.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Mik azok az egyes alkalmazott tipikus adatfolyamat állapotát képernyők?
A Microsoft ellenőrizheti, hogy az adatok általános minőségének ellenőrzésével:

* A **rekordok**száma.
* Az **attribútumok** (vagy **szolgáltatások**) száma.
* Az attribútum **adattípusai** (névleges, sorszám vagy folytonos).
* A **hiányzó értékek**száma.
* **Jól formázott** adategységek.
  * Ha az adatok TSV- vagy CSV, ellenőrizze, hogy az oszlop elválasztók és a sor elválasztók mindig megfelelően külön oszlopok és sorok.
  * Ha az adatok HTML vagy XML formátumú, ellenőrizze-e az adatok jól formázott alapján a megfelelő szabványokon.
  * Elemzés is szükség lehet annak érdekében, hogy a strukturált adatok kinyerését részben strukturált vagy strukturálatlan adatokon.
* **Inkonzisztens adatrekordok**. Ellenőrizze, hogy értékek engedélyezettek. Például, ha az adattanulói GPA (a minőségi pont átlaga) tartalmaz, ellenőrizze, hogy a GPA a kijelölt tartományban van-e, azaz 0 ~ 4.

Ha az adatokkal kapcsolatos problémákat tapasztal, a **feldolgozási lépések** szükségesek, ami gyakran magában foglalja a hiányzó értékek törlését, az adatnormalizálás, a diszkretizálási, a szöveg feldolgozását, valamint a beágyazott karakterek eltávolítását és/vagy cseréjét, amelyek hatással lehetnek az adatok igazítására, a közös mezőkben szereplő vegyes adattípusokra és mások

A **Azure Machine learning jól formázott táblázatos adatokat**használ.  Ha az adatokat már táblázatos formában végzik, az adatfeldolgozást közvetlenül a Machine Learning Azure Machine Learning Studio (klasszikus) használatával is elvégezheti.  Ha adatok nem táblázatos formában, például azt az XML-elemzés lehet szükség annak érdekében, hogy az adatok átalakítása táblázatos formában.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Mik a főbb előtti adatfeldolgozási feladatok?
* **Adattisztítás**: adja meg a hiányzó értékeket, azonosítsa és távolítsa el a zajos adatokat és a kiugró adatokat.
* **Adatátalakítás**: a méretek és a zaj csökkentése érdekében normalizálja az adatmennyiséget.
* **Adatmennyiség-csökkentés**: adatrekordok vagy attribútumok a könnyebb adatkezelés érdekében.
* **Adatdiszkretizálási**: folyamatos attribútumok átalakítása kategorikus attribútumokra a könnyű használat érdekében bizonyos gépi tanulási módszerekkel.
* **Szöveg tisztítása**: távolítsa el az adatok helytelen igazítását okozó beágyazott karaktereket, például a tabulátorral tagolt adatfájlban lévő beágyazott lapokat, a beágyazott új sorokat, amelyek például a rekordokat bontják fel.

Az alábbi szakaszokban részletesen adatok feldolgozási lépések közül.

## <a name="how-to-deal-with-missing-values"></a>Hogyan foglalkozik a hiányzó értékeket?
Hiányzó kezelésére, érdemes először a probléma meghatározásához a hiányzó értékek is jobb kezelni az az oka. Tipikus hiányzó érték kezelése módszerek a következők:

* **Törlés**: hiányzó értékekkel rendelkező rekordok eltávolítása
* **Próbabábu-helyettesítés**: hiányzó értékek kicserélése egy dummy értékkel: például *ismeretlen* a kategorikus vagy a 0 értékre a numerikus értékeknél.
* **Mean helyettesítés**: Ha a hiányzó adatok numerikusak, cserélje le a hiányzó értékeket az átlag értékre.
* **Gyakori helyettesítés**: Ha a hiányzó adatok kategorikusak, cserélje le a hiányzó értékeket a leggyakoribb elemre.
* **Regressziós helyettesítés**: használjon regressziós metódust a hiányzó értékek romlott értékekkel való lecseréléséhez.  

## <a name="how-to-normalize-data"></a>Hogyan lehet adatokat normalizálása?
Az adatok normalizálása átméretezi a numerikus értékeket egy megadott tartományba. Népszerű normalizálási módszerek az alábbiak:

* **Min-max normalizálás**: lineárisan alakítsa át az értékeket egy tartományba, azaz 0 és 1 közötti értéket, ahol a minimális érték 0, a maximális érték pedig 1.
* **Z – pontszám normalizálása**: az adatméretezés a középérték és a szórás alapján: az adatmennyiség és a szórás közötti különbség felosztása.
* **Decimális skálázás**: méretezheti az adatmennyiséget az attribútumérték decimális pontjának áthelyezésével.  

## <a name="how-to-discretize-data"></a>Hogyan lehet adatokat diszkretizálásához?
Adatok is diszkretizálható folyamatos érték átalakítása névleges attribútumok vagy intervallumok szerint. Néhány módszer az ennek a következők:

* **Egyenlő szélességű dobozolási**: az attribútumok összes lehetséges értékének tartományát azonos méretű N csoportba sorolja, és hozzárendeli azokat az értékeket, amelyek a raktárhely számával ellátott raktárhelyen jelennek meg.
* **Egyenlő magasságú dobozolási**: az attribútumok összes lehetséges értékének tartományát N csoportokba osztja, amelyek mindegyike azonos számú példányt tartalmaz, majd hozzárendeli azokat az értékeket, amelyek az adott raktárhely számával ellátott raktárhelyre esnek.  

## <a name="how-to-reduce-data"></a>Hogyan csökkenthető az adatok?
A könnyebb adatkezelési adatok méretének csökkentése érdekében különböző módszer áll rendelkezésre. Adatok mérete és a tartományhoz a következő módszerek lehet alkalmazni:

* **Mintavétel rögzítése**: kóstolja meg az adatrekordokat, és csak a reprezentatív részhalmazt válassza ki az adatokból.
* **Attribútumok mintavételezése**: válassza ki a legfontosabb attribútumok egy részhalmazát az adatok közül.  
* **Összesítés**: az adatok csoportokba osztása és az egyes csoportok számának tárolása. Például az elmúlt 20 évben egy étteremlánc napi bevétel számú összesíthetők havi bevételhez az adatok méretének csökkentése érdekében.  

## <a name="how-to-clean-text-data"></a>Tiszta szöveges adatok hogyan?
**A táblázatos adatok szöveges mezői** tartalmazhatják az oszlopok igazítását és/vagy a rekordok határait befolyásoló karaktereket. A tabulátorral tagolt fájlok beágyazott lapjai például az oszlopok helytelen igazítását okozzák, a beágyazott új sortörési sorok pedig sortöréseket is tartalmazhatnak. A szöveges kódolás nem megfelelő, ha a szöveg írása vagy olvasása során az adatok elvesztését eredményezi, a nem olvasható karakterek (például nullák) véletlen bevezetését, és a szöveg elemzését is érintheti. Alapos elemzés és -szerkesztő lehet szükség annak érdekében, hogy a tiszta szöveges mezők megfelelő igazítás, illetve strukturált kivonatot adatok és a strukturálatlan és részben strukturált szöveges adatok.

Az **adatfeltárási** szolgáltatás korai áttekintést nyújt az adatvédelemről. Ebben a lépésben egy adat-problémák száma ne maradjon lefedetlen felelősségi lehet, és a probléma megoldására szolgáló kapcsolódó módszereit lehet alkalmazni.  Fontos például, hogy mi a probléma forrását, és hogyan a probléma előfordulhat, hogy vezettek be kérdéseket tehet fel. Ez a folyamat a megoldásához szükséges adatfeldolgozási lépések eldöntését is segíti. A végső használati esetek és a personák azonosításával is felhasználható az adatfeldolgozási tevékenység rangsorolása.

## <a name="references"></a>Referencia
> *Adatbányászat: fogalmak és technikák*, harmadik kiadás, Morgan Kaufmann, 2011, Jiawei Han, Michelin Kamber és Jian Pei
> 
> 

