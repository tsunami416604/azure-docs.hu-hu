---
title: 'Szűrésen alapuló funkció kiválasztása: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatás szűrő alapú funkció-kiválasztási modulját a legnagyobb prediktív teljesítménnyel rendelkező adatkészlet funkcióinak azonosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517968"
---
# <a name="filter-based-feature-selection"></a>Szűrésen alapuló funkció kiválasztása

Ez a cikk azt ismerteti, hogyan használhatja a Azure Machine Learning Designer (előzetes verzió) [szűrő alapú funkciójának kiválasztási](filter-based-feature-selection.md) modulját a bemeneti adatkészletben található azon oszlopok azonosítására, amelyek a legnagyobb prediktív teljesítménnyel rendelkeznek. 

Általánosságban a *szolgáltatás kiválasztása* arra utal, hogy a megadott kimenet alapján statisztikai teszteket alkalmazzon a bemenetekre, hogy meghatározza, mely oszlopok a kimenetebb előrejelzési eredményei. A [szűrésen alapuló funkció kiválasztási](filter-based-feature-selection.md) modulja több funkció-kiválasztási algoritmust is biztosít, amelyek közül választhat, beleértve a korrelációs módszereket, például a Pearson korrelációs és a khi-négyzet értékeket. 

Ha a [szűrő alapú szolgáltatás kiválasztási](filter-based-feature-selection.md) modulját használja, akkor meg kell adnia egy adatkészletet, azonosítania kell a címkét vagy a függő változót tartalmazó oszlopot, majd meg kell adnia egyetlen módszert a funkció fontosságának méréséhez.

A modul a legjobb funkciók oszlopait tartalmazó adatkészletet jeleníti meg, a prediktív Power alapján rangsorolva. Emellett a funkciók nevét és a hozzájuk tartozó pontszámokat is megjeleníti a kiválasztott metrika alapján.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Mi a szűrésen alapuló funkció kiválasztása, és miért érdemes használni?  

Ennek a funkciónak a kiválasztásához a "filter-based" nevű modult kell használni, mert a kijelölt metrikával azonosítja a nem releváns attribútumokat, és kiszűri a modellből a redundáns oszlopokat.  Egyetlen statisztikai mértéket választ ki, amely megfelel az adatainak, a modul pedig kiszámítja az egyes szolgáltatások oszlopainak pontszámát. Az oszlopokat a szolgáltatás pontszámai rangsorolják. 

A megfelelő funkciók kiválasztásával növelheti a besorolás pontosságát és hatékonyságát. 

Általában csak azokat az oszlopokat használja, amelyek a legjobb pontszámot használják a prediktív modell létrehozásához. A gyenge funkciók kiválasztásával rendelkező oszlopok maradhatnak az adatkészletben, és figyelmen kívül hagyhatók a modell létrehozásakor.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Szolgáltatás kiválasztási mérőszámának kiválasztása

A **szűrésen alapuló szolgáltatás kiválasztása** számos mérőszámot biztosít az egyes oszlopokban található információ értékének értékeléséhez.  Ez a szakasz az egyes mérőszámok általános leírását és alkalmazási módját ismerteti. Az egyes mérőszámok használatának további követelményeit a [technikai megjegyzések](#technical-notes) szakaszban és az egyes modulok konfigurálására vonatkozó [utasításokban](#how-to-configure-filter-based-feature-selection) találja.

-   **Pearson korreláció**  

     A Pearson korrelációs statisztikája vagy a Pearson korrelációs együtthatója a statisztikai modellekben is ismert `r` értékként. Bármely két változó esetében egy olyan értéket ad vissza, amely jelzi a korreláció erősségét.

     A Pearson korrelációs együtthatóját úgy számítjuk ki, hogy a két változó egymástól való eltérését és a szórásuk szorzatát választja. Az együtthatót a két változó méretezésének változásai nem érintik.  

-   **Chi négyzetes**  

     A kétirányú Chi-négyzetes teszt olyan statisztikai módszer, amely azt méri, hogy a várt értékek a tényleges eredmények legyenek. A metódus feltételezi, hogy a változók véletlenszerűek, és a független változók megfelelő mintáján alapulnak. Az eredményül kapott KHI-négyzetes statisztikai érték azt jelzi, hogy az eredmények milyen mértékben származnak a várt (véletlenszerű) eredményből.  


> [!TIP]
> Ha egy másik lehetőségre van szüksége az egyéni funkciók kiválasztási módszeréhez, használja az [R-parancsfájl végrehajtása](execute-r-script.md) modult. 
##  <a name="how-to-configure-filter-based-feature-selection"></a>A szűrésen alapuló szolgáltatás kijelölésének konfigurálása

Kiválaszthatja a standard statisztikai metrikát, és a modul kiszámítja az oszlopok egy pár oszlopa közötti korrelációt: a Label oszlop és a szolgáltatás oszlopát.

1.  Adja hozzá a **szűrő alapú szolgáltatás kiválasztási** modulját a folyamathoz. A tervezőben a **funkció kiválasztása** kategóriában található.

2. Csatlakoztasson olyan bemeneti adatkészletet, amely legalább két olyan oszlopot tartalmaz, amelyek lehetséges funkciók.  

    A **IsFeature** attribútum beállításához használja a [metaadatok szerkesztése](edit-metadata.md) modult, és győződjön meg arról, hogy az oszlop elemzése és a szolgáltatás pontszáma jön létre. 

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a bemenetként megadott oszlopok lehetséges funkciók. Egy olyan oszlop például, amely egyetlen értéket tartalmaz, nem rendelkezik információs értékkel.
    >
    > Ha tudja, hogy vannak olyan oszlopok, amelyek helytelen funkciókat tesznek elérhetővé, akkor eltávolíthatja őket az oszlop kiválasztásával. A [metaadatok szerkesztése](edit-metadata.md) modullal is megadhatja azokat **kategorikusként**. 
3.  A **szolgáltatás pontozási módszere**esetében válassza az alábbi, a pontszámok kiszámításakor használandó statisztikai módszerek egyikét.  

    | Módszer              | Követelmények                             |
    | ------------------- | ---------------------------------------- |
    | Pearson korreláció | A címke lehet szöveg vagy numerikus. A funkcióknak numerikusnak kell lenniük. |
    Chi négyzetes| A címkék és a szolgáltatások lehetnek szöveg vagy numerikus elemek. Használja ezt a módszert a számítási funkció fontosságához két kategorikus oszlop esetében.|

    > [!TIP]
    > Ha módosítja a kijelölt metrikát, az összes többi beállítás alaphelyzetbe áll, ezért először állítsa be ezt a beállítást.)
4.  Jelölje be a csak szolgáltatásként megjelölt oszlopokhoz csak a korábban funkciókként jelölt oszlopok esetében a **csak a funkció bekapcsolása oszlopok** lehetőséget. 

    Ha törli ezt a beállítást, a modul egy pontszámot hoz létre minden olyan oszlophoz, amely egyébként megfelel a feltételeknek, akár a **kívánt funkciók számában**megadott oszlopok számával.  

5.  A **Target (cél) oszlopban**kattintson az **oszlop kiválasztásának elindítása** lehetőségre, hogy a Label (címke) oszlopot név vagy az index szerint válassza ki (az indexek egy-alapúak).  

     A statisztikai korrelációt magában foglaló összes módszerhez címkével ellátott oszlop szükséges. A modul tervezési idejű hibát ad vissza, ha a nem címke vagy több címke oszlopot választja. 

6.  A **kívánt funkciók számának**megadásához írja be az eredményként visszaadott szolgáltatás oszlopainak számát.  

     - A megadható szolgáltatások minimális száma 1, de azt javasoljuk, hogy növelje ezt az értéket.  

     - Ha a megadott számú kívánt funkció nagyobb, mint az adatkészlet oszlopainak száma, akkor a rendszer az összes funkciót visszaadja, még a nulla pontszámmal is.  

    - Ha kevesebb eredményt ad meg, mint a szolgáltatások oszlopai, a szolgáltatások csökkenő pontszám szerint vannak rangsorolva, és csak a legfontosabb funkciók lesznek visszaadva. 

7.  Futtassa a folyamatot, vagy válassza ki a [szűrésen alapuló funkció kiválasztási](filter-based-feature-selection.md) modulját, majd kattintson a **kijelölt futtatása**elemre.


## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után:

+ Ha meg szeretné tekinteni az elemzett szolgáltatások oszlopainak teljes listáját, valamint azok pontszámait **, kattintson a**jobb gombbal a modulra, válassza a **szolgáltatások**lehetőséget, majd kattintson a vizualizáció elemre.  

+ A szolgáltatás kiválasztási feltételei alapján létrehozott adatkészlet megtekintéséhez kattintson a jobb gombbal a modulra, válassza az **adatkészlet**lehetőséget, majd kattintson a **Megjelenítés**elemre. 

Ha az adatkészlet kevesebb oszlopot tartalmaz, mint amennyit várt, ellenőrizze a modul beállításait, valamint a bemenetként megadott oszlopok adattípusait. Ha például az 1 értékre állítja be a **kívánt funkciókat** , a kimeneti adatkészlet csak két oszlopot tartalmaz: a Label oszlopot és a legmagasan rangsorolt funkció oszlopot.


##  <a name="technical-notes"></a>Technikai megjegyzések  

### <a name="implementation-details"></a>Megvalósítás részletei

Ha a Pearson korrelációt egy numerikus szolgáltatáson és egy kategorikus címkén használja, a szolgáltatás pontszámának kiszámítása a következőképpen történik:  

1.  A kategorikus oszlopban minden szinten a numerikus oszlop feltételes középértékét kell kiszámítani.  

2.  A feltételes oszlop oszlopának korrelációja a numerikus oszloppal.  

### <a name="requirements"></a>Követelmények  

-   Nem hozható létre szolgáltatás-kiválasztási pontszám a **címkeként** vagy **pontszámként** megjelölt oszlopokhoz.  

-   Ha a metódus által nem támogatott adattípusú oszlopra próbál pontozási metódust használni, akkor a modul hibát jelez, vagy az oszlophoz null értékű pontszám lesz hozzárendelve.  

-   Ha egy oszlop logikai (igaz/hamis) értékeket tartalmaz, azok az igaz = 1 és a False = 0 értékkel lesznek feldolgozva.  

-   Egy oszlop nem lehet olyan szolgáltatás, amely **címkeként** vagy **pontszámként**lett megjelölve.  

### <a name="how-missing-values-are-handled"></a>A hiányzó értékek kezelése  

-   Nem adhat meg célként (label) oszlopként olyan oszlopot, amely tartalmazza az összes hiányzó értéket.  

-   Ha egy oszlop hiányzó értékeket tartalmaz, a rendszer figyelmen kívül hagyja azokat az oszlop pontszámának kiszámításakor.  

-   Ha egy szolgáltatásként kijelölt oszlop minden hiányzó értékkel rendelkezik, a rendszer nulla pontszámot rendel hozzá.   


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

