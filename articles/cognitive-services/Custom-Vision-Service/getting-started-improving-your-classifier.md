---
title: Az osztályozót – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Útmutató az osztályozó által igénybe vett minőségének javítására.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 35f83832b0ceb7507b39095e9cc974d82a480c69
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883073"
---
# <a name="how-to-improve-your-classifier"></a>Tartalombesoroló teljesítményének hogyan

Ebben az útmutatóban, megtudhatja, hogyan a Custom Vision Service osztályozó minőségének javítása érdekében. Az osztályozó által igénybe vett minőségét az összeg, minőségi és a címkézett adatokkal, adja meg, és hogyan elosztott terhelésű van a teljes adatkészlet különböző függ. Egy jó osztályozó Mi a rendszer elküldi az osztályozó által igénybe vett, amely kiegyensúlyozott betanítási adatkészletet tartalmaz. A folyamat létrehozásának ilyen besorolás meghatározási; szokás igénybe vehet néhány kerekít képzési várt eredmény eléréséhez.

Az alábbiakban látható egy általános mintája segítséget pontosabb tartalombesoroló létrehozása:

1. Első-ciklikus képzés
1. További rendszerképeket és adatok; hozzáadása újratanítás
1. A különböző háttér, világító, objektum mérete, kamera szög és stílus; lemezképek hozzáadása újratanítás
1. Új lemezkép segítségével előrejelzési tesztelése
1. Előrejelzési eredményeket meglévő betanítási adatok módosítása

## <a name="prevent-overfitting"></a>Overfitting megakadályozása

Egyes esetekben besorolás megtudhatja, hogy előrejelzéseket végezzen tetszőleges, amelyek a rendszerképek a közös jellemzők alapján. Például ha alma citrusfélékről és a egy osztályozó hoz létre, és a használt rendszerképek citrus tagoknál alma és fehér lemezeken, az osztályozó által igénybe vett adhat alma és citrusfélékről helyett a teljes és a lemezek indokolatlan fontosságát.

![Váratlan besorolási képe](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához, használja az alábbi útmutatót képzési több különböző rendszerképek: Adja meg a képeket a különböző szögek, háttér, objektumméret, csoportok és egyéb változatok.

## <a name="data-quantity"></a>Adatok mennyisége

Betanító kép a szám a legfontosabb tényező. Kiindulási pontként címke legalább 50 képenként használatát javasoljuk. Kevesebb képekkel overfitting magasabb kockázata, és a teljesítmény számok javasolhat jó minőségű, míg a modell valós adatokkal előfordulhat, hogy-kihívást jelent. 

## <a name="data-balance"></a>Adatok terheléselosztása

Fontos is érdemes figyelembe venni a relatív a betanítási adatok mennyisége. Például egy címke és a egy másik címke 50 képek 500 rendszerképekből teszi egy imbalanced betanítási adatkészletet. Ennek hatására a modell több címkére, mint a többi előrejelzésére pontosítani fogjuk. Ön valószínűleg jobb eredmények megtekintéséhez, ha legalább egy 1:2 aránya a címke a legkevesebb képekkel és a címkét a legtöbb képekkel megmaradjanak. Ha a címke a legtöbb képekkel 500 lemezképet tartalmaz, például a címke a legkevésbé képekkel rendelkeznie kell legalább 250 képek a betanításhoz.

## <a name="data-variety"></a>Adatok különböző

Győződjön meg arról, mi a rendszer elküldi az osztályozó által igénybe vett a normál használat során képviselője rendszerképek használata. Ellenkező esetben az osztályozó által igénybe vett sikerült megtudhatja, hogy előrejelzéseket végezzen tetszőleges, amelyek a rendszerképek a közös jellemzők alapján. Például ha alma citrusfélékről és a egy osztályozó hoz létre, és a használt rendszerképek citrus tagoknál alma és fehér lemezeken, az osztályozó által igénybe vett adhat alma és citrusfélékről helyett a teljes és a lemezek indokolatlan fontosságát.

![Váratlan besorolási képe](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához, annak érdekében, hogy az osztályozó által igénybe vett lemezképeket számos is generalize is. Az alábbiakban néhány módszert is választja ki a tanítási állítottak több különböző:

* __Háttér:__ Adja meg a képeket az objektum, másik háttér előtt. Fényképek természetes környezetekben is jobb, mint a fényképek semleges háttér előtt, az osztályozó által igénybe vett további információkat nyújtanak.

    ![Háttér-minták képe](./media/getting-started-improving-your-classifier/background.png)

* __Megvilágítási:__ Adja meg a képeket a változatos világítás (készített, a flash, magas kitettség), és így tovább, különösen akkor, ha az előrejelzéshez használt rendszerképek különböző megvilágítási rendelkezik. Is hasznos lehet a különböző színtelítettség, hue és fényerő-rendszerképek használatához.

    ![Világítás minták képe](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektum mérete:__ Adja meg, amelyben az objektumok eltérőek lehetnek a méretét és rendszerképeket (például egy fényképet kötegek származó és az egyetlen banán közelképe). Különböző méretezési segít jobban generalize az osztályozó által igénybe vett.

    ![A minták mérete képe](./media/getting-started-improving-your-classifier/size.png)

* __Kamera szöge:__ Adjon meg másik kamera szögek képekből. Azt is megteheti, ha az összes fényképet rögzített kamera (például a térfigyelő kamerák) kell tenni, ügyeljen arra, hogy egy másik címkét rendel minden overfitting elkerülése érdekében rendszeresen előforduló objektum&mdash;értelmezése nem kapcsolódó objektumok (például lampposts) a kulcs szolgáltatásban.

    ![Szög minták képe](./media/getting-started-improving-your-classifier/angle.png)

* __Stílusa:__ Adja meg a képeket, különböző stílust ugyanahhoz az osztályhoz (például a azonos gyümölcs különböző fajtáinak). Javasoljuk azonban, ha (például Mickey egér és a egy valós beszédhelyzetek egér) mutatkozhatnak stílusok objektummal rendelkezik, akkor címkézze különálló osztályt jobban a különböző funkciói.

    ![Style-minták képe](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negatív képek

A projekt valamely pontján, előfordulhat, hogy hozzá kell _minták negatív_ annak érdekében, pontosabb az osztályozó által igénybe vett. Negatív minták azokat, amelyek a nem megfelelő más címkék. Ezek a lemezképek feltöltésekor a alkalmazni a speciális **negatív** címke működnek.

> [!NOTE]
> A Custom Vision Service néhány automatikus negatív kép kezelését támogatja. Például és banán osztályozó szőlőmustnak épít, és a egy cipő előrejelzéshez kép küldése, ha az osztályozó által igénybe vett kell pontszám a lemezképet, 0 % közelében szőlőmust és banán is.
> 
> Másrészről azokban az esetekben, ahol a negatív rendszerképekkel képzés használt rendszerképek egy változata, akkor valószínű, hogy a modell lesz a negatív képek besorolása, a nagyszerű Hasonlóságok miatt címkézett osztály. Például ha grépfrút osztályozó és a egy narancssárga rendelkezik, és a egy clementine képe, csatorna, előfordulhat, hogy pontszám a clementine egy narancs számos funkcióját a clementine csúcsos narancs, mert. Ha a negatív képek az ilyen jellegű, azt javasoljuk, létrehozhat egy vagy több további címkéket (például **más**) és a negatív képek a címkével ellátott címkét, hogy ezeket az osztályokat jobban megkülönböztetni a modell betanítása közben .

## <a name="use-prediction-images-for-further-training"></a>További oktatási előrejelzési képek használata

Amikor használja, vagy a kép osztályozó tesztelése az előrejelzési végpont lemezképek elküldésével, a Custom Vision service tárolja ezeket a képeket. Ezután használhatja őket a modell továbbfejlesztése.

1. Az osztályozó által igénybe vett elküldött rendszerképek megtekintéséhez nyissa meg a [Custom Vision weblap](https://customvision.ai)nyissa meg a projektet, és válassza ki a __előrejelzéseket__ fülre. Az alapértelmezett nézet a jelenlegi verzió továbbfejlesztésében képeit jeleníti meg. Használhatja a __iteráció__ legördülő menüre kattintva megtekintheti az előző lépés során küldött kép.

    ![Képernyőkép az előrejelzés a lap képeket tartalmazó nézet](./media/getting-started-improving-your-classifier/predictions.png)

2. Kép az osztályozó által előrejelzett is címkéinek megtekintéséhez mutasson. Képek vannak rendezve, hogy a kapcsolatok, amivel jelentős az osztályozó által igénybe vett fejlesztései a legtöbb felsorolt tetején. Egy másik rendezési módszer használatához válasszon ki egy a __rendezési__ szakaszban. 

    Kép felvétele a meglévő betanítási adatok, válassza ki a lemezképet, állítsa be a megfelelő kód(ok), és kattintson __mentse és zárja be__. A kép eltűnik __előrejelzéseket__ és képzési képkészlet hozzáadni. Kiválasztásával megtekintheti a __betanító kép__ fülre.

    ![A címkézési lapjának képe](./media/getting-started-improving-your-classifier/tag.png)

3. Ezután a __Train__ újratanítás PowerShell osztályozó gombra.

## <a name="visually-inspect-predictions"></a>Vizuálisan megvizsgálják az előrejelzések

Vizsgálhatja meg a lemezkép-előrejelzéseket, nyissa meg a __betanító kép__ lapra, válassza ki az előző képzési iterációját a **iteráció** legördülő menüből, és a egy vagy több címke alapján ellenőrizze a **címkék** szakaszban. A nézet most kell megjelenítenie a piros Keretes minden, a képek, amelynek a modell nem sikerült megfelelően előre jelezni az adott címke.

![Az ismétlés előzmények képe](./media/getting-started-improving-your-classifier/iteration.png)

Néha egy vizuális ellenőrzése azonosíthatja a mintákat, amelyek ezután kijavíthatja a további betanítási adatok hozzáadásával vagy módosításával a meglévő betanítási adatok. Például alma Lime és a egy osztályozó helytelenül említik összes zöld alma Lime szerint. Majd hozzáadását, és zöld alma megcímkézett képeket tartalmazó betanítási adatok megadásával javítsa ki a probléma.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megismerhette, hogy az egyéni rendszerkép osztályozási modell pontosabb különböző módszereket vetnek. Ezután megtudhatja, hogyan lemezképek programozott módon teszteléséhez az előrejelzési API-nak elküldésével.

> [!div class="nextstepaction"]
> [Előrejelzési API használata](use-prediction-api.md)
