---
title: Tudásbázis – QnA Maker élettartama
titleSuffix: Azure Cognitive Services
description: A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 68fcc8b324bebfc7ca4fa94b2633b475137ea580
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075593"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>A QnA Maker Tudásbázis életciklusa
A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis létrehozása
A QnA Maker Tudásbázis (KB) végpontja biztosítja a legjobb-match válasz tartalma a KB-os felhasználói lekérdezés. Tudásbázis létrehozása egy egyszeri művelet egy content tárház kérdések, válaszok és kapcsolódó metaadatok beállítására. Tudásbázis például gyakori kérdéseket tartalmazó oldalak, kézikönyvek vagy strukturált Q-A-párokat már meglévő tartalmak bejárásához hozható létre. Ismerje meg, hogyan [Tudásbázis létrehozása](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Tesztelése és frissítése a Tudásbázis
A Tudásbázis készen áll a tesztelésre, a rendszer kitölti tartalmat, besorolást vagy automatikus kivonása után. Tesztelés végezhető el a **teszt** panel gyakori felhasználói lekérdezések beírásával, és annak ellenőrzése, hogy a visszaadott válaszokat várt módon, és a rendszer megfelelő magabiztossági pontszámot. Alacsony megbízhatósági pontszámok újraindított alternatív kérdéseket adhat hozzá. Hozzáadhat új válaszok is, ha a lekérdezés visszaadja a "nincs egyezés a KB-ban" alapértelmezett válasz. Ez a teszt-frissítés szoros ciklus továbbra is fennáll, addig, amíg az eredmények elégedett. Ismerje meg, hogyan [a Tudásbázis tesztelése](../How-To/test-knowledge-base.md).

A nagy Tudásbázis a tesztelés automatizálható generateAnswer API-k használatával. 

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele
Ha elkészült a Tudásbázis tesztelése, közzéteheti azt. A legújabb verzióra a tesztelt Tudásbázis egy dedikált Azure Search-index jelölő leküldések közzététele a **közzétett** Tudásbázis. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

Ezzel a módszerrel végzett módosítások folyamatban van a Tudásbázis tesztelése verziója nincsenek hatással a közzétett verzió, amely egy éles alkalmazásban élő lehet.

Ezek tudásbázisok mindegyike külön tesztelési megcélozhatóvá válnak. A teszt verzióját a Tudásbázis-célként az API-k használatával, `isTest=true` generateAnswer hívásában jelző.

Ismerje meg, hogyan [közzéteheti a tudásbázist](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Használat monitorozása
Kell tudniuk jelentkezni a csevegési naplók, a szolgáltatás, akkor engedélyeznie kell Application Insights amikor Ön [a QnA Maker szolgáltatás létrehozása](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző analytics kérheti le. További információ az application insights használatával első [a QnA Maker szolgáltatás analytics](../How-To/get-analytics-knowledge-base.md).

Analytics-témák alapján, győződjön meg arról, megfelelő [frissítéseit a Tudásbázis](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)

## <a name="see-also"></a>Lásd még 

[Tudásbázis](./knowledge-base.md)
[QnA Maker – áttekintés](../Overview/overview.md)
