---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604967"
---
A Cognitive Services két természetes nyelvi feldolgozási szolgáltatást biztosít, [a Language Understanding](../luis/what-is-luis.md) és a [QnA Maker](../qnamaker/overview/overview.md)szolgáltatást, amelyek mindegyike más célt szolgál. Tudja meg, mikor kell használni az egyes szolgáltatások, és hogyan bók egymást.

A természetes nyelvi feldolgozás (NLP) lehetővé teszi, hogy az ügyfélalkalmazás, például egy csevegőrobot természetes nyelven működjön együtt a felhasználókkal. A felhasználó beír egy mondatot vagy kifejezést. A felhasználó szövegének rossz nyelvtana, helyesírása és írásjeleke lehet. A Cognitive Service a felhasználói mondaton keresztül egyébként is dolgozhat, és a csevegőrobotnak a felhasználó nak segítségre van szüksége.

## <a name="cognitive-services-with-nlp"></a>Kognitív szolgáltatások Az NLP

A language understanding (LUIS) és a QnA Maker biztosítja az NLP-t. Az ügyfélalkalmazás természetes nyelvű szöveget küld. A szolgáltatás átveszi a szöveget, feldolgozza és visszaadja az eredményt.

## <a name="when-to-use-each-service"></a>Mikor kell használni az egyes szolgáltatások

A nyelvi megértés (LUIS) és a QnA Maker különböző problémákat old meg. A LUIS határozza meg a felhasználó szövegének (más néven utterance (utterance (a qna maker) szándékát, míg a QnA Maker a felhasználó szövegére (más néven lekérdezésre) adott választ.

Annak érdekében, hogy válassza ki a megfelelő szolgáltatást, meg kell értenie a felhasználói szöveget az ügyfélalkalmazásból érkező, és milyen információkat az ügyfélalkalmazás nak kell beszereznie a Cognitive Service.

Ha a csevegőrobot `How do I get to the Human Resources building on the Seattle North campus?`megkapja a szöveget, az alábbi táblázat segítségével megtudhatja, hogyan működik az egyes szolgáltatások a szöveggel.

|Szolgáltatás|Az ügyfélalkalmazás határozza meg|
|--|--|
|LUIS|**Meghatározza a felhasználó szövegszándékát** - a szolgáltatás nem adja vissza a választ a kérdésre. Ez a szöveg például a `FindLocation` szándéknak megfelelőnek minősül.<br>|
|QnA Maker|**A kérdésre adott választ** egyéni tudásbázisból adja vissza. Ez a szöveg például kérdésként van meghatározva `Get on the #9 bus and get off at Franklin street`a statikus szövegválaszával.|
|||

## <a name="when-do-you-use-luis"></a>Mikor használja a LUIS-t?

A LUIS használata, ha tudnia kell, hogy a szándék a kimondott ság egy folyamat részeként a csevegőrobot. Folytatva a példa `How do I get to the Human Resources building on the Seattle North campus?`szövegét, ha tudja, hogy a felhasználó szándéka az, hogy megtalálja a helyét, átadhatja az utterance (kihúzott entitásokkal) részleteit egy másik szolgáltatásnak, például egy szállítási kiszolgálónak, hogy megkapja a választ.

A szándék meghatározásához nem kell kombinálnia a LUIS és a QnA Maker.You don't need to combine LUIS and QnA Maker to determine intent.

Előfordulhat, hogy összekapcsolják a két szolgáltatás az utterance (kifejezés), ha a csevegőrobot kell feldolgozni a szöveget a szándékok és entitások (luis), valamint megtalálni az adott statikus szöveges válasz (a QnA Maker használatával).

## <a name="when-do-you-use-qna-maker"></a>Mikor használja a QnA Makert?

Használja a QnA Makert, ha statikus tudásbázissal rendelkezik a válaszokhoz. Ez a tudásbázis az igényeinek megfelelően készült, amelyet olyan dokumentumokkal készített, mint a PDF-fájlok és az URL-ek.

Folytatva a példa `How do I get to the Human Resources building on the Seattle North campus?`utterance (kifejezés) , küldje el a szöveget, lekérdezésként, a közzétett QnA Maker szolgáltatás, és megkapja a legjobb választ.

A kérdésre adott válasz meghatározásához nem kell kombinálnia a LUIS és a QnA Maker.You don't need to combine LUIS and QnA Maker to determine the answer to the question.

Előfordulhat, hogy összekapcsolják a két szolgáltatás az utterance (kifejezés), ha a csevegőrobot kell feldolgozni a szöveget a szándékok és entitások (luis), valamint megtalálni a választ (qna maker használatával).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Mindkét szolgáltatás használata, ha a tudásbázis nem teljes

Ha a QnA Maker tudásbázisát építi, de tudja, hogy a tárgytartomány változik (például időszerű információk), kombinálhatja a LUIS és a QnA Maker szolgáltatásokat. Ez lehetővé teszi, hogy a tudásbázisban lévő információkat használja, de a LUIS-t is használja a felhasználó szándékának meghatározásához. Miután az ügyfélalkalmazás nak megvan a szándéka, kérhet releváns információkat egy másik forrásból.

Az ügyfélalkalmazásnak figyelnie kell a LUIS és a QnA Maker válaszait a pontszámokhoz. Ha a QnA Maker pontszáma tetszőleges küszöbérték alatt van, használja a luis által visszaadott szándék- és entitásadatokat az adatok harmadik félnek történő továbbadására.

Folytatva a példaszöveget, tegyük fel, `How do I get to the Human Resources building on the Seattle North campus?`hogy a QnA Maker alacsony megbízhatósági pontszámot ad vissza. Használja a luis visszaadott szándékot, `FindLocation` és a `Human Resources building` `Seattle North campus`kinyert entitásokat, például és a , hogy ezeket az információkat egy leképezési vagy keresési szolgáltatásnak küldje el egy másik válaszért.

Ezt a harmadik féltől származó választ érvényesítés céljából bemutathatja a felhasználónak. Miután rendelkezik a felhasználó jóváhagyásával, visszatérhet a QnA Maker-hez, és hozzáadhatja az információkat a tudás bővítéséhez.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Mindkét szolgáltatást használja, ha a csevegőrobotnak több információra van szüksége

Ha a csevegőrobotnak több információra van szüksége, mint amennyit bármelyik szolgáltatás nyújt, a döntési fán keresztül folytathatja, használja mindkét szolgáltatást, és dolgozza fel mindkét választ az ügyfélalkalmazásban.

A Bot framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** eszközzel mindkét szolgáltatással való együttműködésre irányuló folyamatot hozhat létre. Ez az eszköz egy felső LUIS-alkalmazás leképezéseket hoz létre, amelyek a LUIS és a QnA Maker között gyermekalkalmazásokként szállítanak.

Használja a Bot builder minta, **NLP a feladás,** a [C #](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) vagy [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), az ilyen típusú chat bot.

## <a name="best-practices"></a>Ajánlott eljárások

Az egyes szolgáltatásokra vonatkozó gyakorlati tanácsok megvalósítása:

* [Luis](../luis/luis-concept-best-practices.md) gyakorlati tanácsok
* [A QnA Maker](../qnamaker/concepts/best-practices.md) bevált módszerei

## <a name="see-also"></a>Lásd még

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Diszpécser CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot keretrendszer minták](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure-robotszolgáltatás](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot emulátor](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot keretrendszer webes chat](https://github.com/microsoft/BotFramework-WebChat)