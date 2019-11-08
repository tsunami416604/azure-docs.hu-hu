---
title: NLP-Cognitive Services használata a beszélgetések gazdagítása érdekében
titleSuffix: Azure Cognitive Services
description: Cognitive Services két természetes nyelvi feldolgozási szolgáltatást biztosít, Language Understanding és QnA Maker, amelyek mindegyike más célra szolgál. Ismerje meg, hogy mikor kell használni az egyes szolgáltatásokat, és hogyan kell egymással kiegészíteni őket.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818201"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Természetes nyelvi feldolgozási (NLP) Cognitive Services használata a bot-beszélgetések gazdagítása érdekében

Cognitive Services két természetes nyelvi feldolgozási szolgáltatást biztosít, [Language Understanding](what-is-luis.md) és [QnA Maker](../qnamaker/overview/overview.md), amelyek mindegyike más célra szolgál. Ismerje meg, hogy mikor kell használni az egyes szolgáltatásokat, és hogyan kell egymással kiegészíteni őket. 

A természetes nyelvi feldolgozás (NLP) lehetővé teszi az ügyfélalkalmazás, például a csevegési robot használatát a felhasználókkal való munkavégzéshez természetes nyelven. A felhasználó mondatot vagy kifejezést ír be. A felhasználó szövege nem lehet rossz nyelvtan, helyesírás és írásjel. A kognitív szolgáltatás a felhasználói mondaton keresztül is képes működni, így információt ad vissza, amikor a csevegési robot segítségére van szüksége a felhasználónak. 

## <a name="cognitive-services-with-nlp"></a>NLP-Cognitive Services

Language Understanding (LUIS) és QnA Maker biztosítanak NLP számára. Az ügyfélalkalmazás természetes nyelvi szöveget küld. A szolgáltatás végrehajtja a szöveget, feldolgozza és visszaadja az eredményt. 

## <a name="when-to-use-each-service"></a>Mikor kell használni az egyes szolgáltatásokat

Language Understanding (LUIS) és QnA Maker különböző problémák megoldására. LUIS meghatározza, hogy a felhasználó szövegének (azaz a teljes kifejezésnek) szándéka, míg QnA Maker meghatározza a felhasználó szövegére (azaz lekérdezésre) adott választ. 

A megfelelő szolgáltatás kiválasztásához meg kell ismernie az ügyfélalkalmazástól érkező felhasználói szöveget, valamint azt, hogy milyen információkra van szükség az ügyfélalkalmazás számára a kognitív szolgáltatástól.

Ha a csevegési robot megkapja a `How do I get to the Human Resources building on the Seattle North campus?`szöveget, az alábbi táblázat segítségével megismerheti, hogy az egyes szolgáltatások hogyan működnek együtt a szöveggel.

|Szolgáltatás|Ügyfélalkalmazás határozza meg|
|--|--|
|LUIS|**Meghatározza a felhasználó szövegének szándékát** – a szolgáltatás nem adja vissza a kérdésre adott választ. Ez a szöveg például a `FindLocation` szándéknak megfelelőként van osztályozva.<br>|
|QnA Maker|Egy egyéni Tudásbázisból származó **kérdésre adott válasz visszaadása** . Ez a szöveg például a `Get on the #9 bus and get off at Franklin street`statikus szöveges válaszával kapcsolatos kérdésként van meghatározva.|
|||

## <a name="when-do-you-use-luis"></a>Mikor használja a LUIS-t? 

Akkor használja a LUIS-t, ha ismernie kell a Kimondás szándékát egy folyamat részeként a csevegési robotban. A példa szövege, `How do I get to the Human Resources building on the Seattle North campus?`, ha tudja, hogy a felhasználó szándéka a hely megkeresése, megadhatja a válasz részleteit (az entitások kihúzásával) egy másik szolgáltatáshoz, például egy szállítási kiszolgálóhoz, hogy megkapja a választ. 

Nem kell összekapcsolnia a LUIS és a QnA Makert a szándék meghatározásához. 

Összekapcsolhatja a két szolgáltatást ehhez a kilépéshez, ha a csevegési robotnak a szándékok és entitások alapján kell feldolgoznia a szöveget (a LUIS használatával), valamint meg kell keresnie az adott statikus szöveges választ (QnA Maker használatával).

## <a name="when-do-you-use-qna-maker"></a>Mikor használja a QnA Maker? 

A QnA Maker akkor használja, ha a válaszok statikus tudásbázisa van. Ez a Tudásbázis az Ön igényeinek megfelelően egyedi, amelyet dokumentumok, például PDF-EK és URL-címek használatával készített. 

Ha folytatja a példát, `How do I get to the Human Resources building on the Seattle North campus?`, küldje el a szöveget lekérdezésként a közzétett QnA Maker-szolgáltatásba, és fogadja el a legjobb választ. 

Nincs szükség a LUIS és a QnA Maker összekapcsolására a kérdés megválaszolásának meghatározásához.

Összekapcsolhatja a két szolgáltatást ehhez a kilépéshez, ha a csevegési robotnak a szándékok és entitások alapján kell feldolgoznia a szöveget (a LUIS használatával), valamint a válasz megtalálását (QnA Maker használatával).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Mindkét szolgáltatás használata, ha a Tudásbázis hiányos

Ha felépíti a QnA Maker tudásbázist, de tudja, hogy a tárgy tartománya módosul (például az időben megjelenő információk), akkor a LUIS és a QnA Maker szolgáltatásokat kombinálhatja. Ez lehetővé teszi a tudásbázisban található információk használatát, de a LUIS használatával is meghatározhatja a felhasználó szándékát. Ha az ügyfélalkalmazás szándéka van, kérheti a kapcsolódó adatokat más forrásokból is. 

Az ügyfélalkalmazás a következő pontszámok esetében is figyelnie kell a LUIS és a QnA Maker válaszokat. Ha a QnA Maker pontszáma valamilyen tetszőleges küszöbérték alá esik, használja a LUIS által visszaadott szándékot és entitási adatokat, hogy átadja az adatokat egy külső szolgáltatásnak.

Ha folytatja a példában szereplő szöveget, `How do I get to the Human Resources building on the Seattle North campus?`, tegyük fel, hogy QnA Maker alacsony megbízhatósági pontszámot ad vissza. A LUIS, `FindLocation` és a kinyert entitások, például a `Human Resources building` és a `Seattle North campus`által visszaadott szándék használatával egy másik válaszhoz is elküldheti ezeket az információkat egy leképezési vagy keresési szolgáltatásba. 

Ezt a külső féltől származó választ is bemutathatja a felhasználónak az érvényesítéshez. A felhasználó jóváhagyása után visszatérhet a QnA Makerra, és hozzáadhatja az adatokat az ismeretek növeléséhez. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Mindkét szolgáltatást használhatja, ha a csevegő robotjának további információra van szüksége

Ha a csevegési robotnak több információra van szüksége, mint amennyit a szolgáltatás nyújt, a döntési fa folytatásához használja mindkét szolgáltatást, és dolgozza fel mindkét választ az ügyfélalkalmazás számára. 

A robot Framework **[elküldési parancssori](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** felületének használatával létrehozhat egy folyamatot, amely mindkét szolgáltatással használható. Ez az eszköz egy olyan legjobb LUIS-alkalmazást hoz létre, amely a LUIS és a QnA Maker között alárendelt alkalmazásként való kiszállításra szolgál. 

Az ilyen típusú csevegő robotmegvalósításához használja a robot [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) Builder-mintát, a következőt, vagy a [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)-t. 

## <a name="best-practices"></a>Ajánlott eljárások

Ajánlott eljárások megvalósítása az egyes szolgáltatásokhoz:

* [Luis](luis-concept-best-practices.md) – ajánlott eljárások
* [QnA Maker](../qnamaker/concepts/best-practices.md) ajánlott eljárások

## <a name="see-also"></a>Lásd még:

* [Language Understanding (LUIS)](what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Küldési CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Robot Framework-minták](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot Emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework webes csevegés](https://github.com/microsoft/BotFramework-WebChat)