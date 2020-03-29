---
title: Az osztályozó fejlesztése - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogy az adatok mennyisége, minősége és változatossága hogyan javíthatja az osztályozó minőségét a Custom Vision szolgáltatásban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718542"
---
# <a name="how-to-improve-your-classifier"></a>Hogyan lehet javítani az osztályozó

Ebben az útmutatóban megtudhatja, hogyan javíthatja a Custom Vision Service osztályozó minőségét. Az osztályozó minősége a megadott címkézett adatok mennyiségétől, minőségétől és változatosságát, valamint a teljes adatkészlet kiegyensúlyozottságáttól függ. Egy jó osztályozó kiegyensúlyozott betanítási adatkészletet, amely reprezentatív, hogy mit kell benyújtani az osztályozó. Az ilyen osztályozó építésének folyamata iteratív; ez gyakori, hogy néhány fordulóban a képzés, hogy elérjék a várt eredményeket.

Az alábbiakban egy általános minta segít a pontosabb osztályozó létrehozásában:

1. Első körös edzés
1. További képek és egyenlegadatok hozzáadása; Átképzés
1. Különböző háttérrel, megvilágítással, objektummérettel, kameraszöggel és stílussal rendelkező képek hozzáadása; Átképzés
1. Az előrejelzés tesztelése új kép(ek) használatával
1. Meglévő betanítási adatok módosítása az előrejelzési eredmények nek megfelelően

## <a name="prevent-overfitting"></a>A túlszerelés megakadályozása

Néha, egy osztályozó megtanulja, hogy előrejelzések alapján tetszőleges jellemzők, hogy a képek közös. Ha például alma és citrus félék osztályozóját készíti, és a kezében lévő almákról és a citrusfélékről készített képeket fehér lemezeken használta, az osztályozó indokolatlan jelentőséget adhat a kéznek a lemezeknek, nem pedig az almának és a citrusféléknek.

![Váratlan besorolás képe](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához használja az alábbi útmutatót a változatosabb képek betanításához: különböző szögekből, hátterekből, objektummérettel, csoportokkal és egyéb változatokkal rendelkező képeket biztosít.

## <a name="data-quantity"></a>Adatmennyiség

A betanítási képek száma a legfontosabb tényező. Azt javasoljuk, hogy címkénként legalább 50 képet használjon kiindulási pontként. Kevesebb kép miatt nagyobb a túlszerelés kockázata, és bár a teljesítményszámok jó minőséget sugallnak, a modell valós adatokkal is megküzdhet. 

## <a name="data-balance"></a>Adategyenleg

Fontos figyelembe venni a betanítási adatok relatív mennyiségét is. Például 500 kép használata egy címkéhez és 50 kép egy másik címkéhez, kiegyensúlyozatlan betanítási adatkészletet eredményez. Ez azt eredményezi, hogy a modell pontosabban előre jelzi az egyik címkét, mint a másik. Valószínűleg jobb eredményeket fog látni, ha legalább 1:2 arányt tart fenn a legkevesebb képet mutató címke és a legtöbb képet mutató címke között. Ha például a legtöbb képet felkereső címke 500 képet mutat be, a legkevesebb képet lehetővé tévő címke betanításához legalább 250 képpel kell rendelkeznie.

## <a name="data-variety"></a>Adatfajta

Ügyeljen arra, hogy olyan képeket használjon, amelyek reprezentatívak arra nézve, hogy mit küld be az osztályozónak a normál használat során. Ellenkező esetben az osztályozó megtanulhatja, hogy a képek közös tulajdonságai alapján előrejelzéseket készítsen. Ha például alma és citrus félék osztályozóját készíti, és a kezében lévő almákról és a citrusfélékről készített képeket fehér lemezeken használta, az osztályozó indokolatlan jelentőséget adhat a kéznek a lemezeknek, nem pedig az almának és a citrusféléknek.

![Váratlan besorolás képe](./media/getting-started-improving-your-classifier/unexpected.png)

A probléma megoldásához adjon meg különböző képeket annak érdekében, hogy az osztályozó jól általánosíthassa. Az alábbiakban felsorolunk néhány módot arra, hogy változatosabbá tegye a képzési készletet:

* __Háttér:__ Adja meg a képeket az objektum előtt a különböző háttérrel. A természetes környezetben készült fényképek jobbak, mint a semleges háttér előtt készült fényképek, mivel több információt nyújtanak az osztályozószámára.

    ![Háttérminták képe](./media/getting-started-improving-your-classifier/background.png)

* __Világítás:__ A képek változatos megvilágítású (azaz hozott vaku, nagy expozíció, és így tovább), különösen, ha a képek használt előrejelzés különböző világítás. Az is hasznos, ha különböző telítettségű, színárnyalatos és fényerőű képeket használ.

    ![A világítási minták képe](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektum mérete:__ Adjon meg olyan képeket, amelyekben a tárgyak mérete és száma változó (például egy banáncsomók fényképe és egy banán közelképe). A különböző méretezés segít az osztályozó általánosításának.

    ![A méretminták képe](./media/getting-started-improving-your-classifier/size.png)

* __Kamera szöge:__ Különböző kameraállásokkal készített képek biztosítása. Ha az összes fényképet rögzített kamerákkal (például térfigyelő kamerákkal) kell készíteni, ügyeljen arra, hogy minden rendszeresen&mdash;előforduló objektumhoz más címkét rendeljen, hogy ne legyen túlillesztve a nem kapcsolódó tárgyak (például lámpaoszlopok) fő jellemzőjeként való túlszereléshez.

    ![Szögminták képe](./media/getting-started-improving-your-classifier/angle.png)

* __Stílus:__ Adjon képeket az azonos osztály különböző stílusairól (például ugyanazon gyümölcs különböző fajtáiról). Ha azonban drasztikusan eltérő stílusú objektumai vannak (például mickey egér vs. egy valós egér), javasoljuk, hogy külön osztályként címkézze fel őket, hogy jobban képviselje a különböző jellemzőket.

    ![Stílusminták képe](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negatív képek

A projekt egy bizonyos pontján előfordulhat, hogy _negatív mintákat_ kell hozzáadnia az osztályozó pontosabbsá teszi. A negatív minták azok, amelyek nem egyeznek meg a többi címkével. Amikor feltölti ezeket a képeket, alkalmazza rájuk a speciális **Negatív** címkét.

> [!NOTE]
> A Custom Vision Service támogatja az automatikus negatív képkezelést. Ha például szőlő- és banánosztályozót épít, és egy cipő képét küldi el előrejelzésre, az osztályozónak a képet 0% közelében kell pontszámoznia mind a szőlő, mind a banán esetében.
> 
> Másrészt, azokban az esetekben, ahol a negatív képek csak egy változata a képek et a képzés, valószínű, hogy a modell minősíteni a negatív képeket, mint a címkézett osztály miatt a nagy hasonlóságok. Például, ha van egy narancs vs grapefruit osztályozó, és a takarmány egy képet a clementine, akkor pontszám a clementine, mint egy narancs, mert sok jellemzője a clementine hasonlítanak a narancs. Ha a negatív képek ilyen jellegűek, javasoljuk, hogy hozzon létre egy vagy több további címkét (például **az Egyéb),** és a negatív képeket ezzel a címkével címkézze fel a betanítás során, hogy a modell jobban meg tudja különböztetni ezeket az osztályokat.

## <a name="use-prediction-images-for-further-training"></a>Előrejelzési képek használata továbbképzéshez

Ha a képosztályozót az előrejelzési végpontra küldött képek használatával használja vagy teszteli, a Custom Vision szolgáltatás tárolja ezeket a képeket. Ezután használhatja őket a modell fejlesztéséhez.

1. Az osztályozónak küldött képek megtekintéséhez nyissa meg az [Egyéni látás weblapot](https://customvision.ai), nyissa meg a projektet, és válassza az __Előrejelzések__ lapot. Az alapértelmezett nézet az aktuális iterációból származó képeket jeleníti meg. Az __Iterációs legördülő__ menüben megtekintheti a korábbi ismétlések során elküldött képeket.

    ![képernyőkép az előrejelzések lapról, képek nézetben](./media/getting-started-improving-your-classifier/predictions.png)

2. Vigye az egérmutatót egy kép fölé az osztályozó által előre jelzett címkék megtekintéséhez. A képek úgy vannak rendezve, hogy azok, amelyek a legtöbb fejlesztést hozhatják az osztályozóhoz, a tetején vannak felsorolva. Ha más rendezési módszert szeretne használni, válasszon a __Rendezés__ szakaszban. 

    Ha képet szeretne hozzáadni a meglévő betanítási adatokhoz, jelölje ki a képet, állítsa be a megfelelő címkét(eke)t, majd kattintson a __Mentés és bezárás gombra.__ A rendszer eltávolítja a képet __az előrejelzésekből,__ és hozzáadja a betanítási képek készletéhez. Megtekintheti a __Betanítási képek__ lapon.

    ![A címkézési oldal képe](./media/getting-started-improving-your-classifier/tag.png)

3. Ezután a __Vonat__ gombbal újratanítja az osztályozót.

## <a name="visually-inspect-predictions"></a>Szemrevételezéssel vizsgálja meg az előrejelzéseket

A képelőrejelzések vizsgálatához lépjen a __Betanítási képek__ lapra, válassza ki az előző betanítási iterációt az **Iterációs legördülő** menüben, és ellenőrizzen egy vagy több címkét a **Címkék** szakaszban. A nézetnek most egy piros mezőt kell megjelenítenie minden olyan kép körül, amelyhez a modell nem tudta megfelelően megjósolni az adott címkét.

![Az iterációs előzmények képe](./media/getting-started-improving-your-classifier/iteration.png)

Előfordulhat, hogy a szemrevételezéses ellenőrzés azonosítja azokat a mintákat, amelyeket aztán további betanítási adatok hozzáadásával vagy a meglévő betanítási adatok módosításával kijavíthatja. Előfordulhat például, hogy az alma és a lime osztályozója helytelenül az összes zöld almát lime-ként címkézi. Ezt követően a zöld almák címkézett képeit tartalmazó betanítási adatok hozzáadásával és biztosításával javíthatja a problémát.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban számos technikát tanult meg az egyéni képbesorolási modell pontosabbsá tévává. Ezután megtudhatja, hogyan tesztelheti a képeket programozott módon az előrejelzési API-ba való elküldésükkel.

> [!div class="nextstepaction"]
> [Előrejelzési API használata](use-prediction-api.md)
