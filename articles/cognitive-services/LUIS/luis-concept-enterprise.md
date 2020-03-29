---
title: Vállalati fogalmak - LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg a nagy MÉRETŰ LUIS-alkalmazások vagy több alkalmazás, köztük a LUIS és a QnA Maker tervezési koncepcióit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221061"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Vállalati stratégiák egy LUIS-alkalmazáshoz
Tekintse át ezeket a vállalati alkalmazás tervezési stratégiáit.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Ha a kvótán túli LUIS-kérelmeket vár

A LUIS rendelkezik egy havi kvótával, valamint egy másodpercenkénti kvótával, az Azure-erőforrás tarifacsomagja alapján. 

Ha a LUIS-alkalmazás kérelmezési aránya meghaladja az engedélyezett [kvótaarányt,](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)a következőket teheti:

* A terhelést több LUIS-alkalmazásra is el kell osztani ugyanazzal az [alkalmazásdefinícióval.](#use-multiple-apps-with-same-app-definition) Ez magában foglalja, opcionálisan, a LUIS egy [tárolóból](luis-container-howto.md)futó. 
* Hozzon létre és [rendeljen hozzá több kulcsot](#assign-multiple-luis-keys-to-same-app) az alkalmazáshoz. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Több alkalmazás használata ugyanazzal az alkalmazásdefinícióval
Exportálja az eredeti LUIS alkalmazást, majd importálja újra az alkalmazást külön alkalmazásokba. Minden alkalmazás saját alkalmazásazonosítóval rendelkezik. Amikor közzéteszi, ahelyett, hogy ugyanazt a kulcsot az összes alkalmazás, hozzon létre egy külön kulcsot minden alkalmazáshoz. Egyensúlyozza ki a terhelést az összes alkalmazásban, hogy egyetlen alkalmazás se terhelődhessen. [Add Application Insights](luis-tutorial-bot-csharp-appinsights.md) a használat figyeléséhez. 

Annak érdekében, hogy ugyanazt a felső szándékot az összes alkalmazás között, győződjön meg arról, hogy a szándék előrejelzése az első és a második szándék között elég széles ahhoz, hogy a LUIS nem zavaros, így az alkalmazások közötti különböző változatok utterances. 

A testvéralkalmazások betanításakor győződjön meg arról, hogy [az összes adatot betanítja.](luis-how-to-train.md#train-with-all-data)

Jelöljön ki egyetlen alkalmazást főalkalmazásként. Minden véleményezésre javasolt kimondott szöveget hozzá kell adni a fő alkalmazáshoz, majd vissza kell helyezni az összes többi alkalmazásba. Ez vagy az alkalmazás teljes exportálása, vagy a címkézett kimondott szöveg betöltése a fő kiszolgálóról a gyermekekre. A betöltés történhet a [LUIS-webhelyről](luis-reference-regions.md) vagy a szerzői API-ból egyetlen [utterance (kifejezés)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) vagy [köteg](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)esetén. 

Ütemezze be a [végpontkimondott szövegek](luis-how-to-review-endpoint-utterances.md) időszakos áttekintését, például kéthetente az aktív tanuláshoz, majd újraésképezze be és tegye közzé újra. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Több LUIS-kulcs hozzárendelése ugyanahhoz az alkalmazáshoz
Ha a LUIS-alkalmazás több végpontlekérést kap, mint amennyit az egyes kulcs kvótája megenged, hozzon létre és rendeljen hozzá további kulcsokat a LUIS alkalmazáshoz. Hozzon létre egy forgalomkezelőt vagy terheléselosztót a végponti lekérdezések kezeléséhez a végpontkulcsok között. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Ha a monolitikus alkalmazás rossz szándékot ad vissza
Ha az alkalmazás célja, hogy a felhasználói kimondott szövegek széles skáláját előre jelezje, fontolja meg a [feladási modell megvalósítását.](#dispatch-tool-and-model) Egy monolitikus alkalmazás feltörése lehetővé teszi a LUIS számára, hogy a leképezések között sikeresen összpontosítson az észlelési feladatok között, ahelyett, hogy a szülőalkalmazás és a gyermekalkalmazások leképezései között összezavarodna. 

Ütemezze a [végpont kimondott szövegének](luis-how-to-review-endpoint-utterances.md) időszakos áttekintését az aktív tanuláshoz, például kéthetente, majd újraés képezze be és tegye közzé újra. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Ha több mint 500 szándékra van szüksége
Tegyük fel, hogy egy több mint 500 szándékkal rendelkező irodai asszisztenst fejleszt. Ha 200 szándék kapcsolódik az értekezletek ütemezéséhez, 200 az emlékeztetőkről, 200 a munkatársakról szóló információk megszerzéséről, 200 pedig e-mailek küldésére, csoportos leképezésekre vonatkozik, hogy minden csoport egyetlen alkalmazásban legyen, majd hozzon létre egy felső szintű alkalmazást, amely tartalmazza az egyes szándékokat. Használja a [feladási modellt](#dispatch-tool-and-model) a legfelső szintű alkalmazás létrehozásához. Ezután változtassa meg a robotot, hogy használja a lépcsőzetes hívást, ahogy az a [diszpécsermodell oktatóanyagában](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)látható. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Ha több LUIS- és QnA-készítő alkalmazást kell kombinálnia
Ha több LUIS- és QnA-készítő alkalmazással rendelkezik, amelyeknek válaszolniuk kell egy robotra, a [feladási modell](#dispatch-tool-and-model) segítségével hozhatja létre a legfelső szintű alkalmazást.  Ezután változtassa meg a robotot, hogy használja a lépcsőzetes hívást, ahogy az a [diszpécsermodell oktatóanyagában](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)látható. 

## <a name="dispatch-tool-and-model"></a>Diszpécser eszköz és modell
A [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) alkalmazásban található [Dispatch][dispatch-tool] parancssori eszközzel több LUIS- és/vagy QnA Maker-alkalmazást egyesítsen egy szülő LUIS-alkalmazásba. Ez a megközelítés lehetővé teszi, hogy egy szülő tartomány, beleértve az összes téma és a különböző alárendelt tartományban külön alkalmazásokban. 

![A feladási architektúra fogalmi képe](./media/luis-concept-enterprise/dispatch-architecture.png)

A szülőtartomány a LUIS-ban `Dispatch` található az alkalmazások listájában elnevezett verzióval. 

A csevegőrobot megkapja az utterance (kifejezés) üzenetet, majd elküldi a szülő LUIS alkalmazás előrejelzéshez. A szülőalkalmazás legfelső előre jelzett leképezése határozza meg, hogy melyik LUIS-gyermekalkalmazás lesz a következő neve. A csevegési robot elküldi az utterance (kifejezés) a gyermek alkalmazás egy konkrétabb előrejelzést.

Ismerje meg, hogyan történik ez a hierarchia a hívások a Bot Builder v4 [diszpécser-alkalmazás-bemutató](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Szándékkorlátok a feladási modellben
A diszpécseralkalmazás 500 feladási forrással rendelkezik, ami maximum 500 leképezésnek felel meg. 

## <a name="more-information"></a>További információ

* [Bot kereteskeret SDK](https://github.com/Microsoft/botframework)
* [Feladási modell oktatóanyaga](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Diszpécser CLI](https://github.com/Microsoft/botbuilder-tools)
* Feladási modell robot mintája - [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>További lépések

* További információ [a kötegek teszteléséről](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
