---
title: Adatok tisztítása és előkészítése az Azure Machine Learning |} A Microsoft Docs
description: Adatok előkészítése, gépi tanulási előzetes feldolgozása és.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 52457e19cede5d8d2b74d9c3d81ebf35e3ad06c4
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446560"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Az adatok bővített gépi tanulásra való előkészítésének feladatai
Előzetesen feldolgozni, és adattisztítást is a fontos feladatok, amelyek általában kell elvégezni, mielőtt adatkészlet használható hatékonyan a machine Learning szolgáltatáshoz. Nyers adatokat gyakran zajos és nem megbízható, és esetleg hiányzik a értékeket. Az ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ezek a feladatok tartozik, a csoportos adatelemzési folyamat (TDSP), és általában hajtsa végre egy kezdeti feltárásával egy adatkészlet felderítésére, és tervezze meg a szükséges előzetes feldolgozás segítségével. Részletesebb útmutatásért TDSP folyamattal kapcsolatban, tekintse meg a leírt lépéseket a [csoportos adatelemzési folyamat](overview.md).

Előzetes feldolgozása és tisztítási feladatok, például az adatok feltárására feladat elvégezhető a számos különböző környezetekben, például az SQL vagy a Hive vagy az Azure Machine Learning Studióban, és a különböző eszközök és nyelvek, például az R vagy Python, attól függően, ahol az adatok tárolása és hogyan van formázva. TDSP meghatározási jellegűek, mivel ezek a feladatok is igénybe vehet a munkafolyamatban a folyamat számos lépés helyén.

Ez a cikk bemutatja a különböző adatokat feldolgozó fogalmakat és feladatokat, amelyek előtt vagy után tölt be adatot az Azure Machine Learningbe végezhető.

Adatáttekintés és előfeldolgozásához belül az Azure Machine Learning studio kész példát talál a [előzetesen feldolgozni az adatokat az Azure Machine Learning Studióban](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videó.

## <a name="why-pre-process-and-clean-data"></a>Miért előzetes feldolgozása és az adatok?
Különböző forrásokból származó valós adatok megtörtént, és folyamatokat, és tartalmazhat szabálytalanságok vagy sérült adatokat, hogy az adatkészlet. A tipikus adatfolyamat minőségi problémák merülnek fel a következők:

* **Hiányos**: adatok nem áll rendelkezésre az attribútumok vagy a hiányzó értékeket tartalmazó.
* **Zajos**: adatok hibás rekordok vagy a kiugró értékeket tartalmaz.
* **Inkonzisztens**: adatok ütköző rekordok vagy az eltérések tartalmaznak.

Minőségi data quality prediktív modelleket előfeltétele. "Szemétgyűjtési szemétgyűjtési ki a" elkerülése és adatok minőség javítása, és ezért a teljesítmény modellezheti, amihez elengedhetetlen elvégzésére adat problémák korai azonosításához, és döntse el, a megfelelő adatokat feldolgozó és tisztítási lépéseket adatok állapota képernyő.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Mik azok az egyes alkalmazott tipikus adatfolyamat állapotát képernyők?
A Microsoft ellenőrizheti, hogy az adatok általános minőségének ellenőrzésével:

* Hány **rekordok**.
* Hány **attribútumok** (vagy **funkciók**).
* Az attribútum **adattípusok** (névleges, folyamatos vagy sorszámnál).
* Hány **hiányzó**.
* **Szabályosságának** az adatok.
  * Ha az adatok TSV- vagy CSV, ellenőrizze, hogy az oszlop elválasztók és a sor elválasztók mindig megfelelően külön oszlopok és sorok.
  * Ha az adatok HTML vagy XML formátumú, ellenőrizze-e az adatok jól formázott alapján a megfelelő szabványokon.
  * Elemzés is szükség lehet annak érdekében, hogy a strukturált adatok kinyerését részben strukturált vagy strukturálatlan adatokon.
* **Inkonzisztens adatfelderítési rekordok**. Ellenőrizze, hogy értékek engedélyezettek. például ha az adatok tartalmazzák a tanulói GPA, ellenőrizze, hogy a kijelölt tartomány van a GPA tegyük fel, hogy 0 ~ 4.

Ha megtalálta az adatokat, problémák **feldolgozási lépések** van szükség, amelyek gyakran magában foglalja a hiányzó értékeket, az adatok normalizálási, diszkretizálási tisztítás, eltávolítása és/vagy cserélje le a szöveg feldolgozási beágyazott karakter, ami hatással lehet az adatok igazítás, vegyes adattípusok közös mezőket, és mások.

**Az Azure Machine Learning helytelen formátumú táblázatos adatokat használ fel**.  Ha az adatok már táblázatos formában, előtti adatfeldolgozási elvégezhető közvetlenül az Azure Machine Learning, a Machine Learning studióban.  Ha adatok nem táblázatos formában, például azt az XML-elemzés lehet szükség annak érdekében, hogy az adatok átalakítása táblázatos formában.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Mik a főbb előtti adatfeldolgozási feladatok?
* **Adatok tisztítása**: Adja meg, vagy hiányzó észlelését, és távolítsa el a zajos adatok és a kiugró értékeket.
* **Adatátalakítás**: normalizálása adatok mérete és a zaj csökkentésére.
* **Adatcsökkentés**: minta adatfelderítési rekordok és attribútumok adatok egyszerűbb kezelése.
* **Adatok diszkretizálási**: folytonos attribútumok konvertálása kategorikus attribútumok a használat megkönnyítése érdekében bizonyos machine learning-módszerekkel együtt.
* **Szöveg tisztítás**: távolítsa el a miatt előfordulhat, hogy adatokat illesztését, beágyazott karaktereket, például a beágyazott lapjai tabulátorral tagolt adatfájlt, beágyazott új sorok, amelyek megszakadhat a rekordokat, és így tovább.

Az alábbi szakaszokban részletesen adatok feldolgozási lépések közül.

## <a name="how-to-deal-with-missing-values"></a>Hogyan foglalkozik a hiányzó értékeket?
Hiányzó kezelésére, érdemes először a probléma meghatározásához a hiányzó értékek is jobb kezelni az az oka. Tipikus hiányzó érték kezelése módszerek a következők:

* **Törlés**: hiányzó értéket tartalmazó rekordok eltávolítása
* **Üres behelyettesítési**: hiányzó értékeket cserélje le a helyőrző értékét: pl. *ismeretlen* kategorikus vagy 0 numerikus értékek.
* **Jelenti azt, hogy a helyettesítés**: hiányzó adatok esetén numerikus, cserélje le a hiányzó értékeket az átlag.
* **Gyakori behelyettesítési**: hiányzó adatok esetén kategorikus, cserélje le a hiányzó értékeket a leggyakoribb elem
* **Regresszió behelyettesítési**: egy regressziós módszerrel romlott értékeket cserélje le a hiányzó értékeket.  

## <a name="how-to-normalize-data"></a>Hogyan lehet adatokat normalizálása?
Adatok normalizálási újra méretezhető megadott tartományra numerikus értékeket. Népszerű normalizálási módszerek az alábbiak:

* **Min-Max normalizálási**: lineárisan átalakítja az adatokat számos, tegyük fel, hogy 0 és 1, ahol a minimális értéke 0 és maximális értékét 1-re való méretezése között.
* **Z-pontszám normalizálási**: méretezése és szórásnál alapján: az adatok és a középérték közötti különbség nullával való osztás a szórást.
* **Tizedes tört skálázás**: az adatok méretezését a tizedesjeltől attribútumérték helyezi.  

## <a name="how-to-discretize-data"></a>Hogyan lehet adatokat diszkretizálásához?
Adatok is diszkretizálható folyamatos érték átalakítása névleges attribútumok vagy intervallumok szerint. Néhány módszer az ennek a következők:

* **A Dobozolás egyenlő szélességű**: az összes lehetséges értékek az attribútumok felosztani N csoportokba az azonos méretű és rendelhet hozzá értéket, amely esetben az doboz bin számát.
* **A Dobozolás egyenlő magasságú**: osztása egy attribútum minden lehetséges értékek N csoportok, mindegyik példány azonos számú, hogyan végezheti el az értékeket, amelyeket tartoznak az doboz bin számát.  

## <a name="how-to-reduce-data"></a>Hogyan csökkenthető az adatok?
A könnyebb adatkezelési adatok méretének csökkentése érdekében különböző módszer áll rendelkezésre. Adatok mérete és a tartományhoz a következő módszerek lehet alkalmazni:

* **Jegyezze fel a mintavételi**: mintát vesz az adatfelderítési rekordok, és csak az adatok közül választhat, a reprezentatív részhalmazát.
* **Mintavételi attribútum**: válassza ki a legfontosabb attribútumok csak egy része az adatok közül.  
* **Összesítés**: az adatok felosztásához csoportokba, és tárolja a számokat az egyes csoportokhoz. Például az elmúlt 20 évben egy étteremlánc napi bevétel számú összesíthetők havi bevételhez az adatok méretének csökkentése érdekében.  

## <a name="how-to-clean-text-data"></a>Tiszta szöveges adatok hogyan?
**A táblázatos adatok szöveges mezők** befolyásoló oszlopok igazítása és/vagy rekord határok karaktereket tartalmazhatnak. Például egy tabulátorral tagolt fájl OK oszlop illesztését lapok ágyazva, és beágyazott új sor karakter szünet rekord sorokat. Nem megfelelő szöveg kódolási kezelése során írási/olvasási szöveges információk elvesztését, szándékolatlan bevezetése nem olvasható karaktereket, például null értékeket, és előfordulhat, hogy is érinthetik szöveg elemzése vezet. Alapos elemzés és -szerkesztő lehet szükség annak érdekében, hogy a tiszta szöveges mezők megfelelő igazítás, illetve strukturált kivonatot adatok és a strukturálatlan és részben strukturált szöveges adatok.

**Adatfeltárás** kínál egy korai képet kaphat az adatok. Ebben a lépésben egy adat-problémák száma ne maradjon lefedetlen felelősségi lehet, és a probléma megoldására szolgáló kapcsolódó módszereit lehet alkalmazni.  Fontos például, hogy mi a probléma forrását, és hogyan a probléma előfordulhat, hogy vezettek be kérdéseket tehet fel. Ennek segítségével könnyebben meghatározhatja az adatok feldolgozási lépések, amelyek kell tenni a problémák megoldásához. Milyen típusú insights egy szándékozik célosztályából származik az adatok is használható, az adatfeldolgozás rangsorolását.

## <a name="references"></a>Referencia
> *Adatbányászat: Elvekről és technikákról*, harmadik kiadás, Morgan Kaufmann, 2011 Jiawei Han Micheline Kamber és Jian Pei
> 
> 

