---
title: Szövegelemzési modellek – az Azure Machine Learning Studio létrehozása |} A Microsoft Docs
description: Hogyan szövegelemzési modellek létrehozása az Azure Machine Learning Studióban szöveg előfeldolgozása, N-gramokat vagy a szolgáltatáskivonatolás modulok használata
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: ''
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 11f4ad4ff1e8e2eab688596d393e63009f7e5624
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255482"
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Szövegelemzési modellek létrehozása az Azure Machine Learning Studio használatával
Az Azure Machine Learning segítségével hozhat létre és szövegelemzési modelleket. Ezek a modellek, például dokumentum besorolását vagy sentiment analysis problémák megoldásában is segítenek.

A text analytics kísérletben ugyanúgy általában:

1. Tisztítása és szöveges adatkészlet előfeldolgozása
2. Bontsa ki a numerikus funkció vektorok Előfeldolgozott szövegből
3. Tanítási osztályozási vagy regressziós modell
4. Pontszám, és a modell érvényesítése
5. A modell üzembe helyezése éles környezetbe

Ez az oktatóanyag bemutatja ezeket a lépéseket, hogy végig Amazon könyv felülvizsgálatok adatkészletet használó sentiment analysis modell (lásd: Ez a tanulmány "adatainak, Bollywood, bumm-mezőket és Blenders: Tartomány betanítás a különböző vélemények osztályozásával"John Blitzer, amelyet megjelölése Dredze és Fernando Pereira; Társítás számítási Linguistics (ACL), a 2007.) Ez az adatkészlet felülvizsgálati eredmények (1-2 vagy 4 – 5) és a egy szabad formátumú szöveges áll. A célja, hogy előre jelezni a felülvizsgálati pontszám: alacsony (1 - 2) vagy a nagy (4 – 5).

A jelen oktatóanyagban tárgyalt Azure AI-katalógusban található kísérletek találhatja meg:

[Könyv felülvizsgálatok előrejelzése](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Könyv felülvizsgálatok – prediktív Kísérletté előrejelzése](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1. lépés: Tisztítása és szöveges adatkészlet előfeldolgozása
Az első lépések a kísérletet a felülvizsgálati eredmények határozhatja meg a probléma, két osztályú osztályozási kategorikus alacsony és magas gyűjtők való felosztásával. Használjuk a [metaadatainak szerkesztése](https://msdn.microsoft.com/library/azure/dn905986.aspx) és [csoport kategóriaértékeket](https://msdn.microsoft.com/library/azure/dn906014.aspx) modulok.

![Címke létrehozása](./media/text-analytics-module-tutorial/create-label.png)

Majd, hogy törölni a szövegelemzés a következőkkel [előfeldolgozása szöveg](https://msdn.microsoft.com/library/azure/mt762915.aspx) modul. A tisztítás csökkenti a lényeget a az adatkészlet, amellyel találja a legfontosabb funkciókat és a kész modell pontosságának javítása. Áll – gyakori szavakat, például az "a" vagy "a" - és a számok, különleges karaktereket, ismétlődő karaktereket, e-mail-címeket és URL-címek eltávolítjuk. Azt is kisbetűket, a szavakat lemmatize szöveg átalakítása és észlel majd változáskövető által mondat határok "|||" Előfeldolgozott szöveges szimbólum.

![Szöveg előfeldolgozása](./media/text-analytics-module-tutorial/preprocess-text.png)

Mi történik, ha szeretné használni a listán szereplő áll? A nem kötelező bemenő adatként adhat át. Akkor is használhatja az egyéni C# szintaxis reguláris kifejezést cserélje le a karakterláncrész, és törölnie szavak rész melyik: főneveket, a műveletek vagy melléknevek.

Az előfeldolgozási befejeződése után azt az adatok felosztása tanítási, és tesztelje a csoportok.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2. lépés: Bontsa ki a numerikus funkció vektorok Előfeldolgozott szövegből
A modell egy szöveges adatok, általában akkor szabad formátumú szöveges konvertálása a numerikus funkció vektorok. Ebben a példában használjuk [N-Gram típusú szolgáltatások kinyerése szövegből](https://msdn.microsoft.com/library/azure/mt762916.aspx) modul átalakítja az ilyen formátumú szöveges adatokat. Ez a modul veszi tartalmazó oszlop, elválasztó karakter elválasztott szavakat, és kiszámítja az szókészletet tartalmazó vagy N-gramokat szó, amely az adatkészlet megjelenik. Ezt követően számolja hány alkalommal minden egyes szó, vagy N-gram, minden egyes rekord jelenik meg, és hozza létre a szolgáltatás vektorok azoktól, akik száma. Ebben az oktatóanyagban azt N-Gram típusú méretének beállítása 2, így a szolgáltatás vektorok önálló szavak és a két következő szavak kombinációi tartalmazza.

![Bontsa ki az N-gramokat](./media/text-analytics-module-tutorial/extract-ngrams.png)

Mi a alkalmazni TF * N-Gram típusú súlyozást IDF (kifejezés gyakorisága inverz dokumentum Frequency) száma. Ez a megközelítés szavak, amelyek egyetlen rekordot gyakran jelennek meg, de a teljes adatkészleten ritkák súlyozást hozzáadja. Egyéb lehetőségek közé tartozik a bináris, TF, és a gráf a mérlegelni az elbírálása során.

Ezek a szolgáltatások gyakran rendelkeznek magas dimenziói. Például ha a forrásgyűjteményébe 100 000 egyedi szavakat tartalmaz, a szolgáltatás tárhely kellene 100 000 dimenziókat, vagy több N-gramokat használata. Az N-Gram típusú szolgáltatások kinyerése a modul lehetővé teszi olyan beállítási lehetőségekkel dimenzióinak csökkentése érdekében. Is ki szeretne zárni a szavak, amelyek rövid vagy hosszú, vagy túl ritka, vagy túl gyakran jelentős prediktív értékkel. Ebben az oktatóanyagban Elzárkózunk N-gramokat, amelyek kevesebb mint 5 rekordjának vagy a több mint 80 %-a rekordok jelennek meg.

Szolgáltatás kiválasztása segítségével is, csak azokat a szolgáltatásokat, amelyek a leginkább az előrejelzési cél a korrelált kiválasztása. 1000 jellemzőket KHI szolgáltatás kiválasztása használjuk. Az jobban illeszkedhet kiválasztott szavakat vagy N-gramokat jobb kinyerési N-gramokat modul kimenete kattintva tekintheti meg.

Az N-Gram típusú szolgáltatások kinyerése egy másik módszert, mint a Szolgáltatáskivonatolás modul is használhatja. Vegye figyelembe azonban, hogy [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/dn906018.aspx) nem rendelkezik beépített kijelölés funkciók vagy TF * IDF mérlegelni az elbírálása során.

## <a name="step-3-train-classification-or-regression-model"></a>3. lépés: Tanítási osztályozási vagy regressziós modell
Most már a szöveg átalakítása numerikus funkció oszlopaihoz. Az adatkészlet az előző lépésben, karakterlánc típusú oszlopokra továbbra is tartalmazza, így használjuk Select Columns in Dataset kizárja őket.

Használjuk majd [Two-Class logisztikai regressziós](https://msdn.microsoft.com/library/azure/dn905994.aspx) előre, a célt: nagyon vagy kevésbé gyakori felülvizsgálati pontszámot. Ezen a ponton a text analytics probléma átalakítása egy rendszeres besorolás a problémát. Az Azure Machine Learningben elérhető eszközök segítségével a modell továbbfejlesztése. Például kísérletezhet a különböző osztályozó eszközökkel, ismerje meg, hogy pontos eredményeket biztosít, vagy használja a hiperparaméter finomhangolása a pontosságának növelése érdekében.

![Tanítási és pontszám](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4. lépés: Pontszám, és a modell érvényesítése
Hogyan szeretné ellenőrizze, hogy a betanított modell? Azt az élelmiszer-vizsgálati pontszám, és pontossága kiértékelése. A modell megtudhatta, azonban az jobban illeszkedhet N-gramokat és a betanítási adatkészletet a súlyukat. Ezért kell használjuk, hogy a szöveg szóhasználati, és ezek súlyok amikor funkciók beolvasása a teszt adatai, a szöveg szóhasználati képest létrehozása helyett. Ezért azt kinyerése N-Gram típusú szolgáltatások modul hozzáadása a kísérlet pontozási ága képzési ágból a kimeneti szókincsből eredőket csatlakoztatása és a szöveg szóhasználati mód beállítása legyen írásvédett. Azt is tiltsa le a szűrést az N-gramokat a gyakoriság 1 példány, és legfeljebb 100 %-os minimális beállításával, és kapcsolja ki a szolgáltatás kiválasztása.

Után a szöveges oszlop, a teszt adatai a szolgáltatás numerikus oszlopok átalakítása, hogy a karakterlánc típusú oszlopokra zárja ki a képzési ág például a korábbi szakaszaiban. Használunk majd, Score Model-modul előrejelzéseket és a pontosság kiértékelheti, hogy a modell kiértékelése modul.

## <a name="step-5-deploy-the-model-to-production"></a>5. lépés: A modell üzembe helyezése éles környezetbe
A modell csaknem készen áll az éles környezetben üzembe helyezhető. Amikor üzembe webszolgáltatásként, szabad formátumú szöveges karakterlánc bemenetként vesz igénybe, és adja vissza "nagy" vagy "alacsony". egy előrejelzési A megismert N-gram szókincsből eredőket használ a funkciók és betanított logisztikai regressziós modell segítségével előrejelzést ezek a funkciók a szöveg átalakítása. 

Állítsa be a prediktív kísérletet, azt először mentse az N-gram szókincsből eredőket adatkészletként, és a betanított logisztikai regressziós modell a betanítási ágból a kísérlet. Majd a "Mentés másként" használatával hozzon létre egy prediktív kísérletet a kísérlet graph kísérlet mentse azt. A kísérlet eltávolítjuk a felosztási adatok modul és a képzési ágat. Ezután csatlakozunk a korábban mentett N-gram szóhasználatának és modell N-Gram típusú szolgáltatások kinyerése és a Score Model-modulok jelölik. Azt is eltávolítja a modell kiértékelése modul.

Azt a beszúrandó oszlopok kiválasztása az adathalmaz modul előfeldolgozása szöveg modul a címke oszlop eltávolítása előtt, és törölje a pontszám modul "Pontszám oszlop hozzáfűzése adatkészlethez" lehetőséget. Ezzel a módszerrel a webszolgáltatás nem kér a címkét próbál előre jelezni, és nem echo válasz szolgáltatások a bemeneti biztosítja.

![Prediktív Kísérletté](./media/text-analytics-module-tutorial/predictive-text.png)

Így most hozzáadtuk a kísérletet, amelyek a közzétett webes szolgáltatás, és neve a kérés-válasz vagy kötegelt végrehajtási API-k használatával.

## <a name="next-steps"></a>További lépések
Ismerje meg a szövegelemzési moduljait [MSDN-dokumentáció](https://msdn.microsoft.com/library/azure/dn905886.aspx).

