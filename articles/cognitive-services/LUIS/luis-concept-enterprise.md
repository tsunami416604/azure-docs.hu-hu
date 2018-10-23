---
title: A LUIS-alkalmazás – beszédfelismerés vállalati fogalmai
titleSuffix: Azure Cognitive Services
description: Nagy LUIS-alkalmazások tervezési fogalmak ismertetése.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: bb4078c9131488e5077795df792de259d7c789dc
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638273"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Vállalati stratégiák a LUIS-alkalmazások
Tekintse át ezeket a vállalati alkalmazások tervezési stratégiák.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Ha a kvóta túl LUIS-kérelmek várt
Ha a LUIS alkalmazás kérések sebessége meghaladja a megengedett [kvóta arány](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), a terhelés több LUIS-alkalmazásokba az a [ugyanazon definíció](#use-multiple-apps-with-same-app-definition) , vagy hozzon létre és [hozzárendelése több kulcs](#assign-multiple-luis-keys-to-same-app) , a alkalmazás. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Több alkalmazás használata azonos app-definíció
Az eredeti LUIS alkalmazás exportálása, majd importálja a az alkalmazás különböző alkalmazás be újra. Minden alkalmazás rendelkezik a saját alkalmazás azonosítóját. Ha közzéteszi, alkalmazásnévnek, ugyanazzal a kulccsal használata helyett hozzon létre minden alkalmazáshoz külön kulcsnak. A terhelés kiegyenlítése alkalmazásnévnek úgy, hogy nincs egyetlen alkalmazás túlterhelte van. Adjon hozzá [Application Insights](luis-tutorial-bot-csharp-appinsights.md) felhasználásának figyelésére. 

Annak érdekében, hogy az első közötti összes alkalmazás ugyanazon felső célja, ellenőrizze, hogy az első és második célja között szándék előrejelzési van elég széles ahhoz, hogy a LUIS ne legyen összetéveszteni őket, között kisebb eltérések utterances alkalmazások különböző eredményt. 

Egyetlen alkalmazás jelölhetnek ki a főkiszolgáló. Amely a felülvizsgálatra javasolt megszólalásokat kell adva a fő alkalmazáshoz, majd visszahelyezi a többi alkalmazást is. Ez az vagy egy teljes exportálás, az alkalmazás vagy a betöltés címkézett megcímkézzen a főágból a gyermekek. Betöltése teheti meg, vagy a [LUIS](luis-reference-regions.md) webhelyére vagy a szerzői műveletekhez részben API egy [utterance (kifejezés) egyetlen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) , vagy egy [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Egy rendszeres ütemezés [végpont kimondott szöveg felülvizsgálata](luis-how-to-review-endoint-utt.md) aktív tanulás, például kéthetente, majd újbóli betanítás után tegye közzé újra. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Ugyanazt az alkalmazást a kulcsok több LUIS hozzárendelése
Ha a LUIS-alkalmazás több végpontot, a találatok, mint az egyetlen kulcs kvótája engedélyez, és társítsa további kulcsok a LUIS alkalmazás kap. Hozzon létre egy traffic manager vagy a terheléselosztó a végpont lekérdezések kezelése a végpont kulcsok között. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Ha a monolitikus alkalmazások adja vissza nem megfelelő leképezés
Ha az alkalmazás számos különböző felhasználói utterances előrejelzése hivatott, vegye fontolóra a [dispatch modell](#dispatch-tool-and-model). LUIS fókusz észlelési sikeresen helyett első nem biztos a szülő alkalmazás közötti leképezések és az alárendelt alkalmazások közötti leképezések között egy monolitikus alkalmazások használhatatlanná tévő lehetővé teszi. 

Egy rendszeres ütemezés [végpont kimondott szöveg felülvizsgálata](luis-how-to-review-endoint-utt.md) aktív tanulás, például kéthetente, majd újbóli betanítás után tegye közzé újra. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Ha szüksége van a több mint 500 szándékot
Például tegyük fel, amely több mint 500 szándékot rendelkezik office asszisztens fejleszt. 200 leképezések értekezleteit vonatkoznak, ha emlékeztetők készül a 200-as, 200-as készül a munkatársai, kapcsolatos információk lekérése és e-mailt küld a rendszer 200-as, csoport szándékot, hogy minden csoport egyetlen alkalmazást, majd hozzon létre egy legfelső szintű alkalmazást, amely tartalmazza minden egyes szándékot. Használja a [csatolva az eszköz és architektúra](#dispatch-tool-and-model) hozhat létre a legfelső szintű alkalmazást. Módosítsa a robot a lépcsőzetes hívást használja, mint a show a [dispatch oktatóanyag][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Mikor kell egyesíteni több LUIS és a QnA maker alkalmazások
Ha több LUIS és a QnA maker alkalmazásokkal rendelkezik, kell válaszolnia-robot esetén használja a [küldő eszköz](#dispatch-tool-and-model) hozhat létre a legfelső szintű alkalmazást. Módosítsa a robot a lépcsőzetes hívást használja, mint a show a [dispatch oktatóanyag][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Küldő eszköz és a modell
Használja a [Dispatch] [ dispatch-tool] parancssori eszköz, található [BotBuilder-eszközök](https://github.com/Microsoft/botbuilder-tools) úgy, hogy több LUIS és/vagy a QnA Maker alkalmazásokat a LUIS alkalmazás szülő összevonásával. Ez a megközelítés lehetővé teszi a szülőtartományban, többek között a tulajdonosok és a különböző alárendelt területek önálló alkalmazásokban. 

![Küldési architektúra fogalmi képe](./media/luis-concept-enterprise/dispatch-architecture.png)

A szülőtartomány LUIS nevű verzióját megadó `Dispatch` az alkalmazáslistában. 

A csevegőrobot kap az utterance (kifejezés), majd elküldi a szülő előrejelzéshez LUIS-alkalmazás. A szülő alkalmazásból felső előre jelzett célja határozza meg, melyik gyermek LUIS-alkalmazás neve mellett. A csevegőrobot küld az alárendelt alkalmazás pontosabb előrejelzéshez az utterance (kifejezés).

Megismerheti, hogyan szeretné tenni ezt a hierarchiát a hívások, a Bot Builder v4 [dispatcher-alkalmazás-tutorial][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>A feladó modellben szándék korlátok
A küldő alkalmazás 500 dispatch forrásokból, egyenértékű 500 szándékot, mint a maximális rendelkezik. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [kötegelt tesztelése](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool