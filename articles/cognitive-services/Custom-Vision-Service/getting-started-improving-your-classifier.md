---
title: Az osztályozó javítása – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy miként javítható az osztályozó minősége.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: d71c750185589fd488df70b63fd48e9e674ee3dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561054"
---
# <a name="how-to-improve-your-classifier"></a>Az osztályozó javítása

Ebből az útmutatóból megtudhatja, hogyan javíthatja Custom Vision Service osztályozó minőségét. Az osztályozó minősége a megadott címkével ellátott adatok mennyiségétől, minőségétől és számától függ, és hogy mennyire kiegyensúlyozott a teljes adatkészlet. A megfelelő osztályozó kiegyensúlyozott betanítási adatkészlettel rendelkezik, amely az osztályozó által elküldött adatokat reprezentálja. Az ilyen osztályozók létrehozásának folyamata iterációs jellegű. gyakori, hogy eltarthat néhány kört a képzéstől a várt eredmények eléréséhez.

A következőkben egy általános minta található, amely segít a pontosabb osztályozó létrehozásában:

1. Első köros képzés
1. További lemezképek és egyenlegek hozzáadása; újratanítása
1. Különböző háttérrel, világítással, objektum méretével, kamera látószögével és stílusával adhat hozzá képeket. újratanítása
1. Új rendszerkép (ek) használata az előrejelzés teszteléséhez
1. Meglévő betanítási adatmennyiség módosítása az előrejelzési eredmények alapján

## <a name="prevent-overfitting"></a>Túlilleszkedés megakadályozása

Időnként az osztályozó megtudhatja, hogy az előrejelzések a képek által közösen használt sajátosságok alapján legyenek elérhetők. Ha például az alma és a Citrus számára hoz létre egy osztályozó, és az alma a kezében és a Citrus on fehér lemezeken is használta a képeket, az osztályozó az alma és a Citrus helyett aránytalanul nagy jelentőséget biztosíthat a kezek és a lemezek számára.

![Nem várt besorolású rendszerkép](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához kövesse az alábbi útmutatást a többváltozós képekkel való képzéshez: különböző nézőpontokkal, háttérrel, objektum méretével, csoportokkal és egyéb változatokkal rendelkező lemezképeket biztosíthat.

## <a name="data-quantity"></a>Adatmennyiség

A betanítási lemezképek száma a legfontosabb tényező. Javasolt kiindulási pontként legalább 50 lemezképet használni címkén. Kevesebb képpel nagyobb a terhelés, és míg a teljesítménybeli számok jó minőségűek lehetnek, a modell valós adatokkal járhat. 

## <a name="data-balance"></a>Adategyensúly

Fontos figyelembe venni a betanítási adataihoz tartozó relatív mennyiségeket is. Például a 500 lemezképek használata egy címkéhez és a 50-lemezképek egy másik címkéhez egy kiegyensúlyozatlan betanítási adatkészletet tesz lehetővé. Ez azt eredményezi, hogy a modell pontosabban fog megjelenni egy címke előrejelzésében. Valószínűleg jobb eredményeket tapasztal, ha legalább 1:2 arányban tartja a címkét a legkevesebb képpel és a legtöbb képpel rendelkező címkével. Ha például a legtöbb lemezképtel rendelkező címke 500-es képet tartalmaz, a legkevésbé képképekkel rendelkező címkének legalább 250 lemezképet kell tartalmaznia a betanításhoz.

## <a name="data-variety"></a>Adatválaszték

Ügyeljen arra, hogy a normál használat során az osztályozó által beküldött képeket is használják. Ellenkező esetben az osztályozó megtudhatja, hogy az előrejelzések a képek által közösen használt sajátosságok alapján legyenek elérhetők. Ha például az alma és a Citrus számára hoz létre egy osztályozó, és az alma a kezében és a Citrus on fehér lemezeken is használta a képeket, az osztályozó az alma és a Citrus helyett aránytalanul nagy jelentőséget biztosíthat a kezek és a lemezek számára.

![Nem várt besorolású rendszerkép](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához több rendszerkép is használható, amelyek biztosítják, hogy az osztályozó is általánosítható legyen. Az alábbi módokon teheti meg, hogy a képzési lehetőségek sokrétűek legyenek:

* __Háttér__ A különböző hátterek előtt adja meg az objektum képeit. A természetes környezetekben található fényképek jobbak, mint a semleges hátterű fényképek, mivel további információkat biztosítanak az osztályozó számára.

    ![Háttérbeli minták képe](./media/getting-started-improving-your-classifier/background.png)

* __Világítás__ A képeket különböző világítással (a Flash, a magas expozícióval stb.), különösen akkor, ha az előrejelzéshez használt képek eltérő megvilágítással rendelkeznek. Az is hasznos, ha a képeket különböző telítettséggel, árnyalattal és fényerővel szeretné használni.

    ![Világító minták képe](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektum mérete:__ Adja meg azokat a lemezképeket, amelyekben az objektumok mérete és száma (például a banán fürtökből származó képek és egy egyetlen banánból álló Vértes) változó. A különböző méretezés segíti az osztályozó általánosítás jobb kiválasztását.

    ![Méret minták képe](./media/getting-started-improving-your-classifier/size.png)

* __Kamera szöge:__ Különböző kamera-szögek által készített lemezképek megadása. Ha az összes fényképet rögzített kamerákkal (például a térfigyelő kamerákkal) kell elvégezni, ügyeljen arra, hogy egy másik címkét rendeljen minden rendszeresen előforduló objektumhoz a nem kapcsolódó objektumok&mdash;(például Lámpaoszlopok) értelmezésének elkerülése érdekében. a legfontosabb szolgáltatásként.

    ![Szög minták képe](./media/getting-started-improving-your-classifier/angle.png)

* __Stílusa__ Adja meg ugyanazt az osztály különböző stílusainak képeit (például az azonos gyümölcs különböző fajtáit). Ha azonban a különböző stílusok (például a Mickey Mouse és a valós egér) objektumai vannak, akkor azt javasoljuk, hogy külön osztályokként címkézse őket, hogy jobban képviseljék a különböző funkcióit.

    ![Stílusú minták képe](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negatív képek

Előfordulhat, hogy a projekt egy bizonyos pontján _negatív mintákat_ kell hozzáadnia, hogy az osztályozó pontosabb legyen. A negatív minták olyanok, amelyek nem egyeznek a többi címkével. Ha feltölti ezeket a képeket, alkalmazza rájuk a speciális **negatív** címkét.

> [!NOTE]
> A Custom Vision Service támogatja a negatív rendszerképek automatikus kezelését. Ha például a szőlő és a banán besorolását készíti elő, és a cipőt az előrejelzéshez beküldi, akkor az osztályozó az adott képet a szőlő és a banán esetében is a 0%-os értékkel közelíti meg.
> 
> Ha azonban a negatív rendszerképek csak a betanítás során használt rendszerképek variációi, akkor valószínű, hogy a modell a nagy hasonlóságok miatt a negatív képeket címkézett osztályként fogja osztályozni. Ha például egy narancssárga és a grapefruit besorolással rendelkezik, és egy clementine-képet is megadsz, akkor a clementine narancssárgaként jelenhet meg, mivel a clementine számos funkciója hasonlít a narancssárga értékekre. Ha a negatív képek ilyen jellegűek, javasoljuk, hogy hozzon létre egy vagy több további címkét ( például a többiet), és címkézze a negatív képeket ezzel a címkével a képzés során, hogy a modell jobban megkülönböztetse ezeket az osztályokat.

## <a name="use-prediction-images-for-further-training"></a>Előrejelzési lemezképek használata a további képzéshez

Ha a lemezképet az előrejelzési végpontra küldi el, vagy a rendszerkép besorolását használja, akkor a Custom Vision szolgáltatás tárolja ezeket a lemezképeket. Ezután a modell fejlesztéséhez használhatja őket.

1. Az osztályozó által küldött képek megtekintéséhez nyissa meg a [Custom Vision weblapot](https://customvision.ai), lépjen a projektbe, és válassza a __jóslatok__ fület. Az alapértelmezett nézet az aktuális iteráció képeit jeleníti meg. A korábbi iterációk során elküldött képek megtekintéséhez használhatja az __iteráció__ legördülő menüt.

    ![képernyőkép a jóslatok lapról a képek nézetben](./media/getting-started-improving-your-classifier/predictions.png)

2. Vigye az egérmutatót egy képre az osztályozó által előre jelzett címkék megtekintéséhez. A képek rendezése úgy történik, hogy azok, amelyek az osztályozó legtöbb tökéletesítését lehetővé teszik, megjelennek a tetején. Ha másik rendezési módszert szeretne használni, válasszon ki egy kijelölést a __Rendezés__ szakaszban. 

    Ha képet szeretne adni a meglévő betanítási adatokhoz, válassza ki a képet, állítsa be a megfelelő címkét, majd kattintson a __Mentés és bezárás__gombra. A rendszer eltávolítja a lemezképet az __előrejelzések__ közül, és hozzáadja a betanítási lemezképek készletéhez. A betanítási __képek__ lapon tekintheti meg.

    ![A címkézési lap képe](./media/getting-started-improving-your-classifier/tag.png)

3. Ezután a __vonat__ gomb használatával Újrataníthatja az osztályozó.

## <a name="visually-inspect-predictions"></a>Előrejelzések vizuális vizsgálata

A képelőrejelzések vizsgálatához lépjen a __betanítási képek__ lapra, válassza ki az előző tanítási iterációt az **iteráció** legördülő menüben, és ellenőrizze a **címkék** szakaszban található címkéket. A nézetnek ekkor egy piros téglalapot kell megjelenítenie minden olyan rendszerkép körül, amelyhez a modell nem tudta megfelelően megjósolni a megadott címkét.

![Az iteráció előzményeinek képe](./media/getting-started-improving-your-classifier/iteration.png)

Előfordulhat, hogy a vizuális vizsgálat olyan mintákat azonosít, amelyeket később további betanítási adatok hozzáadásával vagy a meglévő betanítási adatok módosításával lehet kijavítani. Például az Apples és a Limes-osztályozók nem megfelelően címkézik az összes zöld almát. Ezt a problémát a zöld alma címkézett képeit tartalmazó betanítási adatok hozzáadásával és biztosításával orvosolhatja.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban számos technikát tanult meg az egyéni képbesorolási modell pontosabb elvégzéséhez. Következő lépésként megtudhatja, hogyan tesztelheti a képeket programozott módon az előrejelzési API-ba való küldéssel.

> [!div class="nextstepaction"]
> [Az előrejelzési API használata](use-prediction-api.md)
