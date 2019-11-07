---
title: Az érzelmi elemzés használati esete
titleSuffix: ML Studio (classic) Azure
description: Szöveges elemzési modellek létrehozása a Azure Machine Learning Studioban (klasszikus) a szöveges előfeldolgozáshoz, az N-grammhoz vagy a szolgáltatás-kivonatoláshoz használható modulok használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 63c1d19f1ab44fa56893c17edd78aeaf54a17314
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670701"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Hangulati elemzési modell létrehozása Azure Machine Learning Studio (klasszikus)

A Text Analytics-modellek létrehozásához és működővé tenni Azure Machine Learning Studio (klasszikus) is használható. Ezek a modellek segítenek megoldani például a dokumentumok besorolását vagy az érzelmek elemzésével kapcsolatos problémákat.

Egy szöveges elemzési kísérletben általában a következőkre lesz szüksége:

1. Szöveges adatkészlet tisztítása és előfeldolgozása
2. Numerikus funkciós vektorok kinyerése előre feldolgozott szövegből
3. Betanítási besorolás vagy regressziós modell
4. A modell pontszáma és ellenőrzése
5. A modell üzembe helyezése éles környezetben

Ebben az oktatóanyagban megtudhatja ezeket a lépéseket, ahogy az Amazon Book Reviews adatkészletet használó, a "biográfok, a Bollywood, a Boom és a Keverőgépek: tartomány-alkalmazkodás az érzelmek besorolásához" című tanulmányban talál. Mark Dredze és Fernando Pereira; A számítási nyelvészek (ACL) társítása, 2007.) Ez az adatkészlet felülvizsgálati pontszámokból (1-2 vagy 4-5) és egy szabad formátumú szövegből áll. A cél az, hogy előre megjósolja a felülvizsgálati pontszámot: alacsony (1-2) vagy magas (4-5).

A jelen oktatóanyagban ismertetett kísérletek a következő címen találhatók: Azure AI Gallery:

[A könyv felülvizsgálatának előrejelzése](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Book Reviews – prediktív kísérlet](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1\. lépés: szöveges adatkészlet tisztítása és előfeldolgozása
A kísérletet úgy kezdjük el, hogy a felülvizsgálati pontszámokat kategorikus alacsony és magas gyűjtőre osztja, hogy a probléma a kétosztályos besorolással legyen kialakítva. A [metaadatok](https://msdn.microsoft.com/library/azure/dn905986.aspx) és a [csoport kategorikus értékek](https://msdn.microsoft.com/library/azure/dn906014.aspx) modulját használjuk.

![Címke létrehozása](./media/text-analytics-module-tutorial/create-label.png)

Ezután megtisztítjuk a szöveget a [előfeldolgozási szöveg](https://msdn.microsoft.com/library/azure/mt762915.aspx) modul használatával. A tisztítás csökkenti az adatkészlet zajszintjét, segít megtalálni a legfontosabb funkciókat, és javítja a végső modell pontosságát. Eltávolítjuk a indexelendő szavakat, például az "a" vagy az "a"-és számokat, speciális karaktereket, duplikált karaktereket, e-mail-címeket és URL-címeket. A szöveget kisbetűsre is konvertáljuk, lemmatize a szavakat, és felderítjük a mondatokat, amelyeket a "| | |" szimbólum jelez az előre feldolgozott szövegben.

![Szöveg előfeldolgozása](./media/text-analytics-module-tutorial/preprocess-text.png)

Mi a teendő, ha egyéni indexelendő szeretne használni? Átadhatja azt opcionális bemenetként. Egyéni C# szintaxisú reguláris kifejezést is használhat az alsztringek cseréjére, és a szavak a beszédek egy részébe való eltávolítására is lehetőség van.

Az előfeldolgozás befejezése után a rendszer felbontotta az adatgyűjtést a betanítási és a tesztelési készletekbe.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2\. lépés: a numerikus funkciós vektorok kinyerése az előre feldolgozott szövegből
Ahhoz, hogy modellt lehessen készíteni a szöveges adathoz, általában a szabad formátumú szöveget kell konvertálnia numerikus funkciós vektorokra. Ebben a példában a szöveges modulból [kinyert N-Gram-funkciókat](https://msdn.microsoft.com/library/azure/mt762916.aspx) használjuk a szöveges adatok ilyen formátumba való átalakításához. Ez a modul a szóközökkel elválasztott szavak egy oszlopát veszi figyelembe, és kiszámítja az adathalmazban megjelenő szavakat vagy N-gramm szavakat. Ezt követően megszámolja, hogy az egyes szavak és az N-grammok hányszor jelennek meg az egyes rekordokban, és ezekből a szolgáltatásokból származó vektorokat hoz létre. Ebben az oktatóanyagban az N-Gram méretet 2 értékre állítjuk, így a funkció-vektorok egyetlen szót és két következő szó kombinációit tartalmazzák.

![N-gramm kibontása](./media/text-analytics-module-tutorial/extract-ngrams.png)

A TF * IDF (kifejezés gyakorisága inverz dokumentum gyakorisága) súlyozást alkalmaz az N-grammos számokra. Ez a megközelítés felveszi a gyakran előforduló szavak súlyozását egy adott rekordban, de a teljes adathalmazban ritkán fordulnak elő. A további lehetőségek közé tartozik a bináris, a TF és a Graph mérés.

Az ilyen szöveges funkciók gyakran magas dimenzióját rendelkeznek. Ha például a corpus 100 000 egyedi szavakat tartalmaz, a szolgáltatási terület mérete 100 000, vagy több, ha N-gramm van használatban. Az N-Gram-szolgáltatások kinyerése modul lehetőséget nyújt a dimenzióját csökkentésére. Kiválaszthatja azokat a szavakat, amelyek rövid vagy hosszúak, vagy túl ritkák vagy túl gyakoriak, hogy jelentős prediktív értékkel rendelkezzenek. Ebben az oktatóanyagban kizárjuk az N-grammot, amely kevesebb mint 5 rekordot vagy a rekordok több mint 80%-ában jelenik meg.

Emellett a szolgáltatás kijelölésével is kiválaszthatja azokat a funkciókat, amelyek a leginkább összefügg az előrejelzési céllal. A KHI-négyzetes funkció kiválasztásával kiválaszthatja az 1000 funkciókat. Az N-gramm kibontása modul jobb kimenetére kattintva megtekintheti a kijelölt szavak szókincsét vagy az N-grammot.

Az N-Gram-funkciók kinyerésének alternatív megközelítése használhatja a funkció-kivonatolási modult. Vegye figyelembe, hogy a [szolgáltatás kivonatolása](https://msdn.microsoft.com/library/azure/dn906018.aspx) nem rendelkezik beépített funkció-kiválasztási képességekkel vagy TF * IDF-súlyozással.

## <a name="step-3-train-classification-or-regression-model"></a>3\. lépés: besorolás vagy regressziós modell betanítása
Most a szöveg át lett alakítva numerikus funkciók oszlopaira. Az adatkészlet továbbra is tartalmaz előző fázisokból származó karakterlánc-oszlopokat, ezért a kizárni kívánt oszlopokat használjuk az adatkészletben.

Ezután [kétosztályos logisztikai regressziót](https://msdn.microsoft.com/library/azure/dn905994.aspx) használunk, hogy megjósoljuk a célunkat: magas vagy alacsony felülvizsgálati pontszám. Ezen a ponton a szöveges elemzési probléma egy rendszeres besorolási problémára lett átalakítva. A modell fejlesztéséhez használhatja a Azure Machine Learning Studio klasszikus verziójában elérhető eszközöket. Például kísérletezheti különböző besorolásokkal, hogy megtudja, milyen eredményeket adnak, vagy a hiperparaméter hangolásával javítsa a pontosságot.

![Betanítás és pontszám](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4\. lépés: a modell pontszáma és ellenőrzése
Hogyan érvényesítheti a betanított modellt? A teszt adatkészletre mutatunk, és kiértékeljük a pontosságot. A modell azonban megtanulta az N-gramm szókincsét és azok súlyait a betanítási adatkészletből. Ezért ezeket a szókincset és a súlyozást a funkciók tesztelési adatokból való kinyerése során kell használni, a szókincs újbóli létrehozása helyett. Ezért az N-Gram-funkciók modult hozzáadjuk a kísérlet pontozási ágában, összekapcsoljuk a kimeneti szókincset a képzési ágban, és a szókincs módot a csak olvasható értékre állíthatja. Az N-g szűrését is letilthatja a gyakorisággal, ha a minimumot 1 és legfeljebb 100%-ra állítja be, majd kikapcsolja a funkció kijelölését.

A tesztelési adatokban szereplő szöveges oszlop numerikus funkciós oszlopokra való átalakítását követően kizárjuk az előző szakaszokból származó karakterlánc-oszlopokat, például az oktatási ágban. Ezt követően a score Model modul használatával előrejelzéseket készíthet, és kiértékelheti a modell modult a pontosság kiértékeléséhez.

## <a name="step-5-deploy-the-model-to-production"></a>5\. lépés: a modell üzembe helyezése éles környezetben
A modell majdnem készen áll az éles környezetbe való üzembe helyezésre. Webszolgáltatásként való üzembe helyezéskor a rendszer a szabadszöveges karakterláncot adja meg bemenetként, és egy "magas" vagy "alacsony" előrejelzést ad vissza. A megtanult N-Gram szókincs használatával átalakítja a szöveget a szolgáltatásokra, és betanítja a logisztikai regressziós modellt, hogy előrejelzést készítsen ezekről a funkciókról. 

A prediktív kísérlet beállításához először mentse az N-Gram szókincset adatkészletként, és a betanított logisztikai regressziós modellt a kísérlet betanítási ágának. Ezt követően a "Mentés másként" paranccsal mentheti a kísérletet, hogy létrehozzon egy kísérleti gráfot a prediktív kísérlethez. Eltávolítjuk az adatbontási modult és a betanítási ágat a kísérletből. Ezután összekapcsolhatjuk a korábban mentett N-grammos szókincset és modellt az N-Gram funkciók és a modell modulok kinyeréséhez. Emellett eltávolítjuk a modell kiértékelése modult is.

Az adatkészletek modulban az oszlop kiválasztása előtt szúrjuk be a Select Columns elemet, mielőtt a Text (címke) oszlopot el kellene távolítani, majd a pontszám modulban a "Hozzáfűzés pontszám oszlopból" lehetőséget. Így a webszolgáltatás nem kéri le az előre jelzett címkét, és nem a válaszban szereplő bemeneti funkciók visszhangját.

![Prediktív kísérlet](./media/text-analytics-module-tutorial/predictive-text.png)

Most már van egy kísérlet, amely webszolgáltatásként, a Request-Response vagy a Batch végrehajtási API-k használatával hívható meg.

## <a name="next-steps"></a>További lépések
Ismerje meg az [MSDN-dokumentációból](https://msdn.microsoft.com/library/azure/dn905886.aspx)származó Text Analytics-modulokat.

