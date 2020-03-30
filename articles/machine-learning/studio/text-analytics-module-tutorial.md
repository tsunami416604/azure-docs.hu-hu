---
title: Hangulatelemzés használati esete
titleSuffix: ML Studio (classic) - Azure
description: Szövegelemzési modellek létrehozása az Azure Machine Learning Studio -ban (klasszikus) a szöveg előfeldolgozásához, az N-grammokhoz vagy a szolgáltatáskivonatoláshoz használt modulok használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217885"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Hangulatelemzési modell létrehozása az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) használatával szövegelemzési modelleket hozhat létre és működőképessé tehet. Ezek a modellek segíthetnek például a dokumentumbesorolási vagy hangulatelemzési problémák megoldásában.

Egy szövegelemzési kísérletben általában a következőket kell:

1. Szövegadatkészlet tisztítása és feldolgozása
2. Numerikus jellemzővektorok kinyerése előre feldolgozott szövegből
3. Vonat osztályozási vagy regressziós modell
4. A modell pontozása és érvényesítése
5. A modell üzembe helyezése éles környezetben

Ebben a bemutatóban megtanulod ezeket a lépéseket, ahogy végigsétálunk egy hangulatelemzési modellen az Amazon Book Reviews adatkészlet használatával (lásd ezt a kutatási papírt "Életrajzok, Bollywood, Boom-dobozok és Turmixgépek: Domain adaptáció hangulatbesoroláshoz" John Blitzer, Mark Dredze és Fernando Pereira; Számítógépes Nyelvészeti Egyesület (ACL), 2007.) Ez az adatkészlet a véleményezési pontszámokból (1-2 vagy 4-5) és egy szabad formátumú szövegből áll. A cél az, hogy megjósolni a felülvizsgálat pontszám: alacsony (1-2) vagy magas (4-5).

Az oktatóanyagban tárgyalt kísérleteket az Azure AI-galériában találja:

[Könyvértékelések előrejelzése](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Tippelje meg a könyvértékeléseket - Prediktív kísérlet](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1. lépés: Szövegadatkészlet tisztítása és előfeldolgozása
Kezdjük a kísérletet osztva a felülvizsgálati pontszámok kategorikus alacsony és magas vödrök megfogalmazni a problémát, mint két osztály besorolás. [Metaadatok szerkesztése](https://msdn.microsoft.com/library/azure/dn905986.aspx) és [csoport kategorikus értékek](https://msdn.microsoft.com/library/azure/dn906014.aspx) modulokat használunk.

![Címke létrehozása](./media/text-analytics-module-tutorial/create-label.png)

Ezután tisztítsa meg a szöveget a [Szöveg előfeldolgozása](https://msdn.microsoft.com/library/azure/mt762915.aspx) modullal. A tisztítás csökkenti az adatkészlet zaját, segít megtalálni a legfontosabb funkciókat, és javítja a végleges modell pontosságát. Eltávolítjuk a stopszót - az olyan gyakori szavakat, mint "a" vagy "a" - és a számokat, a különleges karaktereket, a duplikált karaktereket, az e-mail címeket és az URL-eket. A szöveget kisbetűssé alakítjuk, lemmatizáljuk a szavakat, és észleljük azokat a mondathatárokat, amelyeket az előre feldolgozott szöveg "|||" szimbóluma jelez.

![Szöveg előfeldolgozása](./media/text-analytics-module-tutorial/preprocess-text.png)

Mi a teendő, ha a stopszavak egyéni listáját szeretné használni? Átadhatja opcionális bemenetként. Egyéni C# szintaxis reguláris kifejezéssel is helyettesítheti a karakterláncokat, és a szavakat beszédrészeként távolíthatja el: főnevek, igék vagy melléknevek.

Az előfeldolgozás befejezése után felosztjuk az adatokat vonat- és tesztkészletekre.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2. lépés: Numerikus jellemzővektorok kinyerése előre feldolgozott szövegből
A szöveges adatok modelljének létrehozásához általában a szabad formátumú szöveget numerikus szolgáltatásvektorokká kell alakítania. Ebben a példában az [N-Gram-funkciók kivonása a szövegmodulból](https://msdn.microsoft.com/library/azure/mt762916.aspx) a szöveges adatok ilyen formátumba történő átalakításához használjuk. Ez a modul egy szóvilágba szóközbe szóközbe szóvá vált szavakból álló oszlopot vesz fel, és kiszámítja az adatkészletben megjelenő szavak vagy N-gramm szavak szótárát. Ezután megszámolja, hogy az egyes szavak vagy N-gramok hányszor jelennek meg az egyes rekordokban, és ezekből a számlálókból szolgáltatásvektorokat hoz létre. Ebben az oktatóanyagban az N-gram mérete 2, így a funkcióvektorok egyetlen szavakat és két további szó kombinációját tartalmazzák.

![Kivonat N-gramm](./media/text-analytics-module-tutorial/extract-ngrams.png)

A TF*IDF (Term Frequency Inverse Document Frequency) súlyozását az N-gram számokra alkalmazzuk. Ez a megközelítés hozzáadja a szavak súlyát, amelyek gyakran jelennek meg egyetlen rekordban, de a teljes adatkészletben ritkák. Egyéb lehetőségek közé tartozik a bináris, TF, és grafikon mérlegelés.

Az ilyen szövegjellemzők gyakran nagy méretűek. Ha például a korpusz 100 000 egyedi szóból áll, a jellemzőterület 100 000 vagy több lesz, ha N-grammokat használ. A Extract N-Gram Features modul egy sor lehetőséget kínál a méretcsökkentésre. Kizárhatja a rövid vagy hosszú, túl ritka vagy túl gyakori szavakat ahhoz, hogy jelentős prediktív értékkel tartozzon. Ebben az oktatóanyagban kizárjuk az 5 rekordnál kevesebb rekordban vagy a rekordok több mint 80%-ában megjelenő N-grammokat.

Emellett a funkcióválasztás sal csak azokat a funkciókat választhatja ki, amelyek a leginkább korreláltak az előrejelzési céllal. A Chi-Squared funkcióválasztást 1000 funkció kiválasztásához használjuk. Megtekintheti a szókincs a kiválasztott szavak vagy N-gramm kattintva a jobb kimenet kivonása N-gramm modul.

Az N-Gram-funkciók kinyerése szolgáltatás használata alternatív módszerként használhatja a funkciókivonat-modult. Ne feledje azonban, hogy [a szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/dn906018.aspx) nem rendelkezik beépített funkciókijelölési képességekkel, illetve tF*IDF-méréssel.

## <a name="step-3-train-classification-or-regression-model"></a>3. lépés: A vonat besorolása vagy regressziós modellje
Most a szöveg numerikus jellemzőoszlopokká alakult át. Az adatkészlet továbbra is tartalmaz karakterláncoszlopokat az előző szakaszokból, ezért az adatkészlet oszlopok kijelölése használatával kizárjuk őket.

Ezután [kétosztályos logisztikai regressziót](https://msdn.microsoft.com/library/azure/dn905994.aspx) használunk a cél előrejelzéséhez: magas vagy alacsony felülvizsgálati pontszám. Ezen a ponton a szövegelemzési probléma rendszeres besorolási problémává alakult át. Használhatja az Azure Machine Learning Studio (klasszikus) elérhető eszközöket a modell fejlesztéséhez. Kísérletezhet például különböző osztályozókkal, hogy megtudja, mennyire pontos eredményeket adnak, vagy a pontosság javítása érdekében használhatja a hiperparaméter-hangolást.

![Vonat és pontszám](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4. lépés: Pontszám és érvényesítése a modell
Hogyan érvényesítené a betanított modellt? A tesztadatkészlethez mérünk, és kiértékeljük a pontosságot. A modell azonban megtanulta az N-gramm okit és azok súlyát a betanítási adatkészletből. Ezért ezt a szókincset és súlyokat kell használnunk, amikor a tesztadatokból kinyerjük a jellemzőket, ahelyett, hogy újra létrehoznánk a szókincset. Ezért hozzáadjuk az N-Gram-jellemzők kinyerési modult a kísérlet pontozási ágához, csatlakoztatjuk a kimeneti szókincset a betanítási ágból, és a szókincs módot csak olvashatóra állítjuk. Az N-grammok szűrését is letiltjuk gyakoriság szerint, ha a minimumot 1 példányra és maximum 100%-ra állítjuk, és kikapcsoljuk a funkcióválasztást.

Miután a tesztadatok szövegoszlopa numerikus jellemzőoszlopokká lett alakítva, kizárjuk a karakterláncoszlopokat az előző szakaszokból, például a betanítási ágból. Ezután a Score Model modul t, hogy előrejelzéseket és modell kiértékelése modul a pontosság kiértékeléséhez.

## <a name="step-5-deploy-the-model-to-production"></a>5. lépés: A modell üzembe helyezése éles környezetben
A modell majdnem készen áll az éles környezetben való üzembe helyezésre. Amikor webszolgáltatásként van telepítve, szabad formátumú szöveges karakterláncot vesz fel bemenetként, és egy "magas" vagy "alacsony" előrejelzést ad vissza. A tanult N-gram szókincs segítségével alakítja át a szöveget funkciókká, és betanított logisztikai regressziós modellt használ, hogy előrejelzést készítsen ezekből a funkciókból. 

A prediktív kísérlet beállítása, először menteni az N-gram szókincs adatkészletként, és a betanított logisztikai regressziós modell a betanítási ága a kísérlet. Ezután a "Mentés másként" használatával mentjük a kísérletet, hogy létrehozzunk egy kísérleti grafikont a prediktív kísérlethez. Eltávolítjuk a Split Data modult és a betanítási ágat a kísérletből. Ezután csatlakoztassa a korábban mentett N-gram szókincs és modell kivonat N-Gram jellemzők és score modell modulok, illetve. Eltávolítjuk a Modell kiértékelése modult is.

A Címkemodul ban az Oszlopok kijelölése modult illesztjük be a Szöveg előfeldolgozása modulba a címkeoszlop eltávolításához, és töröljük a "Pontszámoszlop hozzáfűzése az adatkészlethez" opciót a Score modulban. Így a webszolgáltatás nem kéri a címkét próbál előre jelezni, és nem visszhangozza a bemeneti funkciók válaszként.

![Prediktív kísérlet](./media/text-analytics-module-tutorial/predictive-text.png)

Most már van egy kísérlet, amely közzétehető egy webszolgáltatás, és hívott a kérelem-válasz vagy a kötegelt végrehajtási API-k használatával.

## <a name="next-steps"></a>Következő lépések
Tudjon meg többet a szövegelemzési modulokról az [MSDN dokumentációból.](https://msdn.microsoft.com/library/azure/dn905886.aspx)

