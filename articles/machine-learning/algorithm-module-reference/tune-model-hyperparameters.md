---
title: Modell-hiperparaméterek hangolása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Tune Model Hyperparameters modult az Azure Machine Learningben egy paraméter-söprés végrehajtásához egy modellen az optimális paraméterbeállítások meghatározásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064160"
---
# <a name="tune-model-hyperparameters"></a>Modell-hiperparaméterek hangolása

Ez a cikk ismerteti, hogyan használhatja a Tune Model Hyperparameters modul az Azure Machine Learning designer (előzetes verzió). A cél a gépi tanulási modell optimális hiperparamétereinek meghatározása. A modul több modellt épít és tesztel a beállítások különböző kombinációinak használatával. Összehasonlítja a mutatókat az összes modell, hogy a beállítások kombinációit. 

A kifejezés *paraméter* és *hiperparaméter* zavaró lehet. A modell *paraméterei* a modul jobb oldali ablaktáblájában beállítottak. Alapvetően ez a modul *paraméteres söprést* hajt végre a megadott paraméterbeállításokon. Megtanulja a _hiperparaméterek_optimális készletét, amely az egyes döntési fa, adatkészlet vagy regressziós módszer esetében eltérő lehet. Az optimális konfiguráció megtalálásának folyamatát néha tuningnak is *nevezik.* 

A modul a következő módszert támogatja a modell optimális beállításainak megtalálásához: *integrált vonat és dallam.* Ebben a módszerben konfigurálja a használni kívánt paramétereket. Ezután hagyja, hogy a modul több kombinációra is legépelje. A modul addig méri a pontosságot, amíg meg nem találja a "legjobb" modellt. A legtöbb tanuló modulnál kiválaszthatja, hogy mely paramétereket kell módosítani a képzési folyamat során, és melyekmaradjanak rögzítettek.

Attól függően, hogy mennyi ideig szeretné futtatni a hangolási folyamatot, dönthet úgy, hogy az összes kombinációt kimerítően teszteli. Vagy lerövidítheti a folyamatot a paraméterkombinációk rácsának létrehozásával és a paraméterrács randomizált részhalmazának tesztelésével.

Ez a módszer egy betanított modellt hoz létre, amelyet újrafelhasználhat.  

> [!TIP] 
> Elvégezhet egy kapcsolódó feladatot. A finomhangolás megkezdése előtt alkalmazza a szolgáltatáskijelölést a legmagasabb információs értékkel rendelkező oszlopok vagy változók meghatározásához.

## <a name="how-to-configure-tune-model-hyperparameters"></a>A Tune Model hyperparameters konfigurálása  

A gépi tanulási modell optimális hiperparamétereinek megismerése a folyamatok jelentős használatát igényli.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Modell betanítása paraméteres söpréssel  

Ez a szakasz azt ismerteti, hogyan hajthatja végre az alapvető paraméter-söprést, amely beiktat egy modellt a Modell hiperparamétereinek finomhangolása modul használatával.

1.  Adja hozzá a Tune Model Hyperparameters modult a tervező ben lévő folyamathoz.

2.  Képzetlen modell csatlakoztatása a bal szélső bemenethez. 



4.  Adja hozzá a betanításhoz használni kívánt adatkészletet, és csatlakoztassa a Tune Model Hyperparameters középső bemenetéhez.  

    Ha címkézett adatkészlettel rendelkezik, csatlakoztathatja a jobb szélső bemeneti porthoz (**Nem kötelező érvényesítési adatkészlet**). Ez lehetővé teszi a pontosság mérését edzés és hangolás közben.

5.  A Tune Model Hyperparameters jobb oldali panelén válasszon egy értéket a **Paraméterssöprés isztikai módhoz.** Ez a beállítás határozza meg a paraméterek kijelölésének módját.

    - **Teljes rács**: Ha ezt a lehetőséget választja, a modul a rendszer által előre meghatározott rácson keresztül hurkok, hogy kipróbálja a különböző kombinációkat, és azonosítsa a legjobb tanulót. Ez a beállítás akkor hasznos, ha nem tudja, mi lehet a legjobb paraméterbeállítások, és szeretné kipróbálni az összes lehetséges értékkombinációt.

    - **Véletlenszerű söprés:** Ha ezt a beállítást választja, a modul véletlenszerűen kiválasztja a paraméterértékeket egy rendszer által definiált tartományban. Meg kell adnia a modul által végrehajtandó futtatások maximális számát. Ez a beállítás akkor hasznos, ha szeretné növelni a modell teljesítményét a kiválasztott mérőszámok használatával, de továbbra is megőrzése számítási erőforrások.    

6.  A **Címke oszlopban**nyissa meg az oszlopkijelölőt, és válasszon egyetlen feliratoszlopot.

7.  Válassza ki a futtatások számát:

    1. **A véletlenszerű söprésen futó futtatások maximális száma:** Ha véletlenszerű söprést választ, megadhatja, hogy a modellt hányszor kell betanítani a paraméterértékek véletlenszerű kombinációjának használatával.

8.  A **Rangsorolás**területen válasszon egyetlen metrikát a modellek rangsorolásához.

    Paraméter-söprés futtatásakor a modul kiszámítja a modelltípus összes vonatkozó metrikáját, és visszaadja azokat a **Sweep results** jelentésben. A modul külön metrikákat használ a regressziós és besorolási modellekhez.

    A kiválasztott mérőszám azonban meghatározza a modellek rangsorolásának módját. Csak a felső modell, a kiválasztott metrika szerint rangsorolva, a kimenet, mint egy betanított modell a pontozáshoz használható.

9.  A **Véletlenszerű vetőmag**mezőbe írja be a paraméters söprés indításához használandó számot. 

10. Küldje el a folyamatot.

## <a name="results-of-hyperparameter-tuning"></a>A hiperparaméter-hangolás eredménye

Amikor a képzés befejeződött:

+ A legjobb modell pontossági mutatóinak megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Visualize parancsot.**

    A kimenet tartalmazza a modelltípusra vonatkozó összes pontossági mutatót, de a rangsoroláshoz kiválasztott metrika határozza meg, hogy melyik modell tekinthető "legjobbnak".

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.


## <a name="technical-notes"></a>Technikai megjegyzések

Ez a rész a megvalósítás részleteit és tippjeit tartalmazza.

### <a name="how-a-parameter-sweep-works"></a>A paraméteres söprés működése

A paraméteres söprés beállításakor meghatározhatja a keresés hatókörét. A keresés a véletlenszerűen kiválasztott paraméterek véges számát használhatja. Vagy lehet, hogy egy kimerítő keresés egy paraméter térben, hogy a megadott.

+ **Véletlenszerű söprés:** Ez a beállítás egy modellt egy meghatározott számú iteráció használatával képez be. 

  Meg kell adnia egy értéktartományt, amely felett kell, és a modul az értékek véletlenszerűen kiválasztott részhalmazát használja. Az értékek et csereként választjuk ki, ami azt jelenti, hogy a korábban véletlenszerűen kiválasztott számok nem törlődnek a rendelkezésre álló számok készletéből. Így annak az esélye, hogy bármilyen értéket kiválasztanak, ugyanaz marad az összes menetben.  

+ **Teljes rács**: A teljes rács használatának lehetősége azt jelenti, hogy minden kombinációt tesztelnek. Ez a lehetőség a legalaposabb, de a legtöbb időt igényli. 

### <a name="controlling-the-length-and-complexity-of-training"></a>A képzés hosszának és összetettségének szabályozása

A beállítások számos kombinációjára történő iterálás időigényes lehet, így a modul számos lehetőséget kínál a folyamat korlátozására:

+ Korlátozza a modell teszteléséhez használt ismétlések számát.
+ Korlátozza a paraméter helyet.
+ Korlátozza mind az ismétlések számát, mind a paraméterterületet.

Azt javasoljuk, hogy a folyamat a beállításokat, hogy meghatározza a leghatékonyabb betanítási módszer egy adott adatkészlet és modell.

### <a name="choosing-an-evaluation-metric"></a>Értékelési mérőszám kiválasztása

A tesztelés végén a modell egy jelentést jelenít meg, amely tartalmazza az egyes modellek pontosságát, így áttekintheti a metrika eredményeit:

- A metrikák egységes készlete minden bináris osztályozási modellhez használatos.
- A pontosság minden többosztályos besorolási modellhez használatos.
- A regressziós modellek hez különböző metrikák at használnak. 

Azonban az edzés során ki kell *választania* egy metrikát a hangolási folyamat során létrehozott modellek rangsorolásához. Előfordulhat, hogy a legjobb mérőszám az üzleti problémától, valamint a hamis pozitív és hamis negatív értékektől függően változik.

#### <a name="metrics-used-for-binary-classification"></a>Bináris osztályozáshoz használt mérőszámok

-   **A pontosság** a valódi eredmények és az összes eset aránya.  

-   **A precizitás** a valódi eredmények pozitív eredményekhez viszonyított aránya.  

-   **Visszahívás** a töredéke az összes helyes eredményt az összes eredményt.  

-   **Az F-pontszám** olyan mérték, amely egyensúlyt teremt a pontosság és a visszahívás között.  

-   **Az AUC** olyan érték, amely a görbe alatti területet jelöli, amikor az x tengelyen hamis pozitívokat ábrázolnak, és a valódi pozitívokat az y tengelyen ábrázolja.  

-   **Az átlagos naplóveszteség** két valószínűségi eloszlás közötti különbség: az igaz és a modellben lévő.  

#### <a name="metrics-used-for-regression"></a>Regresszióhoz használt mérőszámok

-   **Az átlagos abszolút hiba** átlaga a modell összes hibáját, ahol a *hiba* az előre jelzett érték és a valós érték távolságát jelenti. Ez gyakran rövidítve *MAE*.  

-   **Az átlagos négyzetes hiba gyöke** a hibák négyzeteinek átlagát méri, majd ennek az értéknek a gyökerét veszi. Ez gyakran rövidítve *RMSE*.  

-   **A relatív abszolút hiba** a hibát a valós érték százalékában jelöli.  

-   **A relatív négyzetes hiba** normalizálja a teljes négyzetes hibát az előre jelzett értékek teljes négyzetes hibájával való osztva.  

-   **A meghatározási együttható** egyetlen szám, amely azt jelzi, hogy az adatok mennyire illeszkednek egy modellhez. Az egyik értéke azt jelenti, hogy a modell pontosan egyezik az adatokkal. A nulla érték azt jelenti, hogy az adatok véletlenszerűek, vagy más módon nem illeszkednek a modellhez. Ez gyakran nevezik *r<sup>2</sup>*, *R<sup>2</sup>*, vagy *r-négyzet*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Olyan modulok, amelyek nem támogatják a paraméteres söprést

Az Azure Machine Learning szinte minden tanulója támogatja a keresztellenőrzést egy integrált paraméteres söpréssel, amellyel kiválaszthatja a folyamatparamétereit. Ha a tanuló nem támogatja egy értéktartomány beállítását, akkor is használhatja azt keresztérvényesítésben. Ebben az esetben a letapogatáshoz egy tartománynyi engedélyezett érték van kiválasztva. 


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

