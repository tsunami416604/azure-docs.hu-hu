---
title: 'Szűrőalapú szolgáltatáskiválasztása: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a szűrőalapú szolgáltatáskiválasztási modult az Azure Machine Learningben a legnagyobb prediktív teljesítményű adatkészlet funkcióinak azonosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477595"
---
# <a name="filter-based-feature-selection"></a>Szűrőalapú szolgáltatásválasztás

Ez a cikk ismerteti, hogyan használhatja a szűrő alapú szolgáltatáskiválasztása modul az Azure Machine Learning designer (előzetes verzió). Ez a modul segít azonosítani a bemeneti adatkészlet azon oszlopait, amelyek a legnagyobb prediktív erővel rendelkeznek. 

A *szolgáltatáskiválasztása* általában a statisztikai tesztek bemenetekre történő alkalmazásának folyamatára vonatkozik, adott egy adott kimenet alapján. A cél annak meghatározása, hogy mely oszlopok prediktívebbek a kimenettel. A szűrőalapú szolgáltatáskijelölési modul több funkciókijelölési algoritmust biztosít. A modul olyan korrelációs módszereket tartalmaz, mint a Pearson korreláció és a khi-négyzet értékek. 

A Szűrőalapú szolgáltatáskijelölés modul használatakor adatkészletet kell megadnia, és azonosíthatja a címkét vagy függő változót tartalmazó oszlopot. Ezután meg kell adnia egy módszert a jellemzőfontosság méréséhez.

A modul olyan adatkészletet ad ki, amely a legjobb funkcióoszlopokat tartalmazza, a prediktív teljesítmény szerint rangsorolva. Azt is kiírja a funkciók nevét és a pontszámok a kiválasztott metrika.  

## <a name="what-filter-based-feature-selection-is"></a>Milyen szűrőalapú szolgáltatásválasztás  

Ezt a modult a szolgáltatás kiválasztásához "szűrőalapúnak" nevezzük, mert a kiválasztott metrika segítségével nem releváns attribútumokat talál. Ezután kiszűri a redundáns oszlopokat a modellből. Egyetlen statisztikai mértéket választ, amely megfelel az adatoknak, és a modul kiszámítja az egyes jellemzőoszlopok pontszámát. Az oszlopokat a funkciópontszámuk alapján rangsorolva adja vissza a rendszer. 

A megfelelő funkciók kiválasztásával potenciálisan javíthatja az osztályozás pontosságát és hatékonyságát. 

Általában csak a legjobb pontszámokkal rendelkező oszlopokat használja a prediktív modell létrehozásához. A gyenge funkciókijelölési pontszámmal rendelkező oszlopok az adatkészletben maradhatnak, és a modell létrehozásakor figyelmen kívül hagyhatók.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Szolgáltatáskijelölési mutató kiválasztása

A szűrőalapú szolgáltatáskijelölési modul számos mérőszámot biztosít az egyes oszlopok ban lévő információs érték felméréséhez. Ez a szakasz az egyes metrikák általános leírását, és hogyan alkalmazzák. Az egyes metrikák használatára vonatkozó további követelményeket a [műszaki megjegyzésekben](#technical-notes) és az egyes modulok konfigurálásának [utasításokban](#how-to-configure-filter-based-feature-selection) találja.

-   **Pearson korreláció**  

    Pearson korrelációs statisztikája, vagyis a Pearson korrelációs együtthatója a statisztikai modellekben is `r` ismert értékként. Bármely két változó esetében egy olyan értéket ad vissza, amely a korreláció erősségét jelzi.

    A Pearson korrelációs együtthatóját két változó kovarianciájának figyelembevételével és a szórásuk szorzatával való elosztjuk. A két változó léptékváltozása nincs hatással az együtthatóra.  

-   **Chi négyzet**  

    A kétirányú khi-négyzet teszt egy statisztikai módszer, amely azt méri, hogy milyen közel vannak a várt értékek a tényleges eredményekhez. A módszer feltételezi, hogy a változók véletlenszerűek, és független változók megfelelő mintájából származnak. Az eredményül kapott khi-négyzet statisztika azt jelzi, hogy milyen messze vannak az eredmények a várt (véletlenszerű) eredménytől.  


> [!TIP]
> Ha az egyéni szolgáltatáskijelölési módszerhez más beállításra van szüksége, használja az [R-parancsfájl végrehajtása](execute-r-script.md) modult. 

## <a name="how-to-configure-filter-based-feature-selection"></a>A szűrőalapú szolgáltatáskijelölés beállítása

Kiválaszt egy szabványos statisztikai mérőszámot. A modul kiszámítja az oszlopok egy pára közötti korrelációt: a címkeoszlopot és egy jellemzőoszlopot.

1.  Adja hozzá a szűrőalapú szolgáltatáskijelölési modult a folyamathoz. A tervező **Funkciókijelölés kategóriájában** találja.

2. Csatlakoztasson egy olyan bemeneti adatkészletet, amely legalább két olyan oszlopot tartalmaz, amelyek potenciális szolgáltatások.  

    Annak érdekében, hogy egy oszlop ot elemezzék, és egy szolgáltatáspontszám jön létre, használja a [Metaadat szerkesztése](edit-metadata.md) modult az **IsFeature** attribútum beállításához. 

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a bemenetként biztosított oszlopok potenciális szolgáltatások. Egy egyetlen értéket tartalmazó oszlopnak például nincs információs értéke.
    >
    > Ha tudja, hogy egyes oszlopok rossz funkciókat hoznának elő, eltávolíthatja őket az oszlopkijelölésből. A [Metaadatok szerkesztése](edit-metadata.md) modullal **kategorikusként**is megjelölheti őket. 
3.  A **Funkciópontozási módszer ben**válasszon az alábbi létrehozott statisztikai módszerek közül a pontszámok kiszámításához.  

    | Módszer              | Követelmények                             |
    | ------------------- | ---------------------------------------- |
    | Pearson korreláció | A címke lehet szöveg vagy numerikus. A jellemzőknek numerikusaknak kell lenniük. |
    Chi négyzet| A címkék és jellemzők lehetnek szövegesek vagy numerikusak. Ezzel a módszerrel két kategorikus oszlop jellemzőfontosságát számítja ki.|

    > [!TIP]
    > Ha módosítja a kiválasztott mutatót, az összes többi kijelölés alaphelyzetbe áll. Tehát győződjön meg róla, hogy ezt a beállítást először.
4.  Válassza a **Csak jellemzőoszlopokon funkcióoszlopokon** lehetőséget, ha csak a korábban szolgáltatásként megjelölt oszlopokhoz szeretne pontszámot létrehozni. 

    Ha törli ezt a beállítást, a modul létrehoz egy pontszámot minden olyan oszlophoz, amely egyébként megfelel a feltételeknek, a **kívánt jellemzők száma**mezőben megadott oszlopok számáig.  

5.  A **Cél oszlopban**válassza az **Indítás oszlopválasztó** lehetőséget a címkeoszlop név vagy index szerint való kiválasztásához. (Az indexek egyalapúak.)  
    Minden statisztikai korrelációval járó módszerhez szükség van egy címkeoszlopra. A modul tervezési időhibát ad vissza, ha nem választ címkeoszlopot vagy több címkeoszlopot. 

6.  A **Kívánt szolgáltatások száma**mezőbe írja be az eredményként visszaadható szolgáltatásoszlopok számát:  

    - A megadható szolgáltatások minimális száma egy, de azt javasoljuk, hogy növelje ezt az értéket.  

    - Ha a megadott számú kívánt szolgáltatás nagyobb, mint az adatkészlet oszlopainak száma, akkor az összes szolgáltatást visszaadja. Még a nulla pontszámmal rendelkező funkciók is visszakerülnek.  

    - Ha kevesebb eredményoszlopot ad meg, mint amennyi jellemzőoszlop van, a szolgáltatások csökkenő pontszám szerint vannak rangsorolva. Csak a legfelső funkciók at adja vissza. 

7.  Küldje el a folyamatot, vagy jelölje ki a Szűrőalapú szolgáltatáskijelölés imát, és válassza a **Kijelölt futtatás lehetőséget.**


## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után:

+ Az elemzett jellemzőoszlopok és pontszámaik teljes listájának megtekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Visualize parancsot.**  

+ Ha a szolgáltatáskiválasztási feltételek alapján szeretné megtekinteni az adatkészletet, kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés parancsot.** 

Ha az adatkészlet a vártnál kevesebb oszlopot tartalmaz, ellenőrizze a modul beállításait. Ellenőrizze a bemenetként megadott oszlopok adattípusait is. Ha például **a Kívánt funkciók számát** 1-re állítja, a kimeneti adatkészlet csak két oszlopot tartalmaz: a címkeoszlopot és a legmagasabban rangsorolt jellemzőoszlopot.


##  <a name="technical-notes"></a>Technikai megjegyzések  

### <a name="implementation-details"></a>A végrehajtás részletei

Ha Pearson korrelációt használ egy numerikus jellemzőn és egy kategorikus címkén, a jellemzőpontszám kiszámítása a következőképpen történik:  

1.  A kategorikus oszlop minden szintjén számítsa ki a numerikus oszlop feltételes átlagát.  

2.  A feltételes átlagok oszlopának és a numerikus oszlopnak a korrelációja.  

### <a name="requirements"></a>Követelmények  

-   A jellemzőkiválasztási pontszám nem generálható a **Címke** vagy **a Pontszám** oszlopként kijelölt oszlopokhoz.  

-   Ha olyan adattípusú oszlopot próbál használni, amelyet a metódus nem támogat, a modul hibát fog felvetni. Vagy nulla pontszámot rendel az oszlophoz.  

-   Ha egy oszlop logikai (igaz/hamis) értékeket `True = 1` tartalmaz, a feldolgozás a és `False = 0`a .  

-   Az oszlop nem lehet jellemző, ha **címkeként** vagy **pontszámként**van megjelölve.  

### <a name="how-missing-values-are-handled"></a>A hiányzó értékek kezelésének  

-   Cél (címke) oszlopként nem adhat meg olyan oszlopot, amelynek minden hiányzó értéke hiányzik.  

-   Ha egy oszlop hiányzó értékeket tartalmaz, a modul figyelmen kívül hagyja azokat, amikor kiszámítja az oszlop pontszámát.  

-   Ha egy jellemzőoszlopként megjelölt oszlop ban minden hiányzó érték található, a modul nulla pontszámot rendel hozzá.   


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

