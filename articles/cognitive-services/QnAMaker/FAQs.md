---
title: Gyakori kérdések – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatás kapcsolatos gyakori kérdések listája
services: cognitive-services
author: tulasim88
manager: cjgronlund
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: be1621bb461d491d4304474034a39a83c5293570
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972640"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Miért van a saját URL-címe / fájl(ok) nem van kibontása kérdés-válasz párt?

Akkor lehet, hogy a QnA Maker nem válaszokat automatikusan kinyerni néhány kérdést és választ (kérdés-válasz) tartalom érvényes GYIK URL-címekről. Ezekben az esetekben egy .txt fájlban illessze be a szövegét, és tekintse meg, ha az eszköz betöltheti azt. Azt is megteheti hogy tudásbázishoz hozzáadhat tartalmat a a Tudásbázis.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Milyen méretű lehet a létrehozott tudásbázis?

A Tudásbázis mérete attól függ, hogy a Termékváltozat az Azure search úgy dönt, hogy a QnA Maker szolgáltatás létrehozásakor. Olvasási [Itt](./Tutorials/choosing-capacity-qnamaker-deployment.md) további részletekért.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Miért nem látok semmit a legördülő jelenik meg, hozzon létre egy új Tudásbázis?

Még nem hozott létre egyetlen QnA Maker szolgáltatást az Azure-ban még. Olvasási [Itt](./How-To/set-up-qnamaker-service-azure.md) hogyan valósítható meg.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Hogyan oszthatom meg a Tudásbázis más?

Megosztás a QnA Maker szolgáltatás szintjén működik, azaz az összes tudásbázisok a szolgáltatások meg lesz osztva. Olvasási [Itt](./How-To/collaborate-knowledge-base.md) hogyan működhet a Tudásbázis.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hogyan válthatok az alapértelmezett üzenetet, ha nem jó talál egyezést?

Az alapértelmezett üzenetet a beállításokat az App service része.
- Nyissa meg a az Azure Portalon, az App service erőforrás

![az App Service qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **beállítások** lehetőség

![qnamaker az App Service-beállítások](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Módosítsa a **DefaultAnswer** beállítás
- Indítsa újra az App service

![qnamaker az App Service-újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Miért érdemes a SharePoint-hivatkozás nem első ki kell olvasni?

Az eszköz elemzi csak nyilvános URL-címeket, és jelenleg nem támogatja a hitelesített adatforrások. Azt is megteheti töltse le a fájlt, és a kérdések és válaszok kinyerése a fájlfeltöltés beállítás használatával.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>A Tudásbázisban végzett frissítések nem fognak megjelenni közzététele. Mi ennek az oka?

Minden szerkesztési művelet az egy táblázat frissítése, tesztelési vagy beállítások, kell menteni kell a közzétételhez. Győződjön meg arról, kattintson a Mentés és a gomb betanításához minden szerkesztési művelet után.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Mikor kell frissíteni a végpont kulcsaim?

A végpont kulcsok frissítenie kell, ha azt gyanítja, hogy azok biztonsága sérült.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A Tudásbázis támogatja a részletes adattartalmat vagy multimédiás nem?

A tudásbázis támogatja a Markdown-formátumot. Azonban az URL-címek automatikus – kibontási korlátozott HTML-Markdown átalakítási képességek. Ha azt szeretné, teljes értékű Markdown használata, a tartalmat közvetlenül a tábla módosítása, vagy töltse fel a Tudásbázis a részletes tartalommal.

Multimédia, például a képek és videók, jelenleg nem támogatott.

## <a name="does-qna-maker-support-non-english-languages"></a>A QnA Maker támogatja a nem angol nyelvű?

További részletekre kíváncsi [támogatott nyelvek](./Overview/languages-supported.md).

Ha több nyelven elérhető tartalmait, mindenképpen hozzon létre egy külön szolgáltatás, az egyes nyelvekhez.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Szükséges a Bot Framework használata a QnA Maker használatához?

Nem, nem kell a Bot Framework használata a QnA Maker. A QnA Maker azonban az Azure Bot Service számos sablon egyik érhető el. Bot Service lehetővé teszi, hogy a gyors intelligens robotfejlesztést keresztül a Microsoft Bot Framework, és fut a kiszolgáló a kevésbé környezetben.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hogyan hozhatnak létre a QnA Maker egy robot?

Kövesse a [ez](./Tutorials/create-qna-bot.md) Robotjait létrehozása az Azure Bot dokumentáció.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hogyan ágyazhatom be a QnA Maker szolgáltatást a honlapomba?

Kövesse az alábbi lépéseket a QnA Maker szolgáltatást webes csevegési vezérlőként beágyazása a webhely:

1. Hozzon létre a gyakori kérdések robot utasításait követve [Itt](./Tutorials/create-qna-bot.md).
2. A webes csevegési engedélyezése a lépéseket követve [Itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)


