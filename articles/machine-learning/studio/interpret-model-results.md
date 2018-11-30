---
title: A Machine Learning Studio - Azure modell eredményeinek értelmezése |} A Microsoft Docs
description: Hogyan választható ki a optimális paraméter használatával, és a vizualizáláshoz pontszám modell kimeneti algoritmus beállítása.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: c06b05989270b4199e5d97ca399a1411b73a702b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310269"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio"></a>Az Azure Machine Learning Studióban modell eredményeinek értelmezése
Ez a témakör bemutatja, hogyan jelenítheti meg, és az Azure Machine Learning Studióban az előrejelzés eredményét értelmezik. Miután a betanított modell, és végzett előrejelzések felett ("pontozását a modell"), és az előrejelzési eredmények értelmezése szüksége.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Machine learning-modellek Azure Machine Learning négy fő típusú léteznek:

* Besorolás
* Fürtözés
* Regresszió
* Ajánló rendszerek

Az ezeken a modelleken előrejelzéshez használt modulokra:

* [Pontszám modell] [ score-model] besorolási és regressziós-modulja
* [Fürtök hozzárendelése] [ assign-to-clusters] modul a fürtszolgáltatás
* [Pontszám Matchbox ajánló] [ score-matchbox-recommender] a javaslattételi rendszerek

Ez a dokumentum ismerteti az egyes modulok előrejelzési eredményeket értelmezése. Ezek a modulok áttekintését lásd: [kiválasztása az Azure Machine Learning termékben használt algoritmusok optimalizálására paraméterek](algorithm-parameters-optimize.md).

Ez a témakör címek előrejelzési értelmezését, de nem a modell értékelése. A modell értékelése kapcsolatos további információkért lásd: [az Azure Machine Learning modellek teljesítményének kiértékelése](evaluate-model-performance.md).

Ha segítségre van szüksége egy egyszerű kísérlet létrehozására első lépések, olvassa el és nem ismeri az Azure Machine Learning [egy egyszerű kísérlet létrehozása az Azure Machine Learning Studio](create-experiment.md) az Azure Machine Learning Studióban.

## <a name="classification"></a>Besorolás
Két alkategóriái osztályozási problémák:

* Csak két osztály (kétosztályos vagy bináris osztályozás) kapcsolatos problémák
* Több mint két osztály (többcsoportos osztályozás) kapcsolatos problémák

Az Azure Machine Learning rendelkezik különböző modulok besorolás az ilyen típusú foglalkozik, de az előrejelzési eredmények értelmezésében módszerei hasonlóak.

### <a name="two-class-classification"></a>Két osztályú osztályozás
**Példakísérlet**

Egy példa egy két osztályú osztályozási probléma a virágok iris besorolása. A feladat az Írisz osztályozása virágok a hozzájuk tartozó funkciók alapján. Az Azure Machine Learning biztosított az Írisz adatkészletet egy részét a népszerű [Írisz adatkészletet](http://en.wikipedia.org/wiki/Iris_flower_data_set) példányait tartalmazó csak két virágzik fajok (0 és 1. osztály). Nincsenek az egyes flower (csészelevél hossza, csészelevél szélessége, szirom hosszúsága és szirom szélessége) négy szolgáltatásai.

![Képernyőkép az iris kísérlet](./media/interpret-model-results/1.png)

1. ábra IRIS két osztályú osztályozási probléma kísérlet

Kísérlet történt a probléma megoldásához az 1. ábrán látható módon. Kétosztályos gyorsított döntési fa modell tanítva, és a pontozott. Most származó előrejelzési eredményeket jelenítheti meg a [Score Model] [ score-model] modul kimeneti portjával kattintva a [Score Model] [ score-model] a modul, majd kattintás a **Visualize**.

![Score model-modul](./media/interpret-model-results/1_1.png)

Ekkor megjelenik a pontozási eredményeinek a 2. ábrán látható módon.

![Iris két osztályú osztályozási kísérlet eredményeit](./media/interpret-model-results/2.png)

2. ábra A két osztályú osztályozási pontszám modell eredményt megjelenítése

**Eredmények értelmezése**

Nincsenek hat oszlopokat az eredmények táblázatában. A bal oldali négy oszlopot a négy funkciók. Jobb két oszlop, pontozott címkék és a pontozott valószínűség előrejelzési eredményeket is. A pontozott valószínűség az oszlopban látható annak a valószínűsége, amelyhez tartozik egy flower a pozitív osztály (1. osztály). Például az első szám az oszlop (0.028571) azt jelenti, hogy ott 0.028571 valószínűsége, hogy az első flower 1. osztályú tartozik. A pontozott címkék oszlop mutatja az egyes flower az előrejelzett osztály. Ez a pontozott valószínűség oszlop alapján. Ha egy flower pontozott valószínűségét, amelyben 0,5 nagyobb, mint 1. osztályú összegyűjtése várható. Ellenkező esetben összegyűjtése várható, osztály 0.

**Webes szolgáltatás-közzététel**

Miután előrejelzési eredményeket megértését, és hang pályamű, a kísérlet tehetők közzé webszolgáltatásként üzembe helyezni a különféle alkalmazásokat, és azt osztály alapján, bármely új iris flower történő beszerzéséhez hívja meg. Betanítási kísérlet átalakítása egy pontozó kísérletet, és közzéteheti webszolgáltatásként kapcsolatban lásd: [az Azure Machine Learning webszolgáltatás közzététele](walkthrough-5-publish-web-service.md). Ez az eljárás ismerteti egy pontozó kísérletet a 3. ábrán látható módon.

![Képernyőkép a pontozási kísérlet](./media/interpret-model-results/3.png)

3. ábra A pontozás az iris két osztályú osztályozási probléma kísérlet

Most már szükség a bemeneti és kimeneti a webszolgáltatáshoz. A bemeneti adatok jobb oldali bemeneti portjával [Score Model][score-model], azaz az Iris flower bemeneti funkciói. A kimenet a kiválasztott attól függ, hogy érdekli az előrejelzett osztály (pontozott címkéje), a pontozott valószínűség vagy mindkettőt. Ebben a példában feltételezzük, hogy Önt érdeklő is. Válassza ki a kívánt kimeneti oszlopokat, használja a [Select Columns in adatkészlet] [ select-columns] modul. Kattintson a [Select Columns in adatkészlet][select-columns], kattintson a **Oszlopválasztás indítása**, és válassza ki **pontozott címkék** és **Scored Valószínűségek**. A kimeneti portra, beállítása után [Select Columns in adatkészlet] [ select-columns] és újra futtatni, készen áll a pontozási kísérlet közzététele webszolgáltatásként kattintva **WEBSZOLGÁLTATÁS közzététele** . Az elkészült kísérletnek a következőképpen néz ki a 4. ábra.

![Az iris két osztályú osztályozási kísérlet](./media/interpret-model-results/4.png)

4. ábra Elkészült pontozási kísérlet egy iris két osztályú osztályozási probléma

Miután a webszolgáltatások futtatásához, és adja meg a teszt-példány néhány funkció értéket, az eredmény két számot adja vissza. Az első szám az pontozott címke, a második pedig a pontozott valószínűség. Ez flower 0.9655 valószínűséggel várhatóan 1. osztályúnak minősülnek.

![Pontszám modell tesztelése értelmezése](./media/interpret-model-results/4_1.png)

![Pontozó terhelésiteszt-eredményei](./media/interpret-model-results/5.png)

5. ábra Iris két osztályú osztályozási webes szolgáltatás eredménye

### <a name="multi-class-classification"></a>Többcsoportos besorolás
**Példakísérlet**

Az ehhez a kísérlethez végrehajtott feladatokat levél-felismerés többosztályos osztályozási példaként. Az osztályozó által igénybe vett próbál meg előre jelezni az egyes betű (osztály) néhány a kézírásos rendszerképek kinyert kézzel írt attribútum értékei alapján.

![Levél felismerés példa](./media/interpret-model-results/5_1.png)

A betanítási adatok nincsenek levél kézírásos rendszerképek kinyert 16 funkciókat. A 26 betűket 26 osztályból alkotnak. 6. ábra egy kísérletet, amely betűvel elismerési többosztályos osztályozási modell betanítása és előrejelezheti a tesztelési adathalmazon ugyanazokat a funkciókat jeleníti meg.

![Levél felismerés többosztályos osztályozási kísérlet](./media/interpret-model-results/6.png)

6. ábra Levél felismerés többosztályos osztályozási probléma kísérlet

Eredményeinek megjelenítése a [Score Model] [ score-model] modul kimeneti portjával kattintva [Score Model] [ score-model] modult, majd kattintás a **Visualize**, megjelenik a tartalmat a 7. ábrán látható módon.

![Pontszám modell eredményeinek](./media/interpret-model-results/7.png)

7. ábra Egy többcsoportos besorolásban pontszám modell eredményeinek képi megjelenítése

**Eredmények értelmezése**

A bal oldali 16 oszlop felel meg a szolgáltatás értékeket a teszt csoport. Az oszlopok nevekkel, pontozott valószínűség "XX" osztály csak a rendszer, például a két osztályú eset pontozott valószínűség oszlopa. Látszanak a valószínűsége annak, hogy a megfelelő bejegyzésben egy bizonyos osztályba. Ha például az első bejegyzés nincs 0.003571 valószínűsége, hogy-e egy "A," 0.000451 annak valószínűségét, a "B" és így tovább. Az utolsó oszlopban (pontozott címkék) megegyezik a pontozott címkék két osztályú esetben. A legnagyobb pontozott valószínűség az osztályt, az előrejelzett osztály a megfelelő tétel kiválasztva. Például az első bejegyzés a pontozott címke már "F", mert a legnagyobb valószínűség lennie egy "F" (0.916995) rendelkezik.

**Webes szolgáltatás-közzététel**

A pontozott címke minden egyes belépési és a pontozott címke valószínűségét is kaphat. Az alapvető logikát, hogy a legnagyobb valószínűség a pontozott valószínűség között találja. Ehhez meg kell használnia a [R-szkript végrehajtása] [ execute-r-script] modul. Az R-kód a 8. ábrán látható, és 9. ábra a kísérlet eredménye látható.

![R-kód példa](./media/interpret-model-results/8.png)

8. ábra Pontozott címkék és a kapcsolódó valószínűségek, a címkék az R-kód

![Kísérlet eredménye](./media/interpret-model-results/9.png)

9. ábra A levél-felismerés többosztályos osztályozási probléma elkészült pontozási kísérlet

Miután közzététele és a webszolgáltatások futtatásához, és adja meg az egyes bemeneti funkció értékeket, a visszaadott eredmény úgy tűnik, a 10. ábrán. A kézzel írt levél kinyert 16 funkcióihoz várhatóan meghalad egy "T" 0.9715 valószínűséggel kell.

![Teszt értelmezése pontszám modul](./media/interpret-model-results/9_1.png)

![Teszteredmény](./media/interpret-model-results/10.png)

10. ábra Többosztályos osztályozási webes szolgáltatás eredménye

## <a name="regression"></a>Regresszió
Osztályozási problémák regressziós problémák eltérnek. Osztályozási probléma, a kívánt előrejelzése diszkrét osztályok, például, hogy melyik osztály egy iris flower tartozik. De amint láthatja, a következő példában egy regressziós probléma, próbál egy folyamatos változó, például egy autó árának előrejelzése.

**Példakísérlet**

A példában a regressziós autó árának előrejelzése használjuk. Az egy autó árát a szolgáltatások, például márka, ösztönzésének típusa, típusú törzs és meghajtó kerék alapján előre jelezni kívánt. A kísérlet 11. ábra jelenik meg.

![Autó árát regressziós kísérlet](./media/interpret-model-results/11.png)

11. ábra Autó árát regressziós probléma kísérlet

Megjeleníteni a [Score Model] [ score-model] modul, az eredmény láthatóhoz hasonló 12. ábra.

![Az autókra price prediction probléma pontozási eredményeinek](./media/interpret-model-results/12.png)

12. ábra. Az autókat tartalmazó ár előrejelzési probléma pontozási eredménye

**Eredmények értelmezése**

Pontozott címkék pontozási eredmény eredményt oszlopa. A szám, a előre jelzett minden autó árát.

**Webes szolgáltatás-közzététel**

A regressziós kísérlettel webszolgáltatások közzététele, és hívja az autó árának előrejelzése ugyanúgy két osztályú osztályozási használatát is.

![Kísérlet pontozási autó árát regressziós probléma](./media/interpret-model-results/13.png)

13. ábra. Kísérlet egy autó árát regressziós probléma pontozási

A webszolgáltatást futtató, a visszaadott eredmény tűnik 14. ábra. Az előre jelzett díjszabása az autó $15,085.52.

![Teszt értelmezése pontozási modul](./media/interpret-model-results/13_1.png)

![A modul pontozási eredményeinek](./media/interpret-model-results/14.png)

14. ábra. Webes szolgáltatás eredménye egy autó árát regressziós probléma

## <a name="clustering"></a>Fürtözés
**Példakísérlet**

Használjuk az Írisz adatkészletet újra fürtözési kísérletet. Itt szűrhet az adatkészlet osztály feliratok ki, hogy csak funkciókkal rendelkezik, és a fürtszolgáltatás is használható. Az iris a használati eset, adja meg a képzés során, ami azt jelenti, hogy a virágok a két osztályba lenne fürt két fürtöket. A kísérlet 15. ábra jelenik meg.

![IRIS fürtözési probléma kísérletezéshez](./media/interpret-model-results/15.png)

15. ábra. IRIS fürtözési probléma kísérletezéshez

Fürtszolgáltatás abban különbözik a besorolást, hogy a tanítási adathalmazt önmagában nem rendelkezik földön-hiteles címkék. A betanítási adatok példányaihoz, különböző fürtök fürtözési csoportok. A betanítási folyamat során a modell által a hozzájuk tartozó funkciók eltérései tanulási bejegyzéseket feliratok. Ezt követően a betanított modell további a jövőbeli bejegyzések besorolására használható. Az eredmény, mi érdekli fürtözési probléma belül két részből áll. Az első rész a tanítási adathalmazt a címkézés és a második az Írisz a betanított modell egy új adatkészlet.

Az eredmény első része az alábbi ábra a bal oldali kimeneti portjára kattintva [Train-csoportosítási modellben] [ train-clustering-model] , és kattintás **Visualize**. A Vizualizáció 16. ábra jelenik meg.

![Fürtszolgáltatás eredménye](./media/interpret-model-results/16.png)

16. ábra. Megjelenítheti a fürtszolgáltatás a tanítási adathalmazt eredménye

A második rész, új bejegyzéseket a betanított fürtözési modell a fürtszolgáltatás eredményét 17. ábra jelenik meg.

![Fürtszolgáltatás eredmény megjelenítése](./media/interpret-model-results/17.png)

17. ábra. Fürtszolgáltatást eredménye egy új adatkészlet megjelenítése

**Eredmények értelmezése**

Bár a különböző kísérlet szakaszok vezethető vissza az eredményeket a két részből áll, azok ugyanúgy néznek, és ugyanúgy értelmezi. Az első négy oszlop olyan szolgáltatása. Az utolsó oszlopban hozzárendelések, az előrejelzési eredmények. A bejegyzések annyi hozzárendelt vannak előre meghatározott kell lennie az ugyanazon a fürtön, azaz oszthatnak meg Hasonlóságok valamilyen módon (Ez a kísérlet használja az alapértelmezett Euclidean távolság metrikát). 2 lesz a fürtök számának adott meg, mert a hozzárendelések bejegyzései vannak ellátva, 0 vagy 1.

**Webes szolgáltatás-közzététel**

A fürtözési kísérlettel webszolgáltatások közzététele, és a fürtszolgáltatás ugyanúgy mint két osztályú osztályozási használati eset előrejelzéseket meghívására.

![Kísérlet pontozási iris fürtözési probléma](./media/interpret-model-results/18.png)

18. ábra. Kísérlet egy iris fürtözési probléma pontozási

A webszolgáltatás futtatása után a visszaadott eredmény 19. ábra tűnik. Ez flower összegyűjtése várható kell lennie a fürt 0.

![Teszt pontozási modul értelmezése](./media/interpret-model-results/18_1.png)

![Pontozó modul eredménye](./media/interpret-model-results/19.png)

19. ábra. Iris két osztályú osztályozási webes szolgáltatás eredménye

## <a name="recommender-system"></a>Ajánló rendszer
**Példakísérlet**

Ajánló rendszerek esetén használhatja a éttermi javaslat probléma példa: az ügyfelek a minősítés előzményei alapján is éttermek javasolnak. A bemeneti adatok három részből áll:

* Az ügyfelektől származó éttermi minősítések
* A szolgáltatás ügyféladatok
* Éttermek a szolgáltatás adatokat

Számos dolgot mindent tehetünk a [Train Matchbox ajánló] [ train-matchbox-recommender] az Azure Machine Learning modul:

* Egy adott felhasználó és az elem minősítését előrejelzése
* Javasolhat egy adott felhasználó számára
* Egy adott felhasználóhoz kapcsolódó felhasználók keresése
* Keresse meg az adott elemhez kapcsolódó cikkek

Mit szeretne tenni kiválasztásával négy lehetőségei közül választhat a **ajánló előrejelzési típusú** menü. Itt minden négy különböző alkalmazási helyzetekben is vezeti.

![Matchbox ajánló](./media/interpret-model-results/19_1.png)

Egy tipikus Azure Machine Learning-kísérlet ajánló rendszer esetén 20. ábra tűnik. E ajánló rendszer modulok használatával kapcsolatos információkért lásd: [Train matchbox ajánló] [ train-matchbox-recommender] és [pontszám matchbox ajánló] [ score-matchbox-recommender].

![Ajánló rendszer kísérlet](./media/interpret-model-results/20.png)

20. ábra. Ajánló rendszer kísérlet

**Eredmények értelmezése**

**Egy adott felhasználó és az elem minősítését előrejelzése**

Kiválasztásával **minősítés előrejelzési** alatt **ajánló előrejelzési kind**, arra utasítja a ajánló rendszer előrejelzésére egy adott felhasználó és a cikk a minősítés. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimenet a következőhöz hasonló 21. ábra.

![A ajánló rendszer--minősítés előrejelzés eredményét pontszám](./media/interpret-model-results/21.png)

21. ábra. A ajánló rendszer--előrejelzési minősítés pontszám eredményének megjelenítése

Az első két oszlop a bemeneti adatok által biztosított felhasználói-elem párt. A harmadik oszlop az előre jelzett minősítése egy felhasználó egy bizonyos elem. Például az első sorban ügyfél U1048 várhatóan meghalad arány éttermi 135026 2.

**Javasolhat egy adott felhasználó számára**

Kiválasztásával **elem javaslat** alatt **ajánló előrejelzési kind**, még a ajánló rendszer elemek ajánlaná az adott felhasználó kéri. Választhat a forgatókönyv utolsó paraméter *elemválasztás ajánlott*. A beállítás **a minősített elemek (modell kiértékelése)** elsődlegesen a modell értékelése a betanítási folyamat során. Előrejelzési ebben a szakaszban azt válassza **az összes elem**. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimenet a következőhöz hasonló 22. ábra.

![Ajánló rendszer--elem javaslat pontszám eredménye](./media/interpret-model-results/22.png)

22. ábra. Ajánló rendszer--elem javaslat pontszám eredmény megjelenítése

Az első hat oszlop az adott felhasználói azonosítókat, tételeket jelöli, a bemeneti adatok által biztosított módon. A többi öt oszlop felel meg az elemeket, javasoljuk, hogy a felhasználó a relevancia alapján csökkenő sorrendben. Például az első sorban a leginkább ajánlott éttermi ügyfél U1048 134986, 135018, 134975, 135021 és 132862 követ.

**Egy adott felhasználóhoz kapcsolódó felhasználók keresése**

Kiválasztásával **kapcsolódó felhasználók** alatt **ajánló előrejelzési kind**, akkor van kéri a ajánló rendszer, hogy egy adott felhasználóhoz kapcsolódó felhasználók. A felhasználók, akik rendelkeznek hasonló beállítások, a kapcsolódó felhasználók. Választhat a forgatókönyv utolsó paraméter *kapcsolódó felhasználó által választott*. A beállítás **a felhasználóknak, hogy minősített elemek (modell kiértékelése)** elsődlegesen a modell értékelése a betanítási folyamat során. Válasszon **az összes felhasználó** előrejelzési ebben a szakaszban. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimenet a következőhöz hasonló 23. ábra.

![Ajánló rendszer--kapcsolódó felhasználók pontszám eredménye](./media/interpret-model-results/23.png)

23. ábra. Pontszám modell eredményeinek ajánló rendszer--kapcsolódó felhasználók megjelenítése

Az első hat oszlop mutatja az adott felhasználói azonosítók kapcsolódó felhasználók kereséséhez szükséges, mivel a bemeneti adatok által biztosított. A többi öt oszlop tárolja a felhasználó az előre jelzett kapcsolódó felhasználói szerinti relevancia alapján csökkenő sorrendben. Például az első sorban a leginkább releváns vevő ügyfél U1048 U1051, U1066, U1044, U1017 és U1072 követ.

**Keresse meg az adott elemhez kapcsolódó cikkek**

Kiválasztásával **kapcsolódó elemek** alatt **ajánló előrejelzési kind**, arra utasítja a ajánló rendszer az adott elemhez kapcsolódó elemek kereséséhez. Kapcsolódó elemek találhatók a legnagyobb valószínűséggel lesz a ugyanaz a felhasználó által nemrégiben kedvelt elemek. Választhat a forgatókönyv utolsó paraméter *elemválasztás kapcsolódó*. A beállítás **a minősített elemek (modell kiértékelése)** elsődlegesen a modell értékelése a betanítási folyamat során. Lehetőséget választjuk **az összes elem** előrejelzési ebben a szakaszban. A képi megjelenítés, a [pontszám Matchbox ajánló] [ score-matchbox-recommender] kimenet a következőhöz hasonló 24. ábra.

![Ajánló rendszer--kapcsolódó elemek pontszám eredménye](./media/interpret-model-results/24.png)

24. ábra. Pontszám modell eredményeinek ajánló rendszer--kapcsolódó elemek megjelenítése

Az első hat oszlopot a megadott elem szükséges kapcsolódó elemek kereséséhez, mivel a bemeneti adatok által biztosított azonosítók jelöli. A többi öt oszlop relevancia alapján csökkenő elem az előre jelzett kapcsolódó elemek tárolásához. Például az első sorban a leginkább releváns elem 135026 elem 135074, 135035, 132875, 135055 és 134992 követ.

**Webes szolgáltatás-közzététel**

Ezek a kísérletek közzététele webszolgáltatásként kaphatnak előjelzéseket folyamata az egyes négy forgatókönyv hasonlít. A második forgatókönyvben (egy adott felhasználó ajánlott elemek) itt példaként vesszük. Követheti a másik három ugyanazt az eljárást.

A betanított ajánló rendszermódosítások mentése másként betanított modell, és a bemeneti adatokat tartalmazó egyetlen felhasználói azonosító oszlop szűrése kért, mint 25. ábra a kísérlet kapcsolni, és közzéteheti webszolgáltatásként.

![Kísérlet a éttermi javaslat probléma pontozási](./media/interpret-model-results/25.png)

25. ábra. Kísérlet a éttermi javaslat probléma pontozási

A webszolgáltatást futtató, a visszaadott eredmény tűnik 26. ábra. Felhasználó U1048 öt ajánlott éttermekben 134986, 135018, 134975, 135021 és 132862.

![Ajánló rendszerszolgáltatás mintája](./media/interpret-model-results/25_1.png)

![Minta kísérlet eredménye](./media/interpret-model-results/26.png)

26. ábra. Webes szolgáltatás eredménye éttermi javaslat probléma

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
