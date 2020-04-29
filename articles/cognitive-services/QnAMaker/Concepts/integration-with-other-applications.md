---
title: Integrálás más alkalmazásokkal – QnA Maker
description: A QnA Maker az ügyfélalkalmazások, például a csevegési robotok, valamint más természetes nyelvi feldolgozási szolgáltatások, például a Language Understanding (LUIS) integrálására is használható.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804187"
---
# <a name="design-knowledge-base-for-client-applications"></a>Tudásbázis kialakítása ügyfélalkalmazások számára

A QnA Maker az ügyfélalkalmazások, például a csevegési robotok, valamint más természetes nyelvi feldolgozási szolgáltatások, például a Language Understanding (LUIS) integrálására is használható.

## <a name="integration-with-a-conversational-client"></a>Integráció egy társalgási ügyféllel

A QnA Maker a társalgási ügyfélalkalmazások, például a [Microsoft bot Framework](https://dev.botframework.com/)integrációját. A QnA Maker eljuttatott szöveget nem kell tisztítani vagy átalakítani. QnA Maker természetes nyelveket fogad el, és a legjobb választ adja vissza.

## <a name="create-a-bot-without-writing-any-code"></a>Robot létrehozása kód írása nélkül

Miután közzétette a tudásbázist, hozzon létre egy robotot a **közzétételi** lapról a **robot létrehozása** gomb kiválasztásával. A [robot oktatóanyag](../Quickstarts/create-publish-knowledge-base.md) segítségével megtudhatja, mi történik a gomb kiválasztása után.

## <a name="providing-multi-turn-conversations"></a>Több fordulatos beszélgetések biztosítása

A robot-ügyfél biztosítja a legalkalmasabb választ a Tudásbázisból, és követő utasításokat is biztosít, ha a válasz egy többfordulatos QnA pár része. Megtudhatja, [Hogyan](../how-to/multiturn-conversation.md) vehet fel több beszélgetést és választ a tudásbázisba.

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

QnA Maker a természetes nyelvi feldolgozást használó kérdéseket is felhasználhat, de egy nagyobb rendszer részeként is használható, amely több Tudásbázisból származó kérdésekre ad választ. A QnA Makereket kombinálhatja egy másik kognitív szolgáltatással, Language Understanding (LUIS), hogy természetes nyelvi feldolgozást nyújtson, mielőtt bekerül egy adott tudásbázisba. További információ a [Luis és a QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) együttes használatáról.

## <a name="next-steps"></a>További lépések

A QnA Maker fejlesztési ciklusokkal kapcsolatos [fogalmait](development-lifecycle-knowledge-base.md) megismerheti.