---
title: LUIS alkalmazás - Azure vállalati fogalmak |} Microsoft Docs
description: Tervezési alapelvek nagy LUIS alkalmazások megismerése.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: dde7012dee0eb5ea3ac2e1257cb8d2fca5843d4b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350111"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Vállalati stratégiák LUIS alkalmazások
Tekintse át a Tervező stratégiák a vállalati alkalmazás.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Ha várhatóan LUIS kérelmek száma meghaladja a kvótát
Ha a LUIS app kérelmek aránya meghaladja a megengedett [kvóta arány](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), további LUIS alkalmazásaira terheléselosztás végett a [azonos app-definíciót](#use-multiple-apps-with-same-app-definition) , vagy hozzon létre és [több kulcs hozzárendelése](#assign-multiple-luis-keys-to-same-app) való a alkalmazás. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Ugyanazon alkalmazás definícióját több alkalmazások használata
Az eredeti LUIS alkalmazás exportálása, majd importálja vissza az alkalmazás különálló alkalmazás. Minden alkalmazás rendelkezik saját alkalmazás azonosítója. Történő közzétételekor minden alkalmazások között, ugyanazzal a kulccsal használata helyett hozzon létre egy külön kulcsot minden alkalmazáshoz. A terhelés kiegyenlítése összes alkalmazások között, így nem egyetlen meghatározott alkalmazás túlterhelik. Adja hozzá [Application Insights](luis-tutorial-bot-csharp-appinsights.md) felhasználásának figyelésére. 

Ahhoz, hogy az alkalmazások közötti azonos felső leképezés szüksége van, ellenőrizze, hogy az első és második szándékot közötti leképezési előrejelzését van elég nagy, hogy LUIS nincs konfigurálja, különböző eredményt utterances kisebb változások alkalmazások között. 

Jelölje ki a fő egyetlen alkalmazást. Bármely utterances, amelyek a felülvizsgálati javaslat kell hozzáadni a fő alkalmazást, majd visszahelyezi a más alkalmazásokba. Ez az egy teljes exportálása, az alkalmazás vagy a címkézett utterances tölt be a fő a gyermekek számára. Betöltés teheti vagy a [LUIS] [ LUIS] webhelyére vagy a szerzői műveletekhez API-t a egy [utterance egyszeri](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) vagy egy [kötegelt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Ütemezhet egy rendszeres [át kell tekinteni végpont utterances](label-suggested-utterances.md) aktív biztonságával, például a kéthetente, majd újratanítása és közzé. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Hozzárendelése több LUIS kulcsok ugyanahhoz az alkalmazáshoz
Ha a LUIS app kap további végpont találatok, mint a egyetlen kulcs kvótája engedélyez, hozzon létre, és további kulcsok rendelje a LUIS alkalmazást. Hozzon létre egy traffic manager vagy a terheléselosztó a végpont lekérdezések kezelése az előfizetéshez kulcsok között. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Ha az egységes alkalmazás adja vissza nem megfelelő leképezés
Ha a célja, hogy az alkalmazás felhasználói utterances számos előre jelezni, vegye fontolóra a [feladó modell](#dispatch-tool-and-model). Egy egységes alkalmazás összeállításának lehetővé teszi a LUIS fókusz észlelési közötti leképezések sikeresen helyett első nem biztos a szülő alkalmazás közötti leképezések és alárendelt alkalmazások között. 

Ütemezhet egy rendszeres [át kell tekinteni végpont utterances](label-suggested-utterances.md) aktív biztonságával, például a kéthetente, majd újratanítása és közzé. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Ha szüksége van a több mint 500 leképezések
Például tegyük fel, amely rendelkezik 500 leképezések office asszisztens kidolgozása. 200 leképezések értekezletek ütemezés vonatkoznak, ha emlékeztetők készül 200-as, 200 készül, munkatársakat kapcsolatos információk beolvasásakor és 200 az e-mailek küldéséhez, csoport leképezések, hogy minden egyes csoport egyetlen alkalmazást, majd hozzon létre egy legfelső szintű alkalmazást tartalmazó minden leképezés. Használja a [csatolva az eszköz és az architektúra](#dispatch-tool-and-model) a legfelső szintű alkalmazás elkészítésére. Módosítsa a Megjelenítés a a kaszkádolt hívásához használandó botot a [feladó oktatóanyag][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Ha több LUIS és kérdések és válaszok készítő alkalmazások egyesítenie kell
Ha több LUIS és kérdések és válaszok készítő alkalmazások, amelyeknek válaszolni bot, használja a [küldő eszköz](#dispatch-tool-and-model) a legfelső szintű alkalmazás elkészítésére. Módosítsa a Megjelenítés a a kaszkádolt hívásához használandó botot a [feladó oktatóanyag][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Küldő eszköz és a modell
Használja a [feladó] [ dispatch-tool] található a parancssori eszköz [BotBuilder-eszközök](https://github.com/Microsoft/botbuilder-tools) több LUIS és/vagy a kérdések és válaszok készítő alkalmazások egyesítése egy szülő LUIS alkalmazás számára. Ez a megközelítés lehetővé teszi, hogy a tartomány a különálló alkalmazás tulajdonosok és a különböző alárendelt területeket is beleértve. 

![Küldési architektúra fogalmi képe](./media/luis-concept-enterprise/dispatch-architecture.png)

A szülőtartomány LUIS szerint jelenik meg egy **V feladó** alkalmazást. 

![Képernyőfelvétel a LUIS alkalmazások listája a küldő eszköz által létrehozott LUIS alkalmazással](./media/luis-concept-enterprise/dispatch.png)

A chatbot megkapja a utterance, majd elküldi a szülő LUIS app előrejelzés. A felső előre jelzett célt a szülő alkalmazás mely LUIS alkalmazás mellett elnevezése gyermek határozza meg. A chatbot a utterance küld az alárendelt alkalmazás pontosabb előrejelzés.

Megérteni, hogyan ezt a hierarchiát a hívások a Botot jelentéskészítő v4 érkezett [kézbesítő-alkalmazás-oktatóanyag][dispatcher-application-tutorial].  

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [egy kötegelt tesztelése](luis-how-to-batch-test.md)

[LUIS]:luis-reference-regions.md
[dispatcher-application-tutorial]:https://aka.ms/bot-dispatch
[dispatch-tool]:https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch