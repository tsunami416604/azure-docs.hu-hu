---
title: Gyakori kérdések – Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Gyakori kérdések
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348671"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Miért van a URL-jét (oka) t nem van kibontása kérdés-válasz pár /?

Akkor lehet, hogy kérdések és válaszok készítő nem automatikus-kivonat néhány kérdést és választ (kérdés-válasz) tartalmat érvényes gyakran ismételt kérdések URL-címeket. Ilyen esetben a kérdések és válaszok tartalom illessze be egy .txt fájlt, és tekintse meg, ha az eszköz fogadására képes, akkor. Alternatív megoldásként Ön besorolást hozzáadhat tartalmat a Tudásbázis.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Milyen méretű lehet a létrehozott tudásbázis?

A Tudásbázis méretétől függ a Termékváltozat az Azure search úgy dönt, hogy a kérdések és válaszok készítő szolgáltatás létrehozásakor. Olvasási [Itt](./Tutorials/choosing-capacity-qnamaker-deployment.md) további részleteket.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Miért nem látom semmit a legördülő hozzon létre egy új Tudásbázis közben?

Kérdések és válaszok készítő szolgáltatások még nem hozott létre az Azure-ban még. Olvasási [Itt](./How-To/set-up-qnamaker-service-azure.md) módjáról.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>A Tudásbázis megosztása más

Működik a kérdések és válaszok készítő szolgáltatás szintű megosztás, azaz összes Tudásbázis körrel szolgáltatások lesz megosztva. Olvasási [Itt](./How-To/collaborate-knowledge-base.md) hogyan kell a Tudásbázis másokkal közösen szerkeszthet.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hogyan módosítható az alapértelmezett üzenet, amikor nincs helyes megfelelő?

Az alapértelmezett üzenet a beállítások az App service-ben részét képezi.
- Lépjen a az alkalmazás szolgáltatás-erőforrást az Azure portálon

![qnamaker App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **beállítások** beállítás

![qnamaker App Service-beállítások](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Módosítsa a **DefaultAnswer** beállítás
- Indítsa újra az App service

![qnamaker App Service-újraindítás](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Miért van a SharePoint-hivatkozás nem első kibontott?

Az eszköz csak nyilvános URL-címek elemez, és jelenleg nem támogatja a hitelesített adatforrások. Alternatív megoldásként töltse le a fájlt, és bontsa ki a kérdések és válaszok a fájlfeltöltés kapcsoló használatával.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>A Tudásbázis végzett frissítések nem jelennek meg közzététele. Mi ennek az oka?

Minden alkalmazásszerkesztési művelet a táblázat frissítése a, a teszt vagy a beállítások, meg kell megnyitása előtt tehetők közzé. Győződjön meg arról, kattintson a Mentés és gomb betanítása minden alkalmazásszerkesztési művelet után.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Ha frissítse a végpont kulcsok?

A végpont kulcsok kell frissítse, ha azt gyanítja, hogy azok sérültek.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A Tudásbázis támogatási részletes adatok vagy a multimédia használ?

A tudásbázis támogatja a Markdown-formátumot. Azonban az URL-címek automatikus – kibontási HTML-Markdown átalakítás funkció korlátozott. Ha azt szeretné, teljes körű Markdown-szintaxissal, közvetlenül a táblázat a tartalmának módosítása, vagy töltse fel a Tudásbázis a gazdag tartalommal.

Multimédia, például a lemezképeket és videók, jelenleg nem támogatott.

## <a name="does-qna-maker-support-non-english-languages"></a>Támogatja a kérdések és válaszok készítő nem angol nyelvű?

További részleteket tudhat meg [támogatott nyelv](./Overview/languages-supported.md).

Ha több nyelvet a tartalom, ügyeljen arra, hogy hozzon létre egy külön szolgáltatást, az egyes nyelvekhez.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Szükséges a Bot Framework használata a QnA Maker használatához?

Nem, nem kell a Botot keretrendszer használata a kérdések és válaszok készítő. Azonban kérdések és válaszok készítő érhető el az Azure Botot Service több sablonok egyikét. Botot szolgáltatás lehetővé teszi, hogy a gyors intelligens botot fejlesztési Microsoft Botot keretrendszeren keresztül, és fusson a kiszolgáló kevesebb környezetben.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hogyan hozhat létre a kérdések és válaszok készítő bot?

Kövesse az utasításokat a [ez](./Tutorials/create-qna-bot.md) a Botot létrehozása a Azure Botot dokumentációját.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hogyan ágyazhatom be a QnA Maker szolgáltatást a honlapomba?

Kövesse az alábbi lépéseket a kérdések és válaszok készítő szolgáltatás a webalkalmazás-Csevegés vezérlő beágyazása a webhely:

1. Hozzon létre a gyakran ismételt kérdések botot cikk utasításait követve [Itt](./Tutorials/create-qna-bot.md).
2. A webes Csevegés engedélyezése a következő lépések [Itt](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)


