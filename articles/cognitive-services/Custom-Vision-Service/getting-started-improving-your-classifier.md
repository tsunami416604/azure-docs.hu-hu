---
title: A Custom Vision Service - Azure Cognitive Services használatával tartalombesoroló |} A Microsoft Docs
description: Útmutató a Custom Vision Service osztályozó minőségének javítására.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 07/05/2018
ms.author: nolachar
ms.openlocfilehash: 7c6cbd996d0c35b96fde78daf391bebb36feddce
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888180"
---
# <a name="how-to-improve-your-classifier"></a>Tartalombesoroló teljesítményének hogyan

Útmutató a Custom Vision Service osztályozó minőségének javítására. A minőség az osztályozó által igénybe vett szolgáltatás az összeg, minőségi és a címkézett adatokkal, adja át, és hogyan elosztott terhelésű van az adatkészlet különböző függ. Egy jó osztályozó általában egy elosztott terhelésű betanítási adatkészletet, mi a rendszer elküldi az osztályozó által igénybe vett, amely rendelkezik. A meghatározási folyamata ismétlődő, például egy osztályozó létrehozása gyakran. Szokás igénybe vehet néhány kerekít képzési várt eredmény eléréséhez.

Besorolás javítására tett általános lépései a következők. Ezeket a lépéseket nem rögzített és a gyors szabályokat, de a heurisztika, amelyekkel jobb tartalombesoroló létrehozása.

1. Első-ciklikus képzés
1. Adjon hozzá további rendszerképeket és az adatok elosztása
1. Újratanítás
1. Adja hozzá a lemezképek különböző háttér, világító, objektumméret, kamera szög és stílus
1. Újratanítás PowerShell & kép előrejelzéshez hírcsatorna
1. Vizsgálja meg az előrejelzési eredményeket
1. Meglévő betanítási adatok módosítása

## <a name="data-quantity-and-data-balance"></a>Adatok mennyisége és az adatok elosztása

A legfontosabb, hogy elegendő a besorolás betanítására képek feltöltése. A betanítási készlet címke legalább 50 képenként kiindulási pontként használata ajánlott. Kevesebb képekkel nincs erős kockázata, hogy Ön overfitting. A teljesítmény számok javasolhat jó minőségű, míg a előfordulhat, hogy elvégezzen valós adatokon. További rendszerképeket az osztályozó által igénybe vett képzés általában növeli az előrejelzési eredmények pontossága.

Egy másik szempont, hogy győződjön meg arról, hogy az adatok kiegyensúlyozott. Például 500 képek egy címke és a egy másik címke 50 lemezképek eredményez olyan imbalanced betanítási adatkészletet, a modell több címkére, mint a többi előrejelzésére pontosítani okozó. Ön valószínűleg jobb eredmények megtekintéséhez, ha legalább egy 1:2 aránya a címke a legkevesebb képekkel és a címkét a legtöbb képekkel megmaradjanak. Például ha a címke a lemezképek legnagyobb száma 500 lemezképet tartalmaz, a címke a legkevésbé képekkel rendelkeznie kell legalább 250 képek a betanításhoz.

## <a name="train-more-diverse-images"></a>Több sokféle kép betanítása

Adja meg a rendszerkép található, mi a rendszer elküldi az osztályozó által igénybe vett a normál használat során jellemzőek. Például egy "apple" osztályozó tanítása, az osztályozó által igénybe vett nem lehet olyan pontos, ha csak betanításához alma lemezeket, de a márka alapján történő fényképek alma fák a fényképek. Többek között a lemezképek különböző fog ellenőrizze, hogy az osztályozó által igénybe vett nem torzítatlan, és jól generalize is. Az alábbiakban néhány módszert is választja ki a tanítási állítottak több különböző:

__Háttér:__ adja meg az objektum elé különböző hátterek (vagyis a lemezre és a gyümölcs pékséglánc tasakban gyümölcs) lemezképek. Fényképek környezetben jobb, mint a fényképek semleges háttér előtt, az osztályozó által igénybe vett további információkat nyújtanak.

![Háttér-minták képe](./media/getting-started-improving-your-classifier/background.png)

__Megvilágítási:__ adja meg a képeket a változatos világítás (azt jelenti, hozott-nal, flash, magas, stb.), különösen akkor, ha az előrejelzéshez használt rendszerképek különböző megvilágítási rendelkezik. Emellett hasznos változatos színtelítettség, hue és fényerő képeket is tartalmaznak.

![Világítás minták képe](./media/getting-started-improving-your-classifier/lighting.png)

__Objektum mérete:__ adjon meg képeket, amelyben az objektumok a különböző méretezési rögzítése az objektumot különböző részeit. Például egy fényképet származó és az egyetlen banán közelképe fürtök. Különböző méretezési segít jobban generalize az osztályozó által igénybe vett.

![A minták mérete képe](./media/getting-started-improving-your-classifier/size.png)

__Kamera szöge:__ adjon meg másik kamera szögek képekből. Összes fényképeit veszik rögzített kamerák (például a térfigyelő kamerák) vannak beállítva, hogy rendel másik címkét minden kamera akkor is, ha a ugyanazon objektumok overfitting – a fontos szolgáltatása, a nem kapcsolódó objektumok (például lampposts) modellezési elkerülése érdekében rögzítése.

![Szög minták képe](./media/getting-started-improving-your-classifier/angle.png)

__Stílus:__ adja meg a képeket, különböző stílust ugyanahhoz az osztályhoz (azaz különféle citrusfélékről). Azonban ha az objektumok a jelentősen eltérő stílusok (azaz Mickey egér és a egy valós beszédhelyzetek rat) lemezképek, ajánlott címkézze külön osztályokat jobb jelölik, mint a különböző szolgáltatások.

![Style-minták képe](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Az előrejelzés elküldése-rendszerképek használatához

A Custom Vision Service tárolja a lemezképeket az előrejelzési végpontra küldött. Ezek a lemezképek használatával javíthatja az osztályozó által igénybe vett, használja az alábbi lépéseket:

1. Az osztályozó által igénybe vett elküldött rendszerképek megtekintéséhez nyissa meg a [Custom Vision weblap](https://customvision.ai)nyissa meg a projektet, és válassza ki a __előrejelzéseket__ fülre. Az alapértelmezett nézet a jelenlegi verzió továbbfejlesztésében képeit jeleníti meg. Használhatja a __iteráció__ legördülő mező előző lépés során küldött kép megtekintéséhez.

    ![Az előrejelzések lap képe](./media/getting-started-improving-your-classifier/predictions.png)

2. Kép az osztályozó által előrejelzett is címkéinek megtekintéséhez mutasson. Képek rangsora, hogy a képek, amelyek a legtöbb nyereségek is előtérbe az osztályozó által igénybe vett tetején. Válassza ki, eltérő rendezést, használja a __rendezési__ szakaszban. Kép hozzáadása a meglévő betanítási adatok, válassza ki a lemezképet, jelölje ki a megfelelő címkét, és kattintson a __mentse és zárja be__. A kép eltűnik __előrejelzéseket__ és hozzáadja a betanító kép. Kiválasztásával megtekintheti a __betanító kép__ fülre.

    ![A címkézési lapjának képe](./media/getting-started-improving-your-classifier/tag.png)

3. Használja a __Train__ újratanítás PowerShell osztályozó gombra.

## <a name="visually-inspect-predictions"></a>Vizuálisan megvizsgálják az előrejelzések

Vizsgálhatja meg a lemezkép-előrejelzéseket, válassza ki a __betanító kép__ lapot, majd __iteráció előzmények__. A piros jelölését vázolt lemezképek helytelenül előre meghatározott is.

![Az ismétlés előzmények képe](./media/getting-started-improving-your-classifier/iteration.png)

Néha vizuális ellenőrzése azonosíthatja a mintákat, amelyek ezután kijavíthatja a további betanítási adatok hozzáadásával vagy módosításával a meglévő betanítási adatok. Például Apple sárgászöld és a egy osztályozó helytelenül említik összes zöld alma Lime szerint. Előfordulhat, hogy a probléma elhárításához hozzáadásával és a betanítási adatok zöld alma megcímkézett képeket tartalmazó biztosít.

## <a name="unexpected-classification"></a>Váratlan besorolás

Az osztályozó által igénybe vett néha helytelenül tanul, amelyek a rendszerképek a közös jellemzők. Például ha alma citrusfélékről és a egy osztályozó hoz létre, és a megadott lemezképek tagoknál alma és citrus fehér lemezeken, az osztályozó által igénybe vett előfordulhat, hogy betanításához kéz helyett alma és citrusfélékről fehér lemezeket és a.

![Váratlan besorolási képe](./media/getting-started-improving-your-classifier/unexpected.png)

Az a probléma megoldásához használja a fenti útmutató képzési több különböző rendszerképek: Adja meg a képeket a különböző szögek, háttér, objektumméret, csoportok és más variantní hodnoty.

## <a name="negative-image-handling"></a>Negatív kép kezelése

A Custom Vision Service néhány automatikus negatív kép kezelését támogatja. Abban az esetben, ahol banán osztályozó és szőlőmustnak épít, és küldje el a cipő előrejelzéshez képe az osztályozó által igénybe vett szőlőmust és banán kell pontszám a lemezképet, 0 % közelében.

Másrészről azokban az esetekben, ahol a negatív rendszerképekkel képzés használt rendszerképek egy változata, akkor valószínű, hogy a modell lesz a negatív képek besorolása, a nagyszerű Hasonlóságok miatt címkézett osztály. Például ha grépfrút osztályozó és a egy narancssárga rendelkezik, és a egy clementine képe, csatorna, előfordulhat, hogy pontszám a clementine narancs. Ez akkor fordulhat elő a clementine számos funkcióját óta (szín, alakzat, mintázat, természetes habitat, stb.), amely narancs hasonlók.  Ha a negatív képek az ilyen jellegű, javasoljuk, hogy hozzon létre egyet, vagy további címkéket ("egyéb"), és ez a címke negatív rendszerképek címkézését, hogy ezeket az osztályokat jobban megkülönböztetni a modell betanítása közben.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan tesztelheti lemezképek programozott módon az előrejelzési API-nak elküldésével.

> [!div class="nextstepaction"]
[Előrejelzési API használata](use-prediction-api.md)
