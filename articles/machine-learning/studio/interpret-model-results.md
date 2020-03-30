---
title: Modelleredmények értelmezése
titleSuffix: ML Studio (classic) - Azure
description: Hogyan válasszuk ki az optimális paraméterkészletet egy algoritmushoz a pontszámmodell kimeneteinek használatával és megjelenítéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218064"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Modelleredmények értelmezése az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Ez a témakör bemutatja, hogyan vizualizálhatja és értelmezheti az előrejelzési eredményeket az Azure Machine Learning Studio (klasszikus) alkalmazásban. Miután betanított egy modellt, és elvégezte az előrejelzéseket a tetején ("pontozták a modellt"), meg kell értenie és értelmeznie kell az előrejelzési eredményt.



Az Azure Machine Learning Studio négy fő gépi tanulási modelljét (klasszikus) üzemelteti:

* Osztályozás
* Fürtözés
* Regresszió
* Ajánló rendszerek

A modellek előrejelzéséhez használt modulok a következők:

* [Score Modell][score-model] modul osztályozáshoz és regresszióhoz
* [Hozzárendelés fürtmodulhoz][assign-to-clusters] fürtözéshez
* [Score Matchbox ajánló][score-matchbox-recommender] az ajánlási rendszerekhez

Ez a dokumentum bemutatja, hogyan értelmezheti az előrejelzési eredményeket az egyes modulokesetében. Ezeknek a moduloknak a áttekintését a [Paraméterek kiválasztása az Azure Machine Learning Studio (klasszikus) algoritmusainak optimalizálásához című témakörben találja.](algorithm-parameters-optimize.md)

Ez a témakör az előrejelzés értelmezésével foglalkozik, de a modell értékelésével nem. A modell kiértékeléséről a [Modell teljesítményének kiértékelése az Azure Machine Learning Studio (klasszikus)](evaluate-model-performance.md)alkalmazásban című témakörben talál további információt.

Ha most ismerkedik az Azure Machine Learning Studio (klasszikus) és segítségre van szüksége egy egyszerű kísérlet létrehozásához az első lépésekhez, olvassa el az Egyszerű kísérlet létrehozása az [Azure Machine Learning Studio (klasszikus) című témakört.](create-experiment.md)

## <a name="classification"></a>Osztályozás
A besorolási problémáknak két alkategóriája van:

* Problémák csak két osztály (két osztály vagy bináris besorolás)
* Kettőnél több osztállyal kapcsolatos problémák (többosztályos osztályozás)

Az Azure Machine Learning Studio (klasszikus) különböző modulokkal rendelkezik az ilyen típusú besorolások kezeléséhez, de az előrejelzési eredmények értelmezésének módszerei hasonlóak.

### <a name="two-class-classification"></a>Kétosztályos besorolás
**Példakísérlet**

Egy példa a kétosztályos osztályozási probléma osztályozása írisz virágok. A feladat az íriszvirágok osztályozása jellemzőik alapján. Az Azure Machine Learning Studio (klasszikus) írisz-adatkészlet e népszerű [Iris-adatkészlet](https://en.wikipedia.org/wiki/Iris_flower_data_set) egy része, amely csak két virágfaj (0 és 1 osztály) példányait tartalmazza. Négy jellemzője minden virág (sepal hossza, sepal szélessége, szirom hossza, és a szirom szélessége).

![Az íriszkísérlet képernyőképe](./media/interpret-model-results/1.png)

1. ábra Iris kétosztályos besorolási probléma kísérlet

A probléma megoldására kísérletet hajtottak végre, amint azt az 1. Egy kétosztályos kiemelt döntési fa modell bevan tanítva és pontozva. Most már vizualizálhatja az előrejelzési eredményeket a [Score Model][score-model] modulból, ha a [Score Model][score-model] modul kimeneti portjára kattint, majd a **Visualize parancsra**kattint.

![Pontszámmodell-modul](./media/interpret-model-results/1_1.png)

Ez a 2.

![Az írisz kétosztályos osztályozási kísérlet eredményei](./media/interpret-model-results/2.png)

2. ábra Pontszámmodell-eredmény megjelenítése kétosztályos besorolásban

**Eredmény értelmezése**

Az eredménytáblázathat oszlopból áll. A bal négy oszlop a négy funkció. A jobb oldali két oszlop, a pontozott címkék és a pontozott valószínűségek az előrejelzési eredmények. A Pontozott valószínűség oszlop annak valószínűségét mutatja, hogy egy virág a pozitív osztályba tartozik (1. osztály). Például az oszlop első száma (0,028571) azt jelenti, hogy 0,028571 valószínűsége van annak, hogy az első virág az 1. A Pontozott címkék oszlop az egyes virágok előre jelzett osztályát jeleníti meg. Ez a Pontozott valószínűség oszlopon alapul. Ha egy virág pontozott valószínűsége nagyobb, mint 0,5, akkor az előrejelzések szerint 1. Ellenkező esetben a 0-osztály lesz.

**Webszolgáltatás közzététele**

Miután az előrejelzési eredmények et megértették és megítélték a hangot, a kísérlet webes szolgáltatásként közzétehető, így különböző alkalmazásokban telepítheti, és hívhatja, hogy osztályelőrejelzéseket kapjon minden új íriszvirágon. Ha meg szeretné tudni, hogyan módosíthatja a betanítási kísérletet pontozási kísérletté, és hogyan teheti közzé webszolgáltatásként, olvassa el [a 3.](tutorial-part3-credit-risk-deploy.md) Ez az eljárás pontozási kísérletet biztosít a 3.

![Képernyőkép a pontozási kísérletről](./media/interpret-model-results/3.png)

3. ábra Pontozás a szivárványhártya kétosztályos osztályozási probléma kísérlet

Most be kell állítania a bemeneti és kimeneti a webszolgáltatás. A bemenet a score [modell][score-model]megfelelő bemeneti portja, amely az Iris virág bemeneti funkciókat tartalmaz. A kimenet kiválasztása attól függ, hogy érdekli-e az előre jelzett osztály (pontozott címke), a pontozott valószínűség vagy mindkettő. Ebben a példában feltételezzük, hogy mindkettő érdekli. A kívánt kimeneti oszlopok kijelöléséhez használja az [Oszlopok kijelölése az Adatkészlet modulban.][select-columns] Kattintson [az Oszlopok kijelölése az adatkészletben][select-columns], majd az **Indítás oszlopválasztó**gombra, majd válassza **a Pontozott címkék** és a **Pontozott valószínűség lehetőséget.** Miután beállította az [Oszlopok kijelölése az adatkészletben az Oszlopok kijelölése][select-columns] és újra futtatása kimeneti portját, készen kell állnia arra, hogy a **FORRÁSSZOLGÁLTATÁS közzététele**gombra kattintva közzétegye a pontozási kísérletet webszolgáltatásként. A végső kísérlet úgy néz ki, mint a 4.

![Az írisz kétosztályos osztályozási kísérlet](./media/interpret-model-results/4.png)

4. ábra Végső pontozási kísérlet egy írisz kétosztályos besorolási probléma

Miután futtatta a webszolgáltatást, és megadta egy tesztpéldány néhány jellemzőértékét, az eredmény két számot ad vissza. Az első szám a pontozott címke, a második pedig a pontozott valószínűség. Ez a virág az előrejelzések szerint class 1 0,9655 valószínűséggel.

![Tesztértelmező pontszámmodell](./media/interpret-model-results/4_1.png)

![Pontozási vizsgálati eredmények](./media/interpret-model-results/5.png)

5. ábra Kétosztályos írisz besorolás webszolgáltatás-eredménye

### <a name="multi-class-classification"></a>Többosztályos osztályozás
**Példakísérlet**

Ebben a kísérletben egy levélfelismerő feladatot hajt végre a többosztályos besorolás példájaként. Az osztályozó megkísérli megjósolni egy bizonyos betűt (osztályt) a kézzel írt képekből kinyert néhány kézzel írt attribútumérték alapján.

![Példa levélfelismerésre](./media/interpret-model-results/5_1.png)

A betanítási adatokban 16 funkció van kinyerve kézzel írt levélképekből. A 26 betű alkotja a 26 osztályunkat. 6. ábra egy kísérletet mutat be, amely betanít egy többosztályos osztályozási modellt a betűfelismeréshez, és előrejelzést ad egy tesztadatkészlet ugyanazon szolgáltatáskészletén.

![Levélfelismerési többosztályos osztályozási kísérlet](./media/interpret-model-results/6.png)

6. ábra Levélfelismerés imosztális besorolási probléma kísérlete

A [Score Model][score-model] modul eredményeinek megjelenítése a [Score Model][score-model] modul kimeneti portjára kattintva, majd a **Visualize parancsra**kattintva, a 7.

![Pontszámmodell eredményei](./media/interpret-model-results/7.png)

7. ábra A pontszámmodell megjelenítése többosztályos besorolást eredményez

**Eredmény értelmezése**

A bal oldali 16 oszlop a tesztkészlet jellemzőértékeit jelöli. Az oszlopok nevei, mint a pontozott valószínűség az osztály "XX" olyanok, mint a pontozott valószínűség oszlop a kétosztályos esetben. Azt mutatják, a valószínűsége, hogy a megfelelő bejegyzés esik egy bizonyos osztályba. Az első bejegyzésnél például 0,003571 annak valószínűsége, hogy "A", 0,000451 valószínűsége, hogy "B" és így tovább. Az utolsó oszlop (Pontozott címkék) megegyezik a kétosztályos eset pontozott címkével. A legnagyobb pontozott valószínűséggel rendelkező osztályt választja a megfelelő tétel előre jelzett osztályaként. Az első bejegyzésesetében például a pontozott címke "F", mivel a legnagyobb valószínűséggel "F" (0,916995).

**Webszolgáltatás közzététele**

A pontozott címkét is lekaphatja az egyes bejegyzésekhez, és a pontozott címke valószínűségét. Az alapvető logika az, hogy megtalálja a legnagyobb valószínűség között a pontozott valószínűségek. Ehhez az R parancsfájl végrehajtása modult kell [használnia.][execute-r-script] Az R-kódot a 8.

![R-kód példa](./media/interpret-model-results/8.png)

8. ábra R-kód a pontozott címkék kinyeréséhez és a címkék kapcsolódó valószínűségéhez

![Kísérlet eredménye](./media/interpret-model-results/9.png)

9. ábra. A levélfelismeréstöbbosztályos osztályozási problémájának végső pontozási kísérlete

Miután közzétette és futtatta a webszolgáltatást, és beírt néhány bemeneti jellemzőértéket, a visszaadott eredmény a 10. Ez a kézzel írt levél, annak kivont 16 funkciók, az előrejelzések szerint a "T" a 0,9715 valószínűsége.

![Tesztértelmező pontszám modul](./media/interpret-model-results/9_1.png)

![A vizsgálat eredménye](./media/interpret-model-results/10.png)

10. ábra. Többosztályos besorolás webszolgáltatás-eredménye

## <a name="regression"></a>Regresszió
A regressziós problémák eltérnek a besorolási problémáktól. Besorolási probléma esetén diszkrét osztályokat próbál megjósolni, például azt, hogy melyik osztályhoz tartozik az íriszvirág. De amint a regressziós probléma alábbi példájában látható, egy folyamatos változót próbál megjósolni, például egy autó árát.

**Példakísérlet**

Használja az autóár-előrejelzést példaként a regresszióhoz. Megpróbálja megjósolni az autó árát a jellemzői alapján, beleértve a gyártmányt, az üzemanyag típusát, a karosszériatípusát és a hajtókereket. A kísérletet a 11.

![Autó ár regressziós kísérlet](./media/interpret-model-results/11.png)

11. ábra. Autó ár regressziós probléma kísérlet

A [Score Model][score-model] modul megjelenítése az eredmény a 12.

![Pontozási eredmények az autóár-előrejelzési problémához](./media/interpret-model-results/12.png)

12. ábra. Pontozási eredmény az autóár-előrejelzési problémához

**Eredmény értelmezése**

A pontozott címkék a pontozási eredmény eredményoszlopa. A számok az egyes autók előre jelzett árai.

**Webszolgáltatás közzététele**

Közzéteheti a regressziós kísérletet egy webszolgáltatásban, és ugyanúgy meghívhatja az autóár-előrejelzéshez, mint a kétosztályos besorolási használati esetben.

![Pontozási kísérlet autó ára regressziós probléma](./media/interpret-model-results/13.png)

13. ábra. Pontozási kísérlet egy autó ára regressziós probléma

A webszolgáltatás futtatásakor a visszaadott eredmény a 14. Az előre jelzett ár ez az autó 15.085,52 $.

![Tesztértelmező pontozási modul](./media/interpret-model-results/13_1.png)

![Pontozási modul eredményei](./media/interpret-model-results/14.png)

14. ábra. Webszolgáltatás eredménye egy autó árregressziós probléma

## <a name="clustering"></a>Fürtözés
**Példakísérlet**

Használjuk újra az Iris adatkészletet egy fürtözési kísérlet létrehozásához. Itt kiszűrheti az adatkészlet osztálycímkéit, hogy az csak szolgáltatásokkal rendelkezik, és fürtözésre használható legyen. Ebben az írisz használati esetben adja meg a betanítási folyamat során kettőnek kell lennie a fürtök számát, ami azt jelenti, hogy a virágokat két osztályba csoportosítaná. A kísérletet a 15.

![Iris klaszterek probléma kísérlet](./media/interpret-model-results/15.png)

15. ábra. Iris klaszterek probléma kísérlet

A fürtözés annyiban különbözik a besorolástól, hogy a betanítási adatkészlet önmagában nem rendelkezik alapigazság-címkékkel. A fürtözés a betanítási adatkészletpéldányokat különálló fürtökbe csoportosítja. A betanítási folyamat során a modell a bejegyzéseket a funkciók közötti különbségek megismerésével címkézi. Ezt követően a betanított modell használható a jövőbeli bejegyzések további osztályozására. Az eredménynek két része van, amelyek érdekelnek minket egy fürtözési problémán belül. Az első rész a betanítási adatkészlet címkézése, a második pedig egy új adatkészlet besorolása a betanított modell.

Az eredmény első része a [Train Clustering Model][train-clustering-model] bal oldali kimeneti portjára, majd a **Visualize parancsra**kattintva jeleníthető meg. A képi megjelenítés a 16.

![Fürtözés eredménye](./media/interpret-model-results/16.png)

16. ábra. A betanítási adatkészlet fürtözési eredményének megjelenítése

A második rész eredménye, az új bejegyzések fürtözése a betanított fürtözési modellel, a 17.

![A fürtözés eredményének megjelenítése](./media/interpret-model-results/17.png)

17. ábra. A fürtözés eredményének megjelenítése új adatkészleten

**Eredmény értelmezése**

Bár a két rész eredményei különböző kísérleti szakaszokból származnak, ugyanúgy néznek ki, és ugyanúgy értelmezik őket. Az első négy oszlop funkciók. Az utolsó oszlop, hozzárendelések, az előrejelzés eredménye. Az azonos számú bejegyzés előre jelzett, hogy ugyanabban a fürtben, azaz ezek hasonlóságok valamilyen módon (ez a kísérlet az alapértelmezett euklideszi távolság metrika). Mivel a fürtök számát 2-nek adta meg, a Hozzárendelések bejegyzései 0 vagy 1 címkével vannak ellátva.

**Webszolgáltatás közzététele**

A fürtözési kísérlet közzéteheti egy webszolgáltatásban, és hívja meg a fürtözési előrejelzések ugyanúgy, mint a kétosztályos besoroláshasználati esetben.

![Pontozási kísérlet írisz klaszterezési probléma](./media/interpret-model-results/18.png)

18. ábra. Íriszklaszter-probléma pontozási kísérlete

A webszolgáltatás futtatása után a visszaadott eredmény a 19. Ez a virág az előrejelzések szerint a klaszter 0.

![Tesztértelmező pontozási modul](./media/interpret-model-results/18_1.png)

![Pontozási modul eredménye](./media/interpret-model-results/19.png)

19. ábra. Kétosztályos írisz besorolás webszolgáltatás-eredménye

## <a name="recommender-system"></a>Ajánló rendszer
**Példakísérlet**

Az ajánlói rendszerek esetében példaként használhatja az éttermi ajánlási problémát: éttermeket ajánlhat az ügyfeleknek a minősítési előzmények alapján. A bemeneti adatok három részből állnak:

* Éttermi értékelések az ügyfelektől
* Ügyfélszolgáltatás adatai
* Éttermi szolgáltatás adatai

Az Azure Machine Learning Studio (klasszikus) [Train Matchbox Recommender][train-matchbox-recommender] moduljával számos dolgot tehetünk:

* Adott felhasználó és elem minősítésének előrejelzése
* Elemek ajánlása egy adott felhasználónak
* Adott felhasználóhoz kapcsolódó felhasználók keresése
* Adott cikkhez kapcsolódó elemek keresése

Az **Ajánló előrejelzési típusú** menü négy közül választhat. Itt végigsétálhat mind a négy forgatókönyvön.

![Matchbox ajánló](./media/interpret-model-results/19_1.png)

Egy tipikus Azure Machine Learning Studio (klasszikus) kísérlet egy ajánló rendszer úgy néz ki, mint a 20. Az ajánló rendszermodulok használatáról a [Train matchbox recommender][train-matchbox-recommender] and [Score matchbox recommender][score-matchbox-recommender]című témakörben talál.

![Ajánló rendszer kísérlet](./media/interpret-model-results/20.png)

20. ábra. Ajánló rendszer kísérlet

**Eredmény értelmezése**

**Adott felhasználó és elem minősítésének előrejelzése**

Az **Ajánló** **előrejelzési típusú**Minősítés előrejelzés kiválasztásával arra kéri az ajánlórendszert, hogy előre jelezje egy adott felhasználó és elem minősítését. A [Score Matchbox Recommender][score-matchbox-recommender] kimenet vizualizációja a 21.

![Az ajánlórendszer pontszámeredménye - minősítési előrejelzés](./media/interpret-model-results/21.png)

21. ábra. Vizualizálja az ajánló rendszer pontszámának eredményét - rating prediction

Az első két oszlop a bemeneti adatok által biztosított felhasználói elempárok. A harmadik oszlop egy felhasználó előre jelzett minősítése egy adott elemhez. Az első sorban például az U1048-as ügyfél az előrejelzések szerint az 135026-os éttermet 2-nek értékeli.

**Elemek ajánlása egy adott felhasználónak**

Az Ajánló előrejelzési szöveg **csoportban a Cikkajánlás** **imént**kiválasztásával arra kéri az ajánló rendszert, hogy ajánljon elemeket egy adott felhasználónak. Ebben a forgatókönyvben az utolsó választható paraméter az *Ajánlott elem kiválasztása*. A **beállítás a minősített elemek (a modell értékelése)** elsősorban a modell értékelése a betanítási folyamat során. Ebben az előrejelzési szakaszban az **Összes elem közül**választunk. A [Score Matchbox Recommender][score-matchbox-recommender] kimenet vizualizációja a 22.

![Az ajánlórendszer pontszáma - elemajánlás](./media/interpret-model-results/22.png)

22. ábra. Az ajánló rendszer pontszámának megjelenítése – elemajánlás

A hat oszlop közül az első az adott felhasználói azonosítókat jelöli, amelyekhez a bemeneti adatok által biztosított elemeket ajánlanak. A másik öt oszlop a felhasználónak ajánlott elemeket jelöli csökkenő relevanciasorrendben. Az első sorban például az U1048-as ügyfél számára a legajánlottabb étterem az 134986, amelyet 135018, 134975, 135021 és 132862 követ.

**Adott felhasználóhoz kapcsolódó felhasználók keresése**

Ha a **Kapcsolódó felhasználók** lehetőséget választja az **Ajánló előrejelzési kedvese**csoportban, arra kéri az ajánló rendszert, hogy keresse meg egy adott felhasználó hoz tartozó felhasználókat. A kapcsolódó felhasználók azok a felhasználók, akik hasonló beállításokkal rendelkeznek. Ebben a forgatókönyvben az utolsó paraméter a *Kapcsolódó felhasználó kiválasztása.* A **beállítás a felhasználóktól, amelyek eddig elemek (a modell kiértékelése)** elsősorban a modell kiértékelése a betanítási folyamat során. Válassza az Előrejelzési szakasz **minden felhasználótól** lehetőséget. A [Score Matchbox Recommender][score-matchbox-recommender] kimenet vizualizációja a 23.

![Az ajánlórendszer pontszáma - kapcsolódó felhasználók](./media/interpret-model-results/23.png)

23. ábra. Az ajánló rendszer pontszámainak megjelenítése – kapcsolódó felhasználók

A hat oszlop közül az első a kapcsolódó felhasználók kereséséhez szükséges felhasználói azonosítókat mutatja, a bemeneti adatok szerint. A másik öt oszlop a felhasználó előre jelzett kapcsolódó felhasználóit csökkenő relevanciasorrendben tárolja. Az első sorban például az U1048 vevő számára a legrelevánsabb vevő az U1051, amelyet az U1066, az U1044, az U1017 és az U1072 követ.

**Adott cikkhez kapcsolódó elemek keresése**

Ha kiválasztja **a Kapcsolódó elemek lehetőséget** az **Ajánló előrejelzési kedvesében,** akkor arra kéri az ajánló rendszert, hogy keresse meg az adott elemhez kapcsolódó elemeket. A kapcsolódó elemek azok az elemek, amelyeket a legnagyobb valószínűséggel kedvel ugyanaz a felhasználó. Ebben a forgatókönyvben az utolsó paraméter a *Kapcsolódó elem kiválasztása.* A **beállítás a minősített elemek (a modell értékelése)** elsősorban a modell értékelése a betanítási folyamat során. Az **előrejelzési szakaszhoz az Összes elem közül** választjuk. A [Score Matchbox Recommender][score-matchbox-recommender] kimenet vizualizációja a 24-es számnak tűnik.

![Az ajánlórendszer pontszáma -- kapcsolódó elemek](./media/interpret-model-results/24.png)

24. ábra. Az ajánló rendszer pontszámainak megjelenítése – kapcsolódó elemek

A hat oszlop közül az első a kapcsolódó elemek kereséséhez szükséges elemazonosítókat jelöli, a bemeneti adatok nak megfelelően. A másik öt oszlop az elem előre jelzett kapcsolódó elemeit a relevancia szempontjából csökkenő sorrendben tárolja. Az első sorban például az 135026 tétel legfontosabb tétele a 135074, amelyet 135035, 132875, 135055 és 134992 követ.

**Webszolgáltatás közzététele**

A folyamat közzététele ezeket a kísérleteket webszolgáltatásként az előrejelzések lekérni hasonló mind a négy forgatókönyv. Itt vesszük a második forgatókönyv (javasoljuk elemeket egy adott felhasználó) példaként. Ugyanezt az eljárást követheti a másik hárommal.

A betanított ajánlórendszer betanított modellként történő mentése és a bemeneti adatok egyetlen felhasználói azonosító oszlopra történő szűrése a kérésnek megfelelően, csatlakoztathatja a kísérletet, mint a 25.

![Pontozási kísérlet az éttermi ajánlás problémájáról](./media/interpret-model-results/25.png)

25. ábra. Pontozási kísérlet az éttermi ajánlás problémájáról

A webszolgáltatás futtatásakor a visszaadott eredmény a 26. Az U1048 felhasználó számára az öt ajánlott étterem: 134986, 135018, 134975, 135021 és 132862.

![Az ajánlói rendszerszolgáltatás mintája](./media/interpret-model-results/25_1.png)

![Mintakísérlet eredményei](./media/interpret-model-results/26.png)

26. ábra. Éttermi ajánlási probléma webszolgáltatás-eredménye

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
