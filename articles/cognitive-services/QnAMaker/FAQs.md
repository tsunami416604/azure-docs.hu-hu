---
title: Gyakori kérdések – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker szolgáltatás kapcsolatos gyakori kérdések listája
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: 2e4a5d9b7ee2a1a88bcfe819be6540385458108f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622363"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="manage-the-knowledge-base"></a>A Tudásbázis kezelése

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>A QnA Maker egy részét véletlenül törölt, Mit tegyek? 

Összes törlése végleges, beleértve a kérdés és válasz párt, fájlok, URL-címek, egyéni kérdéseket és válaszokat, tudásbázisok vagy Azure-erőforrások. Ellenőrizze, hogy a Tudásbázis a exportálja a **beállítások** oldalhoz, mielőtt bármelyik részét a Tudásbázis törlése. 

### <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Miért van a saját URL-címe / fájl(ok) nem van kibontása kérdés-válasz párt?

Akkor lehet, hogy a QnA Maker nem válaszokat automatikusan kinyerni néhány kérdést és választ (kérdés-válasz) tartalom érvényes GYIK URL-címekről. Ezekben az esetekben egy .txt fájlban illessze be a szövegét, és tekintse meg, ha az eszköz betöltheti azt. Azt is megteheti, besorolást hozzáadhat tartalmat a Tudásbázis keretében a [QnA Maker portal](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Milyen méretű lehet a létrehozott tudásbázis?

A Tudásbázis mérete attól függ, hogy a Termékváltozat az Azure search úgy dönt, hogy a QnA Maker szolgáltatás létrehozásakor. Olvasási [Itt](./Tutorials/choosing-capacity-qnamaker-deployment.md) további részletekért.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Miért nem látom semmit a legördülő menü jelenik meg, hozzon létre egy új Tudásbázis?

Még nem hozott létre egyetlen QnA Maker szolgáltatást az Azure-ban még. Olvasási [Itt](./How-To/set-up-qnamaker-service-azure.md) megtudhatja, hogyan valósítható meg.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hogyan oszthatom meg a Tudásbázis másokkal?

Megosztása a QnA Maker szolgáltatás szintjén működik, vagyis a szolgáltatásban az összes tudásbázisok meg lesz osztva. Olvasási [Itt](./How-To/collaborate-knowledge-base.md) hogyan működhet a Tudásbázis.

### <a name="can-you-share-a-kb-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-kb"></a>Megoszthat-e, amely nem szerepel az azonos AAD-bérlő módosítása egy KB-os közreműködő egy KB-os? 

Megosztás Azure szerepköralapú hozzáférés-vezérlés (RBAC) alapul. Ha megoszthatja _bármely_ erőforrás egy másik felhasználó az Azure-ban is megoszthatja a QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-kbs-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-kb"></a>Ha egy App Service-csomag az 5 QnAMaker Tudásbázis. Is jogosultságokat rendelhet hozzá olvasási/írási 5 különböző felhasználók így azok férhetnek csak 1 QnAMaker KB-os?

Egy teljes QnAMaker szolgáltatással, nem az egyéni Tudásbázis megoszthatja.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hogyan válthatok az alapértelmezett üzenetet, ha nem jó talál egyezést?

Az alapértelmezett üzenetet a beállításokat az App service része.
- Nyissa meg az Azure Portalon, az App service erőforrás

![az App Service qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **beállítások** lehetőség

![qnamaker az App Service-beállítások](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Módosítsa a **DefaultAnswer** beállítás
- Indítsa újra az App service

![qnamaker az App Service-újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Miért érdemes a SharePoint-hivatkozás nem első ki kell olvasni?

Az eszköz elemzi csak nyilvános URL-címeket, és jelenleg nem támogatja a hitelesített adatforrások. Azt is megteheti töltse le a fájlt, és a kérdések és válaszok kinyerése a fájlfeltöltés beállítás használatával.


### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>A Tudásbázisban végzett frissítések nem fognak megjelenni közzététele. Mi ennek az oka?

Minden szerkesztési művelet az egy tábla frissítése, tesztelési vagy beállítás kell megnyitása előtt közzé lehet tenni. Kattintson a **mentéséhez és a vonat** gomb minden szerkesztési művelet után.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A Tudásbázis támogatja a részletes adattartalmat vagy multimédiás nem?

A tudásbázis támogatja a Markdown-formátumot. Azonban az URL-címek automatikus – kibontási korlátozott HTML-Markdown átalakítási képességek. Ha azt szeretné, teljes értékű Markdown használata, a tartalmat közvetlenül a tábla módosítása, vagy töltse fel a Tudásbázis a részletes tartalommal.

Multimédia, például a képek és videók, jelenleg nem támogatott.

### <a name="does-qna-maker-support-non-english-languages"></a>A QnA Maker támogatja a nem angol nyelvű?

További részletekre kíváncsi [támogatott nyelvek](./Overview/languages-supported.md).

Ha több nyelven elérhető tartalmait, mindenképpen hozzon létre egy külön szolgáltatás, az egyes nyelvekhez.

## <a name="manage-service"></a>Szolgáltatás kezelése

### <a name="when-should-i-restart-my-app-service"></a>Mikor kell újraindítani az app Service-ben? 

Az app service frissítése, ha a figyelmeztetés ikon mellett a Tudásbázis következő, a verzió értékét a **végpont kulcsok** tábla a **felhasználói beállítások** [oldal](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Mikor kell frissíteni a végpont kulcsaim?

Frissítse a végpont kulcsokat, ha azt gyanítja, hogy azok biztonsága sérült.

### <a name="can-i-use-the-same-azure-search-resource-for-kbs-using-multiple-languages"></a>Használható Tudásbázis több nyelvet használják az Azure Search ugyanazt az erőforrást?

Több nyelv és a több Tudásbázis segítségével a felhasználók számára, az egyes nyelvekhez tartozó QnA Maker erőforrás létrehozásához. Ezzel létrehoz egy külön az Azure search szolgáltatás minden nyelvhez. Egy egyetlen az Azure search szolgáltatásban a különböző nyelvi Tudásbázis keverése csökkentett teljesítményű, az eredmények relevancia eredményez.

## <a name="integrate-with-other-services-including-bots"></a>Integráció más szolgáltatásokkal, beleértve a robotok

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Szükséges a Bot Framework használata a QnA Maker használatához?

Nem, nem kell a Bot Framework használata a QnA Maker. A QnA Maker azonban az Azure Bot Service számos sablon egyik érhető el. Bot Service lehetővé teszi, hogy a gyors intelligens robotfejlesztést keresztül a Microsoft Bot Framework, és hogy fut egy kiszolgáló nélküli környezetben.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hogyan hozhatnak létre a QnA Maker egy robot?

Kövesse a [ez](./Tutorials/create-qna-bot.md) Robotjait létrehozása az Azure Bot Service dokumentációjában.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hogyan ágyazhatom be a QnA Maker szolgáltatást a honlapomba?

Kövesse az alábbi lépéseket a QnA Maker szolgáltatást webes csevegési vezérlőként beágyazása a webhely:

1. Hozzon létre a gyakori kérdések robot utasításait követve [Itt](./Tutorials/create-qna-bot.md).
2. A webes csevegési engedélyezése a lépéseket követve [Itt](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Adattárolás

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Milyen adatokat tárolja, és azt tárolására? 

A QnA Maker szolgáltatás létrehozásakor kiválasztott egy Azure-régióban. A tudásbázisok és a naplófájlok ebben a régióban vannak tárolva. 
