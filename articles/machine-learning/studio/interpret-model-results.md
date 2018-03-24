---
title: A gépi tanulási modell eredmények értelmezését |} Microsoft Docs
description: Válassza ki az optimális paraméterkészlet, az algoritmus használatával, és pontszám modell kimeneti megjelenítése módjáról.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: e8f6d3accc7ad8de1161f08cceb0e856cf764255
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Az Azure Machine Learning modell eredmények értelmezését
Ez a témakör azt ismerteti, hogyan jelenítheti meg, és az Azure Machine Learning Studióban előrejelzés eredmények értelmezéséhez. A modellek betanítása és előrejelzéseket utasítást ("pontozza a mennyiségeket a modell") történik, után kell megértéséhez, valamint az előrejelzés eredmény értelmezhetők.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Gépi tanulási modellek az Azure Machine Learning négy fő típusú léteznek:

* Besorolás
* Fürtözés
* Regressziós
* Ajánló rendszerek

Az előrejelzés fölött ezek a modellek használt modulokra:

* [Pontszám modell] [ score-model] besorolás és regressziós modul
* [Fürtök hozzárendelése] [ assign-to-clusters] modul a fürtszolgáltatás
* [Pontszám Matchbox ajánló] [ score-matchbox-recommender] javaslat rendszerekhez

Ez a dokumentum ismerteti az egyes modulok előrejelzés eredmények értelmezése. Ezek a modulok áttekintését lásd: [paramétereit, és optimalizálja a az Azure Machine Learning algoritmus kiválasztása](algorithm-parameters-optimize.md).

Ez a témakör megoldja az előrejelzés értelmezése, de nem a modell kiértékelése. A modell kiértékelése kapcsolatos további információkért lásd: [kiértékelheti az Azure Machine Learning modell teljesítmény hogyan](evaluate-model-performance.md).

Ha segítségre van szüksége a kezdéshez, lásd: egy egyszerű kísérlet létrehozására és új Azure Machine Learning [egy egyszerű kísérlet létrehozása az Azure Machine Learning Studio](create-experiment.md) az Azure Machine Learning Studióban.

## <a name="classification"></a>Besorolás
Két alkategóriái besorolás problémák:

* Csak két osztály (két osztályú vagy bináris osztályozás) kapcsolatos problémák
* Több mint két osztály (több osztályú osztályozás) kapcsolatos problémák

Az Azure Machine Learning különböző modul az egyes típusaival besorolás kezelésére van, de az előrejelzés eredmények értelmezéséhez módszerei hasonlóak.

### <a name="two-class-classification"></a>Két osztályú osztályozás
**Példa kísérlet**

Példa egy két osztályú osztályozási problémához: iris virág besorolását. A feladat besorolása alapján a hozzájuk tartozó funkciók áttekintése iris virág. A megadott Azure Machine Learning Iris adatkészlet része a népszerű [Iris adatkészlet](http://en.wikipedia.org/wiki/Iris_flower_data_set) példányait tartalmazó csak két virágzik felhasználójától (0 és 1 osztály). Nincsenek minden virág (sepal hossza, sepal szélessége, szirom hossza és szirom szélessége) négy funkciói.

![Képernyőkép a iris kísérlet](./media/interpret-model-results/1.png)

1. ábra. IRIS két osztályú osztályozás probléma kísérlet

A kísérlet elvégzése a probléma elhárítása érdekében az 1. ábrán látható módon. A két osztályú súlyozott döntési fa modell betanítása, és a program pontozza a mennyiségeket. Most előrejelzési eredményeivel jelenítheti meg a [Score Model] [ score-model] modul a kimeneti portjára kattintva a [Score Model] [ score-model] modult, majd kattintson **Visualize**.

![Score model-modul](./media/interpret-model-results/1_1.png)

Ekkor megjelenik a pontozási eredményei a 2. ábrán látható módon.

![Iris két osztályú osztályozás kísérlet eredményei](./media/interpret-model-results/2.png)

2. ábra Két osztályú osztályozás pontszám modell eredményt megjelenítése

**Eredmények értelmezése**

Az eredmények táblázatában hat oszlopok szerepelnek. A bal oldali négy oszlopot a négy funkciók. A jobb oldali két oszlop, pontozott címkék és a program pontozza a mennyiségeket valószínűség az előrejelzés eredmények. A program pontozza a mennyiségeket valószínűség az oszlopban látható a valószínűsége annak, hogy a virág az (osztály % 1) pozitív osztályhoz tartozik. Például az első az oszlop (0.028571) azt jelenti, hogy nincs értéke 0.028571 valószínűsége, hogy az első virág osztály 1 tartozik. A pontozott címkék oszlop mutatja az egyes virág előre jelzett osztálya. A program pontozza a mennyiségeket valószínűség oszlop alapul. Ha egy virág pontozott valószínűségét 0,5-nél nagyobb, mint osztály 1 előre jelezni. Ellenkező esetben azt várhatóan mint osztály 0.

**Webes szolgáltatás-közzététel**

Miután az előrejelzés eredmények megértettem, és hang ítélt, a kísérlet tehetők közzé egy webszolgáltatás, hogy telepítse a különböző alkalmazások és neki osztály előrejelzéseket bármely új iris virág az beszerzése. A tanítási kísérletet váltson át egy pontozási kísérletet, és közzéteheti webszolgáltatásként, lásd: [közzététele az Azure Machine Learning webszolgáltatásba](walkthrough-5-publish-web-service.md). Ez az eljárás ismerteti a pontozási kísérlet a 3. ábrán látható módon.

![Képernyőkép a pontozási kísérlet](./media/interpret-model-results/3.png)

3. ábra. A iris két osztályú osztályozás probléma kísérlet pontozási

Most be kell a bemeneti és kimeneti a webszolgáltatáshoz. A bemeneti érték a jobb oldali bemeneti portját a [Score Model][score-model], ez az a Iris virág szolgáltatások bemeneti. A kimenet a választott attól függ, hogy érdekli az előre jelzett osztály (pontozott címke) és a pontozott valószínűség tartozhatnak. Ebben a példában feltételezzük, hogy érdekli, mindkét. A kívánt kimeneti oszlopok kiválasztásához használja a [oszlopok kiválasztása az adathalmaz] [ select-columns] modul. Kattintson a [oszlopok kiválasztása az adathalmaz][select-columns], kattintson a **Oszlopválasztás**, és válassza ki **pontozott címkék** és **program pontozza a mennyiségeket valószínűség**. Miután beállította a kimeneti portjára [oszlopok kiválasztása az adathalmaz] [ select-columns] és újra futtatni, akkor készen fog állni a pontozási kísérlet közzéteheti webszolgáltatásként kattintva **WEBSZOLGÁLTATÁS közzététele**. Az elkészült kísérletnek a következőképpen néz 4. ábra.

![A iris két osztályú osztályozás kísérlet](./media/interpret-model-results/4.png)

4. ábra. Egy iris két osztályú osztályozási problémához végső pontozási kísérlet

A webszolgáltatás futtatása, és adja meg a teszt-példány néhány funkció érték, után az eredmény két számot adja vissza. Az első szám a pontozott címke, a második pedig a pontozott valószínűség. A virág 0.9655 valószínűséggel várhatóan osztály 1.

![Teszt értelmezése pontszám modell](./media/interpret-model-results/4_1.png)

![A pontozási teszteredmények](./media/interpret-model-results/5.png)

5. ábra. Webes szolgáltatás eredmény iris két osztályú osztályozás

### <a name="multi-class-classification"></a>Több osztály besorolás
**Példa kísérlet**

A kísérletben multiclass besorolás példa betűnek-felismerési feladat végrehajtása. A osztályozó megkísérli bizonyos betűvel (osztály) bizonyos kezű attribútumértékei alapján a kézzel írott képek kinyert előre jelezni.

![Felismerés példa betűnek](./media/interpret-model-results/5_1.png)

A betanítási adatok nincsenek kézzel írott levél képek kinyert 16 szolgáltatásokat. A 26 betűk a 26 osztályok alkotják. 6. ábra, amelyek a vonatkozó felszólítás multiclass besorolási modell betanításához és előre jelezni a kísérlet ugyanaz a funkció be adatok TesztKészlet mutatja be.

![Levél felismerés multiclass besorolás kísérlet](./media/interpret-model-results/6.png)

6. ábra. Levél felismerés multiclass besorolás probléma kísérlet

Származó eredmények megjelenítése a [Score Model] [ score-model] a kimeneti portjára kattintva modul [Score Model] [ score-model] modult, majd kattintson **Visualize**, láthatja a tartalmat a 7. ábrán látható módon.

![Pontszám modell eredmények](./media/interpret-model-results/7.png)

7. ábra. A többszörös osztály besorolást pontszám modell eredményeinek képi megjelenítése

**Eredmények értelmezése**

A bal oldali 16 oszlop felel meg a TesztKészlet szolgáltatás értékeit. Az oszlopok nevét, például a program pontozza a mennyiségeket valószínűség "XX" osztály csak vannak, például a két osztályú esetben a program pontozza a mennyiségeket valószínűség oszlop. A valószínűségi mutatnak, amelyek a megfelelő bejegyzésben beleesik egy adott osztály. Például az első bejegyzés nincs 0.003571 valószínűsége, hogy a rendszer egy "A" 0.000451 annak valószínűségét, a "B", és így tovább. Az utolsó oszlopban (pontozott címkék) megegyezik a pontozott címkék a két osztályú esetében. Az osztály az a legnagyobb pontozott valószínűség szerint a megfelelő bejegyzés az előre jelzett osztály választja ki. Például az első bejegyzés a pontozott címkéje "F" mivel a legnagyobb valószínűség kell lennie egy "F" (0.916995) rendelkezik.

**Webes szolgáltatás-közzététel**

A pontozott címke beolvasása minden egyes belépési és a pontozott címke valószínűségét. Az alapvető logikát, hogy a legnagyobb valószínűség között a pontozott valószínűség megtalálja. Ehhez kell használnia a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Az R-kód a 8. ábrán látható, és a 9. ábra a kísérlet eredménye látható.

![R-példakód](./media/interpret-model-results/8.png)

8. ábra. Pontozott címkék és a feliratok társított valószínűség kibontott R-kód

![Kísérlet eredménye](./media/interpret-model-results/9.png)

9. ábra. A levél-felismerési multiclass osztályozási problémához végső pontozási kísérlet

Miután közzététele, és futtassa a webszolgáltatás, és adja meg a néhány bemeneti szolgáltatás érték 10. ábra visszaadott eredmény tűnik. A kézzel írott levél a kibontott 16 szolgáltatásai várhatóan 0.9715 valószínűséggel "ma" lehet.

![Pontszám modul értelmezése](./media/interpret-model-results/9_1.png)

![Tesztelési eredménye](./media/interpret-model-results/10.png)

10. ábra. Webes szolgáltatás eredmény multiclass besorolás

## <a name="regression"></a>Regressziós
Visszavonási besorolás problémák eltérnek. Egy osztályozási problémához, a kívánt előre jelezni diszkrét osztályok, például, hogy melyik osztály egy iris virág tartozik. De mint regressziós probléma az alábbi példában látható, próbál-e egy folyamatos változóval, például egy autó árának előrejelzése.

**Példa kísérlet**

A példa autó árának előrejelzése regressziós használni. A funkciót, beleértve a ellenőrizze, a üzemanyag típusa, a törzs típusa és a meghajtó kerék alapján egy autó árára kívánt. A kísérlet 11. ábra jelenik meg.

![Autó árának regressziós kísérlet](./media/interpret-model-results/11.png)

11. ábra. Autó árának regressziós probléma kísérlet

Megjeleníteni a [Score Model] [ score-model] modul, az eredmény a következőképpen néz 12. ábra.

![Autó árának előrejelzése probléma pontozási eredményei](./media/interpret-model-results/12.png)

12. ábra. A autó árának előrejelzése probléma pontértéket

**Eredmények értelmezése**

Pontozott címkék pontozási ennek eredménye oszlopa. A számok a előre jelzett minden autó árát.

**Webes szolgáltatás-közzététel**

A regressziós kísérlet be egy webszolgáltatás-bővítmény közzététele, és nevezze el az autó árának előrejelzése a két osztályú osztályozás használata gazdabuszadaptereken azonos módon.

![Kísérlet pontozás autó árának regressziós probléma](./media/interpret-model-results/13.png)

13. ábra. Kísérlet egy autó árának regressziós probléma pontozási

A webszolgáltatást futtató, a visszaadott eredmény néz 14. ábra. Az előre jelzett árat a car $15,085.52.

![Értelmezése pontozási modul](./media/interpret-model-results/13_1.png)

![A modul pontozási eredményei](./media/interpret-model-results/14.png)

14. ábra. Webes szolgáltatás eredménye egy autó árának regressziós probléma

## <a name="clustering"></a>Fürtözés
**Példa kísérlet**

Most a Iris adatkészlet újra készítéséhez használja egy csoportosítási kísérletet. Itt végezhet el a következő adatkészletben osztály feliratok, hogy csak lehetőséggel rendelkezik, és nem használható csoportosítási. Az ezen iris eset használatához adja meg a kettő, a képzési során, ami azt jelenti, akkor fürtön a virág, két osztályba számát. A kísérlet 15. ábrán látható.

![IRIS fürtözési probléma kísérletezhet](./media/interpret-model-results/15.png)

15. ábra. IRIS fürtözési probléma kísérletezhet

Fürtszolgáltatás eltér az osztályozási abban, hogy a tanítási adathalmazt önmagában nem rendelkezik ground-igazság címkék. Fürtszolgáltatás csoportok a betanítási készlet példányok különálló csoportokba. A képzés során a modell felirataihoz, ha a bejegyzések által tanulási a szolgáltatások közötti különbségeket. Ezt követően a betanított modell segítségével további osztályozására jövőbeli bejegyzéseket. Az eredmény azt is fürtözési probléma belül két részből áll. Az első rész van címkézését a tanítási adathalmazt, és a második van zárolásának betanított modell egy új adatkészlet.

Az eredmény első része is ábrázolt, kattintson a bal oldali kimeneti portjára [Train-csoportosítási modellben] [ train-clustering-model] majd **Visualize**. A képi megjelenítés 16. ábra jelenik meg.

![Fürtszolgáltatás eredménye](./media/interpret-model-results/16.png)

16. ábra. Megjelenítheti a fürtszolgáltatás a tanítási adathalmazt eredménye

A második rész a fürtszolgáltatás új bejegyzések fürtözési betanított modell esetén az eredmény 17. ábra jelenik meg.

![Fürtszolgáltatás eredmény megjelenítése](./media/interpret-model-results/17.png)

17. ábra. Csoportosítás eredménye egy új adatkészlet megjelenítése

**Eredmények értelmezése**

Bár az eredmények a két részből áll másik kísérlet szakaszból erednek, azokat is ugyanúgy néznek ki, és ugyanúgy értelmezi. Az első négy oszlop olyan szolgáltatása. Az utolsó oszlop-hozzárendelés, az előrejelzési eredménye. A hozzárendelt ugyanannyi bejegyzések vannak előre jelezni kell lennie az ugyanabban a fürtben, ez azt jelenti, hogy a közös Hasonlóságok (ehhez a kísérlethez használja az alapértelmezett Euclidean távolság metrika) valamilyen módon. A 2-nek számát adott meg, mert hozzárendelések bejegyzései tartalma, 0 vagy 1.

**Webes szolgáltatás-közzététel**

A csoportosítási kísérlet be egy webszolgáltatás-bővítmény közzététele, és neki előrejelzéseket, a megszokott módon a két osztályú osztályozás nagybetűhasználattal fürtözéshez.

![Kísérlet pontozás iris fürtözési probléma](./media/interpret-model-results/18.png)

18. ábra. Kísérlet egy iris fürtözési probléma pontozási

A webszolgáltatás futtatása után a visszaadott eredmény 19. ábra tűnik. A virág várhatóan fürt 0 lehet.

![Teszt értelmezhetők pontozási modul](./media/interpret-model-results/18_1.png)

![A pontozási modul eredménye](./media/interpret-model-results/19.png)

19. ábra. Webes szolgáltatás eredmény iris két osztályú osztályozás

## <a name="recommender-system"></a>Ajánló rendszer
**Példa kísérlet**

Ajánló rendszerek esetén használhatja a éttermi javaslat probléma példaként: is javasoljuk éttermekben, az ügyfelek a minősítési előzményei alapján. A bemeneti adatok három részből áll:

* Az ügyfelektől éttermi minősítése
* A szolgáltatás ügyféladatok
* Éttermi szolgáltatás adatok

Számos módon teheti azt a [vonat Matchbox ajánló] [ train-matchbox-recommender] az Azure Machine Learning modulban:

* Egy adott felhasználó és a cikk minősítését előrejelzése
* Elemek javasolt egy adott felhasználó számára
* Egy adott felhasználóhoz kapcsolódó felhasználók keresése
* Egy adott elemre vonatkozó elemeinek megkeresése

Választhat, hogy mit kíván tenni a négy beállítás kijelölésével a **ajánló előrejelzés jellegű** menü. Itt, végezze el az összes négy forgatókönyv.

![Matchbox ajánló](./media/interpret-model-results/19_1.png)

Egy tipikus Azure Machine Learning kísérletet ajánló rendszer esetén a következőképpen néz 20. ábra. E ajánló rendszer modulok használatával kapcsolatos információkért lásd: [vonat matchbox ajánló] [ train-matchbox-recommender] és [pontszám matchbox ajánló][score-matchbox-recommender].

![Ajánló rendszer kísérlet](./media/interpret-model-results/20.png)

20. ábra. Ajánló rendszer kísérlet

**Eredmények értelmezése**

**Egy adott felhasználó és a cikk minősítését előrejelzése**

Kiválasztásával **minősítés előrejelzés** alatt **ajánló előrejelzés jellegű**, arra utasítja a ajánló rendszer előre jelezni az egy adott felhasználó és a cikk besorolását. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimeneti néz 21. ábra.

![Pontszám eredmény ajánló rendszer – előrejelzés minősítés](./media/interpret-model-results/21.png)

21. ábra. A ajánló rendszer – előrejelzés minősítés pontszám eredményének megjelenítése

Az első két oszlop a bemeneti adatok által biztosított felhasználói-elem párok. A harmadik oszlop egy olyan felhasználó, az egyes elem előre jelzett minősítése. Például az első sorban ügyfél U1048 várhatóan arány éttermi 135026 2.

**Elemek javasolt egy adott felhasználó számára**

Kiválasztásával **elem ajánlás** alatt **ajánló előrejelzés jellegű**, a ajánló rendszer számára egy adott felhasználó elemek javasolt most kéri. Az utolsó paraméter kiválasztása ebben a forgatókönyvben *ajánlott a kiválasztott elemeket*. A beállítás **az értékelés elemét (modell kiértékelése)** elsősorban a modell kiértékelése a képzés során. Az előrejelzés szakaszra választjuk **az összes elemet**. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimeneti néz 22. ábra.

![Ajánló rendszer--elem javaslat pontszám eredménye](./media/interpret-model-results/22.png)

22. ábra. A ajánló rendszer--elem javaslat pontszám eredményét megjelenítése

Az első hat oszlopok jelöli az adott felhasználói azonosítók elemét, javasoljuk, ahogy az a bemeneti adatok. A más öt oszlop felel meg az elemeket, javasoljuk, hogy a felhasználó relevanciájának csökkenő sorrendben. Az első sorban, például a leginkább ajánlott éttermi ügyfél U1048 134986 135018, 134975, 135021 és 132862 követ.

**Egy adott felhasználóhoz kapcsolódó felhasználók keresése**

Kiválasztásával **kapcsolódó felhasználók** alatt **ajánló előrejelzés jellegű**, egy adott felhasználóhoz kapcsolódó felhasználók kereséséhez a ajánló rendszer éppen kéri. Kapcsolódó felhasználók hasonló beállítások rendelkező felhasználók. Az utolsó paraméter kiválasztása ebben a forgatókönyvben *kapcsolatos felhasználói kijelöléskor*. A beállítás **a felhasználókat, hogy besorolású elemét (modell kiértékelése)** elsősorban a modell kiértékelése a képzés során. Válasszon **az összes felhasználó** az előrejelzés. szakaszra vonatkozóan. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimeneti néz 23. ábra.

![Ajánló rendszer--kapcsolódó felhasználók pontszám eredménye](./media/interpret-model-results/23.png)

23. ábra. A ajánló rendszer--kapcsolódó felhasználók pontszám eredményeinek képi megjelenítése

Az első hat oszlop mutatja az adott felhasználói azonosítók kapcsolódó felhasználók kereséséhez szükséges, mivel a megadott bemeneti adatok. A többi öt oszlop csökkenő sorrendben fontos a felhasználó az előre jelzett kapcsolódó felhasználók tárolja. Például az első sorban, a legfontosabb felhasználói ügyfél U1048 U1051 U1066, U1044, U1017 és U1072 követ.

**Egy adott elemre vonatkozó elemeinek megkeresése**

Kiválasztásával **kapcsolódó elemek** alatt **ajánló előrejelzés jellegű**, arra utasítja a ajánló rendszer egy adott elemhez kapcsolódó elemek kereséséhez. Kapcsolódó elemek még a legnagyobb valószínűség lehet ugyanaz a felhasználó által kedvelt elemek. Az utolsó paraméter kiválasztása ebben a forgatókönyvben *kapcsolódó elem kijelölés*. A beállítás **az értékelés elemét (modell kiértékelése)** elsősorban a modell kiértékelése a képzés során. Választjuk **az összes elemet** az előrejelzés. szakaszra vonatkozóan. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimeneti néz 24. ábra.

![Pontszám eredmény ajánló rendszer--kapcsolódó elemek](./media/interpret-model-results/24.png)

24. ábra. Pontszám modell eredményeinek ajánló rendszer--kapcsolódó elemek megjelenítése

Az első hat oszlopot a megadott elem szükséges a kapcsolódó elemek kereséséhez, mivel a bemeneti adatok által biztosított azonosítók jelöli. A többi öt oszlop csökkenő sorrendben relevanciájának tekintetében a cikk az előre jelzett kapcsolódó elemek tárolására. Például az első sorban, a legfontosabb elem 135026 elem 135074 135035, 132875, 135055 és 134992 követ.

**Webes szolgáltatás-közzététel**

Ezek a kísérletek közzétételi webszolgáltatásként előrejelzéseket eléréséhez a folyamat hasonlít az egyes négy forgatókönyv. A második forgatókönyv (az adott felhasználó ajánlott elemek) itt példaként vesszük. A másik három ugyanezt az eljárást is hajtsa végre.

A betanított ajánló rendszer mentése modell betanítását, és egy felhasználói azonosító oszlop a bemeneti adatok szűrése, kért, mint 25. ábra a kísérlet a számítógéphez, és közzéteheti webszolgáltatásként.

![Kísérlet a éttermi javaslat probléma pontozási](./media/interpret-model-results/25.png)

25. ábra. Kísérlet a éttermi javaslat probléma pontozási

A webszolgáltatást futtató, a visszaadott eredmény néz 26. ábra. A felhasználó U1048 öt ajánlott éttermekben 134986, 135018, 134975, 135021 és 132862.

![Ajánló rendszerszolgáltatás minta](./media/interpret-model-results/25_1.png)

![A minta kísérlet eredménye](./media/interpret-model-results/26.png)

26. ábra. Webes szolgáltatás eredménye éttermi javaslat probléma

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
