---
title: Az érzelmi elemzés használati esete
titleSuffix: ML Studio (classic) - Azure
description: Szöveges elemzési modellek létrehozása a Azure Machine Learning Studioban (klasszikus) a szöveges előfeldolgozáshoz, az N-grammhoz vagy a szolgáltatás-kivonatoláshoz használható modulok használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217885"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Hangulati elemzési modell létrehozása Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A Text Analytics-modellek létrehozásához és működővé tenni Azure Machine Learning Studio (klasszikus) is használható. Ezek a modellek, például dokumentum besorolását vagy sentiment analysis problémák megoldásában is segítenek.

A text analytics kísérletben ugyanúgy általában:

1. Tisztítása és szöveges adatkészlet előfeldolgozása
2. Bontsa ki a numerikus funkció vektorok Előfeldolgozott szövegből
3. Tanítási osztályozási vagy regressziós modell
4. Pontszám, és a modell érvényesítése
5. A modell üzembe helyezése éles környezetbe

Ebben az oktatóanyagban megtudhatja ezeket a lépéseket, ahogy az Amazon Book Reviews adatkészletet használó, a "biográfok, a Bollywood, a Boom és a Keverőgépek: tartomány-alkalmazkodás az érzelmek besorolásához" című tanulmányban talál. Mark Dredze és Fernando Pereira; A számítási nyelvészek (ACL) társítása, 2007.) Ez az adatkészlet felülvizsgálati pontszámokból (1-2 vagy 4-5) és egy szabad formátumú szövegből áll. A célja, hogy előre jelezni a felülvizsgálati pontszám: alacsony (1 - 2) vagy a nagy (4 – 5).

A jelen oktatóanyagban tárgyalt Azure AI-katalógusban található kísérletek találhatja meg:

[A könyv felülvizsgálatának előrejelzése](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Book Reviews – prediktív kísérlet](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1\. lépés: Tisztítása és szöveges adatkészlet előfeldolgozása
Az első lépések a kísérletet a felülvizsgálati eredmények határozhatja meg a probléma, két osztályú osztályozási kategorikus alacsony és magas gyűjtők való felosztásával. A [metaadatok](https://msdn.microsoft.com/library/azure/dn905986.aspx) és a [csoport kategorikus értékek](https://msdn.microsoft.com/library/azure/dn906014.aspx) modulját használjuk.

![Címke létrehozása](./media/text-analytics-module-tutorial/create-label.png)

Ezután megtisztítjuk a szöveget a [előfeldolgozási szöveg](https://msdn.microsoft.com/library/azure/mt762915.aspx) modul használatával. A tisztítás csökkenti a lényeget a az adatkészlet, amellyel találja a legfontosabb funkciókat és a kész modell pontosságának javítása. Áll – gyakori szavakat, például az "a" vagy "a" - és a számok, különleges karaktereket, ismétlődő karaktereket, e-mail-címeket és URL-címek eltávolítjuk. Azt is kisbetűket, a szavakat lemmatize szöveg átalakítása és észlel majd változáskövető által mondat határok "|||" Előfeldolgozott szöveges szimbólum.

![Szöveg előfeldolgozása](./media/text-analytics-module-tutorial/preprocess-text.png)

Mi történik, ha szeretné használni a listán szereplő áll? A nem kötelező bemenő adatként adhat át. Akkor is használhatja az egyéni C# szintaxis reguláris kifejezést cserélje le a karakterláncrész, és törölnie szavak rész melyik: főneveket, a műveletek vagy melléknevek.

Az előfeldolgozási befejeződése után azt az adatok felosztása tanítási, és tesztelje a csoportok.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2\. lépés: A numerikus funkció vektorok kinyerése Előfeldolgozott szöveg
A modell egy szöveges adatok, általában akkor szabad formátumú szöveges konvertálása a numerikus funkció vektorok. Ebben a példában a szöveges modulból [kinyert N-Gram-funkciókat](https://msdn.microsoft.com/library/azure/mt762916.aspx) használjuk a szöveges adatok ilyen formátumba való átalakításához. Ez a modul veszi tartalmazó oszlop, elválasztó karakter elválasztott szavakat, és kiszámítja az szókészletet tartalmazó vagy N-gramokat szó, amely az adatkészlet megjelenik. Ezt követően számolja hány alkalommal minden egyes szó, vagy N-gram, minden egyes rekord jelenik meg, és hozza létre a szolgáltatás vektorok azoktól, akik száma. Ebben az oktatóanyagban azt N-Gram típusú méretének beállítása 2, így a szolgáltatás vektorok önálló szavak és a két következő szavak kombinációi tartalmazza.

![Bontsa ki az N-gramokat](./media/text-analytics-module-tutorial/extract-ngrams.png)

Mi a alkalmazni TF * N-Gram típusú súlyozást IDF (kifejezés gyakorisága inverz dokumentum Frequency) száma. Ez a megközelítés szavak, amelyek egyetlen rekordot gyakran jelennek meg, de a teljes adatkészleten ritkák súlyozást hozzáadja. Egyéb lehetőségek közé tartozik a bináris, TF, és a gráf a mérlegelni az elbírálása során.

Ezek a szolgáltatások gyakran rendelkeznek magas dimenziói. Például ha a forrásgyűjteményébe 100 000 egyedi szavakat tartalmaz, a szolgáltatás tárhely kellene 100 000 dimenziókat, vagy több N-gramokat használata. Az N-Gram típusú szolgáltatások kinyerése a modul lehetővé teszi olyan beállítási lehetőségekkel dimenzióinak csökkentése érdekében. Is ki szeretne zárni a szavak, amelyek rövid vagy hosszú, vagy túl ritka, vagy túl gyakran jelentős prediktív értékkel. Ebben az oktatóanyagban Elzárkózunk N-gramokat, amelyek kevesebb mint 5 rekordjának vagy a több mint 80 %-a rekordok jelennek meg.

Szolgáltatás kiválasztása segítségével is, csak azokat a szolgáltatásokat, amelyek a leginkább az előrejelzési cél a korrelált kiválasztása. 1000 jellemzőket KHI szolgáltatás kiválasztása használjuk. Az jobban illeszkedhet kiválasztott szavakat vagy N-gramokat jobb kinyerési N-gramokat modul kimenete kattintva tekintheti meg.

Az N-Gram típusú szolgáltatások kinyerése egy másik módszert, mint a Szolgáltatáskivonatolás modul is használhatja. Vegye figyelembe, hogy a [szolgáltatás kivonatolása](https://msdn.microsoft.com/library/azure/dn906018.aspx) nem rendelkezik beépített funkció-kiválasztási képességekkel vagy TF * IDF-súlyozással.

## <a name="step-3-train-classification-or-regression-model"></a>3\. lépés: Besorolási vagy regressziós modell betanításához.
Most már a szöveg átalakítása numerikus funkció oszlopaihoz. Az adatkészlet az előző lépésben, karakterlánc típusú oszlopokra továbbra is tartalmazza, így használjuk Select Columns in Dataset kizárja őket.

Ezután [kétosztályos logisztikai regressziót](https://msdn.microsoft.com/library/azure/dn905994.aspx) használunk, hogy megjósoljuk a célunkat: magas vagy alacsony felülvizsgálati pontszám. Ezen a ponton a text analytics probléma átalakítása egy rendszeres besorolás a problémát. A modell javítása érdekében a Azure Machine Learning Studio (klasszikus) eszközben elérhető eszközöket is használhatja. Például kísérletezhet a különböző osztályozó eszközökkel, ismerje meg, hogy pontos eredményeket biztosít, vagy használja a hiperparaméter finomhangolása a pontosságának növelése érdekében.

![Tanítási és pontszám](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4\. lépés: Pontszám, és a modell érvényesítése
Hogyan szeretné ellenőrizze, hogy a betanított modell? Azt az élelmiszer-vizsgálati pontszám, és pontossága kiértékelése. A modell megtudhatta, azonban az jobban illeszkedhet N-gramokat és a betanítási adatkészletet a súlyukat. Ezért kell használjuk, hogy a szöveg szóhasználati, és ezek súlyok amikor funkciók beolvasása a teszt adatai, a szöveg szóhasználati képest létrehozása helyett. Ezért azt kinyerése N-Gram típusú szolgáltatások modul hozzáadása a kísérlet pontozási ága képzési ágból a kimeneti szókincsből eredőket csatlakoztatása és a szöveg szóhasználati mód beállítása legyen írásvédett. Azt is tiltsa le a szűrést az N-gramokat a gyakoriság 1 példány, és legfeljebb 100 %-os minimális beállításával, és kapcsolja ki a szolgáltatás kiválasztása.

Után a szöveges oszlop, a teszt adatai a szolgáltatás numerikus oszlopok átalakítása, hogy a karakterlánc típusú oszlopokra zárja ki a képzési ág például a korábbi szakaszaiban. Használunk majd, Score Model-modul előrejelzéseket és a pontosság kiértékelheti, hogy a modell kiértékelése modul.

## <a name="step-5-deploy-the-model-to-production"></a>5\. lépés: A modell üzembe helyezése éles környezetben
A modell csaknem készen áll az éles környezetben üzembe helyezhető. Amikor üzembe webszolgáltatásként, szabad formátumú szöveges karakterlánc bemenetként vesz igénybe, és adja vissza "nagy" vagy "alacsony". egy előrejelzési A megismert N-gram szókincsből eredőket használ a funkciók és betanított logisztikai regressziós modell segítségével előrejelzést ezek a funkciók a szöveg átalakítása. 

Állítsa be a prediktív kísérletet, azt először mentse az N-gram szókincsből eredőket adatkészletként, és a betanított logisztikai regressziós modell a betanítási ágból a kísérlet. Majd a "Mentés másként" használatával hozzon létre egy prediktív kísérletet a kísérlet graph kísérlet mentse azt. A kísérlet eltávolítjuk a felosztási adatok modul és a képzési ágat. Ezután csatlakozunk a korábban mentett N-gram szóhasználatának és modell N-Gram típusú szolgáltatások kinyerése és a Score Model-modulok jelölik. Azt is eltávolítja a modell kiértékelése modul.

Azt a beszúrandó oszlopok kiválasztása az adathalmaz modul előfeldolgozása szöveg modul a címke oszlop eltávolítása előtt, és törölje a pontszám modul "Pontszám oszlop hozzáfűzése adatkészlethez" lehetőséget. Ezzel a módszerrel a webszolgáltatás nem kér a címkét próbál előre jelezni, és nem echo válasz szolgáltatások a bemeneti biztosítja.

![Prediktív Kísérletté](./media/text-analytics-module-tutorial/predictive-text.png)

Így most hozzáadtuk a kísérletet, amelyek a közzétett webes szolgáltatás, és neve a kérés-válasz vagy kötegelt végrehajtási API-k használatával.

## <a name="next-steps"></a>További lépések
Ismerje meg az [MSDN-dokumentációból](https://msdn.microsoft.com/library/azure/dn905886.aspx)származó Text Analytics-modulokat.

