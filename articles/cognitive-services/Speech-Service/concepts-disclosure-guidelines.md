---
title: Közzétételi tervre vonatkozó irányelvek
titleSuffix: Azure Cognitive Services
description: Bevezetés a közzétételi tervezési irányelvekbe és a közzétételi szint kiértékelésére.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: angle
ms.openlocfilehash: 7dffa3d9f6e96adc6146a1059e7360c77926ece3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837378"
---
# <a name="disclosure-design-guidelines"></a>Közzétételi tervre vonatkozó irányelvek
Ismerje meg, hogyan hozhat létre és tarthat fenn megbízhatóságot az ügyfelekkel a hangélmény szintetikus természetének átláthatósága érdekében.

## <a name="what-is-disclosure"></a>Mi a nyilvánosságra hozatal?

A nyilvánosságra hozatal azt jelenti, hogy az&#39;emberek tudják, hogy a szintetikusan generált hangvételt vagy meghallgatást végeznek.

## <a name="why-is-disclosure-necessary"></a>Miért szükséges a közzététel?

A számítógép által generált hang szintetikus eredetének közzétételéhez viszonylag új üzenet szükséges. A múltban a számítógép által generált hangok nyilvánvalóan az volt, hogy egy valós személynek soha senki nem lenne hibát kitéve. A szintetikus hangok realizmusa azonban minden nap javul, és egyre kevésbé megkülönböztethető az emberi hangtól.

## <a name="goals"></a>Célok
A szintetikus hangélmények tervezésekor a következő elveket kell szem előtt tartani:

**Megbízhatóság megerősítése**
<br>Tervezze meg a Turing-teszt elvégzésének szándékát a felhasználói élmény romlása nélkül. Hagyja, hogy a felhasználók egy szintetikus hanggal kommunikálnak, miközben lehetővé teszik, hogy zökkenőmentesen folytassák a felhasználói élményt.

**Alkalmazkodás a használati környezethez**
<br>Megtudhatja, hogy mikor, hol és hogyan fog kommunikálni a felhasználók a szintetikus hanggal, hogy a megfelelő típusú közzétételi lehetőséget biztosítsák a megfelelő időben.

**Egyértelmű elvárások beállítása**
<br>Lehetővé teszi, hogy a felhasználók könnyedén felfedezzék és megértsék az ügynök képességeit. Lehetőség van arra, hogy további információkat nyújtson a szintetikus hangalapú technológiáról a kérelem alapján.

**Az ölelés sikertelen**
<br>Pillanatok alatt megerősítheti az ügynök képességeit.

## <a name="how-to-use-this-guide"></a>Az útmutató használata

Ez az útmutató segítséget nyújt annak meghatározásában, hogy mely közzétételi minták legyenek optimálisak a szintetikus hangélményhez. Ezt követően példákat kínálunk arra, hogy hogyan és mikor használja őket. Ezek a minták úgy vannak kialakítva, hogy maximalizálják az átláthatóságot a szintetikus beszédtel rendelkező felhasználóktól, miközben az emberi központú kialakításban maradnak.

A hangélmények kialakítására vonatkozó tervezési útmutatót figyelembe véve különösen a következőkre koncentrálunk:

1. [**Közzétételi felmérés**](#disclosure-assessment): a szintetikus hangélményhez ajánlott közzétételi típus megállapításának folyamata

2. [**Közzététel: példák**](concepts-disclosure-patterns.md)olyan közzétételi mintákra, amelyek alkalmazhatók a szintetikus hangalapú felhasználói élményre

3. [**Mikor kell kimutatni**](concepts-disclosure-patterns.md#when-to-disclose): az optimális pillanatot a felhasználói útvonalon való közzétételhez

## <a name="disclosure-assessment"></a>Közzétételi felmérés
Vegye figyelembe,&#39; hogy a felhasználók egy interakcióval kapcsolatos elvárásokat és azt a kontextust, amelyben a hangvételt tapasztalják. Ha a kontextus egyértelművé teszi, hogy a szintetikus hang &quot;beszél,&quot; a közzététel minimális, pillanatnyi vagy szükségtelen lehet. A közzétételt befolyásoló fő típusok közé tartozik a person Type, a forgatókönyv típusa és a kitettség szintje. Emellett segít megfontolni, hogy kik is figyelnek.

### <a name="understand-context"></a>Kontextus ismertetése

Ez a munkalap a szintetikus hangélmény kontextusának meghatározására használható. Ezt a következő lépésben fogja alkalmazni, ahol meghatározhatja a közzétételi szintet.

|                                    | A használat kontextusa                                                                                                                                                                                                                                                                                                                                                       | Lehetséges kockázatok & kihívások                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. persona típusa**               | **Ha a következők bármelyike érvényes, a persona a "Human-Like person" kategóriába tartozik:**<br><br><ul><li> A Persona valódi emberi személyt testesíti meg, függetlenül attól, hogy ez egy fiktív képviselet-e. (például a fénykép vagy a számítógép által generált megjelenítés egy valós személy)<br><br><li> A szintetikus hang egy széles körben felismerhető valódi személy (például híresség, politikai ábra) hangja alapján | Minél több emberi jellegű ábrázolást biztosít a Persona számára, annál valószínűbb, hogy egy felhasználó egy valós személyhez társítja azt, vagy ha úgy gondolja, hogy a tartalmat egy valós személy, nem pedig a számítógép hozza létre. </ul>                                                                                                                                                                      |
| **2. forgatókönyv típusa**            | **Ha a következők bármelyike érvényes, a hangélmény a "szenzitív" kategóriába illeszkedik:**<br><br><ul><li> Személyes adatok beszerzése vagy megjelenítése a felhasználótól <br><br> <li> Szórási idő – bizalmas Hírek/információk (például vészhelyzeti riasztás)<br><br><li> Célja, hogy segítsen a valódi embereknek kommunikálni egymással (például a személyes e-mailek/szövegek olvasása)<br><br> <li> Orvosi/egészségügyi segítséget nyújt </ul>            | Előfordulhat, hogy a szintetikus hang használata nem megfelelőnek vagy megbízhatónak tekinti az azokat használó személyeket, amikor a témakörök bizalmas, személyes vagy sürgős ügyekkel kapcsolatosak. Az is előfordulhat, hogy az empátia és a kontextus ismerete is azonos a valós emberi értékkel. |
| **3. expozíciós szint** |**A hangélmény nagy valószínűséggel a "magas" kategóriába illeszkedik, ha:** <br><br><ul><li>A felhasználó gyakran vagy hosszú ideig fogja hallani vagy kommunikálni a szintetikus hanggal </ul>                                                                                                                                                                             | A hosszú távú kapcsolatok kialakítása során még nagyobb az átláthatóság és a bizalom kiépítése a felhasználókkal.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Közzétételi szint meghatározása

A következő ábra segítségével meghatározhatja, hogy a szintetikus hangélmény magas vagy alacsony szintű közzétételt igényel-e a használat kontextusa alapján.

  ![Közzétételi értékelési diagram](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Segédanyagok

* [A hangalapú tehetségek közzététele](https://aka.ms/disclosure-voice-talent)
* [Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez](concepts-guidelines-responsible-deployment-synthetic.md)
* [A kapuzás áttekintése](concepts-gating-overview.md)

## <a name="next-steps"></a>További lépések

* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)
