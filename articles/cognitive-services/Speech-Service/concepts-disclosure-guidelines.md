---
title: Közzétételi tervezési irányelvek
titleSuffix: Azure Cognitive Services
description: Bevezetés a közzétételi tervezési iránymutatásokba és a közzétételi szint értékelésébe.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776385"
---
# <a name="disclosure-design-guidelines"></a>Közzététel-tervezési irányelvek
Ismerje meg, hogyan építheti ki és tarthatja fenn a bizalmat az ügyfelekkel azáltal, hogy átláthatóan kezeli a hangélmény szintetikus jellegét.

## <a name="what-is-disclosure"></a>Mi a nyilvánosságra hozatal?

A közzététel egy olyan eszköz, amely tudatja az emberekkel, hogy&#39;, hogy újra kommunikáljanak egy szintetikusan generált hanggal, vagy meghallgassanak egy hangot.

## <a name="why-is-disclosure-necessary"></a>Miért van szükség a nyilvánosságra hozatalra?

A számítógép által generált hang szintetikus eredetének nyilvánosságra hozatalának szükségessége viszonylag új. A múltban, a számítógép által generált hangok nyilvánvalóan, hogy senki sem fogja összetéveszteni őket egy valós személy. A szintetikus hangok realizmusa azonban napról napra javul, és egyre megkülönböztethetetlenebbé válik az emberi hangoktól.

## <a name="goals"></a>Célok
Ezeket az elveket kell szem előtt tartani a szintetikus hangélmények tervezésekor:

**A bizalom erősítése**
<br>Tervezze meg azzal a szándékkal, hogy megbukjon a Turing-teszten anélkül, hogy lealacsonyítana a tapasztalatot. Hagyja, hogy a felhasználók a tény, hogy ők kölcsönhatásban áll egy szintetikus hang, miközben lehetővé teszi számukra, hogy zökkenőmentesen vegyenek részt a tapasztalat.

**Alkalmazkodás a használati környezethez**
<br>Ismerje meg, hogy mikor, hol és hogyan lépnek kapcsolatba a felhasználók a szintetikus hanggal, hogy a megfelelő típusú közzétételt biztosítsák a megfelelő időben.

**Egyértelmű elvárások**
<br>Lehetővé teszi a felhasználók számára, hogy könnyen felderítsék és megértsék az ügynök képességeit. Kérésre lehetőséget kínálnak arra, hogy többet tudjon meg a szintetikus hangtechnológiáról.

**Embrace hiba**
<br>Használja a sikertelen pillanatokat az ügynök képességeinek megerősítéséhez.

## <a name="how-to-use-this-guide"></a>Az útmutató használata

Ez az útmutató segít meghatározni, hogy mely közzétételi minták felelnek meg a legjobban a szintetikus hangélménynek. Ezután példákat mutatunk be arra, hogyan és mikor kell használni őket. Minden ilyen minták célja, hogy maximalizálja az átláthatóságot a felhasználók számára a szintetikus beszéd, miközben hű az ember-központú design.

Figyelembe véve a hatalmas testület tervezési útmutatást hangtapasztalatok, mi itt kifejezetten a következőkre összpontosítunk:

1. [**Közzétételi értékelés**](#disclosure-assessment): A szintetikus hangélményhez ajánlott közzétételi típus meghatározására szolgáló folyamat

2. [**Hogyan lehet közzétenni:**](concepts-disclosure-patterns.md)Példák a nyilvánosságra hozatali minták, hogy lehet alkalmazni, hogy a szintetikus hang tapasztalat

3. [**Mikor kell nyilvánosságra hozni:**](concepts-disclosure-patterns.md#when-to-disclose)Optimális pillanatok a felhasználói út során

## <a name="disclosure-assessment"></a>A közzététel értékelése
Vegye figyelembe, hogy a felhasználók&#39; egy interakcióval kapcsolatos elvárásokat, és azt a környezetet, amelyben megtapasztalják a hangot. Ha a kontextus egyértelművé teszi, &quot;hogy&quot; a szintetikus hang beszél, közzététel lehet minimális, pillanatnyi, vagy akár felesleges. A közzétételt befolyásoló környezetfőtípusai közé tartozik a személytípusa, a forgatókönyvtípusa és az expozíció szintje. Az is segít, hogy fontolja meg, aki lehet hallgatni.

### <a name="understand-context"></a>A környezet megértése

Ezzel a munkalappal meghatározhatja a szintetikus hangélmény környezetét. Ezt a következő lépésben fogja alkalmazni, ahol meghatározza a közzétételi szintet.

|                                    | A használat kontextusa                                                                                                                                                                                                                                                                                                                                                       | Lehetséges kockázatok & kihívások                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Persona típus**               | **Ha az alábbiak bármelyike jelentkezik, az Ön személyisége az "Emberszerű Persona" kategóriába tartozik:**<br><br><ul><li> Persona testesíti meg egy igazi ember, hogy ez egy fiktív képviselet, vagy sem. (pl. fénykép vagy egy valós személy számítógép pelenka által létrehozott renderelése)<br><br><li> A szintetikus hang egy széles körben felismerhető valós személy (pl. híresség, politikai figura) hangján alapul. | Minél több emberszerű ábrázolást ad a személyiségének, annál valószínűbb, hogy a felhasználó egy valós személyhez társítja, vagy azt hiszi, hogy a tartalmat nem a számítógép által generált, hanem egy valós személy mondja. </ul>                                                                                                                                                                      |
| **2. Forgatókönyv típusa**            | **Ha az alábbiak bármelyike vonatkozik, a hangélmény az "Érzékeny" kategóriába tartozik:**<br><br><ul><li> Személyes adatok beszerzése vagy megjelenítése a felhasználótól <br><br> <li> Időérzékeny híreket/információkat sugároz (pl. vészhelyzeti riasztás)<br><br><li> Célja, hogy segítse a valódi emberek et kommunikálni egymással (pl. olvassa a személyes e-mailek / szövegek)<br><br> <li> Orvosi/egészségügyi segítséget nyújt </ul>            | A szintetikus hang használata nem feltétlenül tűnik megfelelőnek vagy megbízhatónak az azt használó emberek számára, amikor a témák érzékeny, személyes vagy sürgős ügyekhez kapcsolódnak. Azt is elvárják, ugyanolyan szintű empátia és kontextuális tudatosság, mint egy igazi ember. |
| **3. Az expozíció szintje** |**A hangélmény valószínűleg a "Magas" kategóriába illeszkedik, ha:** <br><br><ul><li>A felhasználó gyakran vagy hosszú ideig hallja vagy kölcsönhatásba lép a szintetikus hanggal </ul>                                                                                                                                                                             | A hosszú távú kapcsolatok kialakításakor még nagyobb az átláthatóság és a felhasználókkal való bizalomépítés fontossága.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Közzétételi szint meghatározása

Az alábbi ábrán meghatározhatja, hogy a szintetikus hangélmény a használati környezet alapján magas vagy alacsony szintű közzétételt igényel-e.

  ![Közzétételi értékelési diagram](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Referenciadokumentumok

* [Közzététel a Voice Talent számára](https://aka.ms/disclosure-voice-talent)
* [A szintetikus hangtechnológia felelősségteljes bevezetésére vonatkozó irányelvek](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating – áttekintés](concepts-gating-overview.md)

## <a name="next-steps"></a>További lépések

* [Közzététel-tervezési minták](concepts-disclosure-patterns.md)
