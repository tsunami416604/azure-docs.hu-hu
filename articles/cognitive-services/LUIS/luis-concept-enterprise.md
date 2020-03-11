---
title: Vállalati fogalmak – LUIS
titleSuffix: Azure Cognitive Services
description: Nagy LUIS vagy a több olyan alkalmazásokat, beleértve a LUIS és a QnA Maker együtt tervezési fogalmak ismertetése.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361125"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Vállalati stratégiák a LUIS-alkalmazások
Tekintse át ezeket a vállalati alkalmazások tervezési stratégiák.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Ha a kvóta túl LUIS-kérelmek várt

A LUIS havi kvótával és másodpercenkénti kvótával rendelkezik az Azure-erőforrás díjszabási szintjétől függően. 

Ha a LUIS-alkalmazásra vonatkozó kérelmek aránya meghaladja az engedélyezett [kvóta mértékét](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), a következőket teheti:

* Terjessze a terhelést több LUIS-alkalmazásba [ugyanazzal az alkalmazás-definícióval](#use-multiple-apps-with-same-app-definition). Ez magában foglalja a LUIS egy [tárolóból](luis-container-howto.md)való futtatását is. 
* Hozzon létre és [rendeljen több kulcsot](#assign-multiple-luis-keys-to-same-app) az alkalmazáshoz. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Több alkalmazás használata azonos app-definíció
Az eredeti LUIS alkalmazás exportálása, majd importálja a az alkalmazás különböző alkalmazás be újra. Minden alkalmazás rendelkezik a saját alkalmazás azonosítóját. Ha közzéteszi, alkalmazásnévnek, ugyanazzal a kulccsal használata helyett hozzon létre minden alkalmazáshoz külön kulcsnak. A terhelés kiegyenlítése alkalmazásnévnek úgy, hogy nincs egyetlen alkalmazás túlterhelte van. [Application Insights](luis-tutorial-bot-csharp-appinsights.md) hozzáadása a használat figyeléséhez. 

Annak érdekében, hogy az első közötti összes alkalmazás ugyanazon felső célja, ellenőrizze, hogy az első és második célja között szándék előrejelzési van elég széles ahhoz, hogy a LUIS ne legyen összetéveszteni őket, között kisebb eltérések utterances alkalmazások különböző eredményt. 

A testvéri alkalmazások betanításakor ügyeljen arra, hogy az [összes adattal betanítsa](luis-how-to-train.md#train-with-all-data).

Egyetlen alkalmazás jelölhetnek ki a főkiszolgáló. Amely a felülvizsgálatra javasolt megszólalásokat kell adva a fő alkalmazáshoz, majd visszahelyezi a többi alkalmazást is. Ez az vagy egy teljes exportálás, az alkalmazás vagy a betöltés címkézett megcímkézzen a főágból a gyermekek. A betöltés történhet a [Luis](luis-reference-regions.md) webhelyről vagy a SZERZŐi API-ból [egyetlen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) kiíráshoz vagy egy [köteghez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Ütemezzen rendszeres áttekintést, például kéthetente, az aktív tanulás [végpont-hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) , majd végezze el a betanítást és az újbóli közzétételt. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Ugyanazt az alkalmazást a kulcsok több LUIS hozzárendelése
Ha a LUIS-alkalmazás több végpontot, a találatok, mint az egyetlen kulcs kvótája engedélyez, és társítsa további kulcsok a LUIS alkalmazás kap. Hozzon létre egy traffic manager vagy a terheléselosztó a végpont lekérdezések kezelése a végpont kulcsok között. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Ha a monolitikus alkalmazások adja vissza nem megfelelő leképezés
Ha az alkalmazás célja, hogy előre megjósolja a felhasználói hosszúságú kimondott szöveg széles körét, érdemes lehet megvalósítani a [küldő modellt](#dispatch-tool-and-model). LUIS fókusz észlelési sikeresen helyett első nem biztos a szülő alkalmazás közötti leképezések és az alárendelt alkalmazások közötti leképezések között egy monolitikus alkalmazások használhatatlanná tévő lehetővé teszi. 

Ütemezze a [végpontok hosszúságú kimondott szöveg rendszeres áttekintését](luis-how-to-review-endpoint-utterances.md) az aktív tanuláshoz, például kéthetente, majd az újratanításhoz és az újbóli közzétételhez. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Ha szüksége van a több mint 500 szándékot
Tegyük fel, hogy olyan Office-Segédet fejleszt, amelynek több mint 500 szándéka van. 200 leképezések értekezleteit vonatkoznak, ha emlékeztetők készül a 200-as, 200-as készül a munkatársai, kapcsolatos információk lekérése és e-mailt küld a rendszer 200-as, csoport szándékot, hogy minden csoport egyetlen alkalmazást, majd hozzon létre egy legfelső szintű alkalmazást, amely tartalmazza minden egyes szándékot. A legfelső szintű alkalmazás létrehozásához használja a [küldő modellt](#dispatch-tool-and-model) . Ezután módosítsa a robotot a lépcsőzetes hívás használatára a [küldő modell oktatóanyagában](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)látható módon. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Mikor kell egyesíteni több LUIS és a QnA maker alkalmazások
Ha több LUIS-és QnA-készítő alkalmazásra van szüksége, amelyeknek egy robotra kell válaszolnia, használja a [küldő modellt](#dispatch-tool-and-model) a legfelső szintű alkalmazás létrehozásához.  Ezután módosítsa a robotot a lépcsőzetes hívás használatára a [küldő modell oktatóanyagában](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)látható módon. 

## <a name="dispatch-tool-and-model"></a>Küldő eszköz és a modell
A [botbuilder témakörben-eszközökben](https://github.com/Microsoft/botbuilder-tools) található [küldő][dispatch-tool] parancssori eszköz használatával több Luis-és/vagy QnA Maker-alkalmazást egyesítheti egy szülő Luis-alkalmazásba. Ez a megközelítés lehetővé teszi a szülőtartományban, többek között a tulajdonosok és a különböző alárendelt területek önálló alkalmazásokban. 

![Küldési architektúra fogalmi képe](./media/luis-concept-enterprise/dispatch-architecture.png)

A szülőtartomány megjegyezte a LUIS-ben egy `Dispatch` nevű verziót az alkalmazások listájában. 

A csevegési robot megkapja a megnyilatkozás, majd az elküldés a szülő LUIS alkalmazást az előrejelzéshez. A fölérendelt alkalmazás első előre jelzett szándéka határozza meg, hogy melyik LUIS Child-alkalmazást hívják tovább. A csevegési robot részletesebb előrejelzést küld a gyermek alkalmazásnak.

Ismerje meg, hogy a rendszer hogyan hívja meg ezt a hierarchiát a Bot Builder v4 [diszpécser-Application-oktatóanyagból](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>A feladó modellben szándék korlátok
A küldő alkalmazás 500 dispatch forrásokból, egyenértékű 500 szándékot, mint a maximális rendelkezik. 

## <a name="more-information"></a>További információ

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Útmutató a küldő modellhez](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Küldési CLI](https://github.com/Microsoft/botbuilder-tools)
* Minta küldése a robothoz – [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Következő lépések

* Útmutató [a Batch teszteléséhez](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
