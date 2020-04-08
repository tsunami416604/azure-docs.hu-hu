---
title: Integrálható más alkalmazásokkal - QnA Maker
description: A QnA Maker integrálódik az ügyfélalkalmazásokba, például a csevegőrobotokba, valamint más természetes nyelvi feldolgozási szolgáltatásokba, például a nyelvi megértésbe (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804187"
---
# <a name="design-knowledge-base-for-client-applications"></a>Ügyfélalkalmazások tudásbázisának tervezése

A QnA Maker integrálódik az ügyfélalkalmazásokba, például a csevegőrobotokba, valamint más természetes nyelvi feldolgozási szolgáltatásokba, például a nyelvi megértésbe (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integráció társalgási ügyféllel

QnA Maker integrálja a társalgási kliens alkalmazások, mint például a [Microsoft Bot Framework](https://dev.botframework.com/). A QnA Makernek küldött szöveget nem kell tisztítani vagy átalakítani. A QnA Maker elfogadja a természetes nyelveket, és a legjobb választ adja vissza.

## <a name="create-a-bot-without-writing-any-code"></a>Hozzon létre egy bot írása nélkül kódot

Miután közzétette a tudásbázist, hozzon létre egy robotot a **Közzététel** lapon a **Robot létrehozása** gombra kattintva. A [bot oktatóanyag](../Quickstarts/create-publish-knowledge-base.md) segítségével megtudhatja, mi történik a gomb kiválasztása után.

## <a name="providing-multi-turn-conversations"></a>Többfordulatos beszélgetések biztosítása

A bot kliens biztosítja a legjobb kiválasztott választ a tudásbázisból, és nyomon követési utasításokat adhat, ha a válasz egy többfordulatos QnA-pár része. Ismerje [meg, hogyan](../how-to/multiturn-conversation.md) adhat hozzá többfordulatos beszélgetési kérdéseket és válaszkészleteket a tudásbázisához.

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

Bár a QnA Maker olyan kérdéseket dolgoz fel, amelyek természetes nyelvi feldolgozást használnak, egy nagyobb rendszer egy olyan része is használható, amely több tudásbázisból válaszol a kérdésekre. A QnA Maker kombinálásával egy másik cognitive Service, Language Understanding (LUIS), természetes nyelvi feldolgozás, mielőtt egy adott tudásbázis. További információ aRRÓL, hogy mikor és hogyan használhatja együtt a [LUIS-t és a QnA Makert.](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json)

## <a name="next-steps"></a>További lépések

Ismerje meg a QnA Maker fejlesztési [ciklusának fogalmait.](development-lifecycle-knowledge-base.md)