---
title: A QnA Maker bemutatása – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: A QnA Maker lehetővé teszi egy olyan kérdés-válasz szolgáltatás működtetését félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "36295145"
---
# <a name="what-is-qna-maker"></a>Mi a QnA Maker?

A [QnA Maker](https://qnamaker.ai) lehetővé teszi egy olyan kérdés-válasz szolgáltatás működtetését félig strukturált tartalomból, mint a GYIK- (Gyakori kérdések) dokumentumok, URL-címek és termékútmutatók. Létrehozhat egy kérdés-válasz modellt, amely rugalmas a felhasználói lekérdezések szempontjából, és olyan válaszokat adhat meg, amelyek természetes, párbeszédszerű használatára betanítható egy robot.

A könnyen kezelhető grafikus felhasználói felületen létrehozhat, felügyelhet, betaníthat és üzembe helyezhet egy szolgáltatást fejlesztői tapasztalat nélkül is.

![Áttekintés](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Legfontosabb tudnivalók

- **Kódolás nélküli** környezet [GYIK-robot](https://aka.ms/qnamaker-docs-create-faqbot) létrehozásához.
- **Nincs több hálózati sávszélesség-szabályozás**. A tranzakciók száma helyett a szolgáltatás üzemeltetéséért fizethet. További részleteket a [díjszabási oldalon](https://aka.ms/qnamaker-docs-pricing) talál.
- **Igény szerint végezhet skálázást**. Válassza ki a forgatókönyvhöz illő egyéni összetevők megfelelő SKU-it. Lásd QnA Maker szolgáltatáshoz tartozó [kapacitás kiválasztásának módját](https://aka.ms/qnamaker-docs-capacity) ismertető részt.
- **Teljes adatkezelési megfelelőség**. Az adatokat és a futtatókörnyezeti összetevőket a rendszer a felhasználó Azure-előfizetésében helyezi üzembe, ügyelve a megfelelőségi korlátokra. További részleteket lent talál.

## <a name="key-qna-maker-processes"></a>Fő QnA Maker-folyamatok

A QnA Maker két fő szolgáltatást biztosít az adatokhoz:

* **Kinyerés**: strukturált kérdés-válasz adatokat nyerhet ki olyan félig strukturált adatforrásokból, mint egy GYIK-dokumentum vagy egy termékútmutató. Ez a kinyerés a tudásbázis létrehozásakor végezhető el. Tudásbázist [itt](https://aka.ms/qnamaker-docs-createkb) hozhat létre.

* **Megfeleltetés**: Ha kész a tudásbázisa, és [elvégezte a betanítást és a tesztelést is](https://aka.ms/qnamaker-docs-trainkb), [közzéteheti](https://aka.ms/qnamaker-docs-publishkb) a tudásbázist. Ez engedélyez egy végpontot a QnA Maker-tudásbázishoz, amely azután használható lesz a robotban vagy az alkalmazásban. Ez a végpont fogadja a felhasználói kérdéseket, és válaszol a tudásbázisban található legjobb kérdéssel/válasszal, mellékelve a találat megbízhatósági pontszámát is.

## <a name="qna-maker-architecture"></a>QnA Maker-architektúra

A QnA Maker-verem a következő részekből áll:

1. **QnA Maker felügyeleti szolgáltatások (vezérlősík)**: A QnA Maker-tudásbázis kezelhetősége, amely tartalmazza a kezdeti létrehozást, a frissítést, a betanítást és a közzétételt. Ezek a tevékenységek a [portálon](https://qnamaker.ai) vagy a [kezelési API-kon](https://aka.ms/qnamaker-v4-apis) keresztül végezhetők el. A felügyeleti szolgáltatások kommunikálnak az alábbi futtatókörnyezeti összetevővel.

2. **QnA Maker-futtatókörnyezet (adatsík)**: Az adatok és a futtatókörnyezet a felhasználó Azure-előfizetésében, a felhasználó által kiválasztott régióban lesz üzembe helyezve. Az ügyfélkérdés/-válasz tartalmat a rendszer az [Azure Search](https://azure.microsoft.com/services/search/) szolgáltatásban tárolja, és a futtatókörnyezetet [App Service](https://azure.microsoft.com/services/app-service/)-ként helyezi üzembe. Választhatja azt a lehetőséget is, hogy üzembe helyez egy [Application Insights](https://azure.microsoft.com/services/application-insights/)-erőforrást elemzési célból.

![Architektúra](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker-szolgáltatás létrehozása](../how-to/set-up-qnamaker-service-azure.md)
