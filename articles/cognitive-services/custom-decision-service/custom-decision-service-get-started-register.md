---
title: Az alkalmazás regisztrálása – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Részletes útmutató az új alkalmazások Azure Custom Decision Service való regisztrálásához.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704549"
---
# <a name="register-your-application"></a>Alkalmazás regisztrálása

Ha Custom Decision Servicet szeretne használni az alkalmazáshoz, regisztrálja azt a portálon. Ez a cikk azt ismerteti, hogyan.

1. Ugrás a Custom Decision Service [elülső oldalára](https://portal.ds.microsoft.com/) . A menüszalagon kattintson a **saját portál**elemre a képen látható módon:

    ![Saját portál](./media/portal.png)

    Ha még nem jelentkezett be, a portál felszólítja, hogy jelentkezzen be a [Microsoft-fiók](https://account.microsoft.com/account). Miután bejelentkezett, a portál megjeleníti a Microsoft-fiók az oldal jobb felső sarkában.

2. Az alkalmazás regisztrálásához kattintson az **új alkalmazás** gombra.

3. A párbeszédpanelen válassza ki az alkalmazáshoz tartozó alkalmazás-azonosítót. Custom Decision Service minden alkalmazáshoz egyedi azonosítót kell megadni. Ha valaki más már elvégezte ezt az azonosítót, a rendszer megkéri, hogy válasszon egy másikat.

4. Adja meg a műveleti set API-t. Ez a beállítás egy RSS-vagy Atom-hírcsatorna, amely az alkalmazás számára elérhető tartalmakat Custom Decision Service. Adja meg a hírcsatorna nevét, és adja meg a kiszolgált URL-címet. Ha később szeretné elvégezni ezt a lépést, kattintson a hírcsatornák gombra, majd kattintson az **új hírcsatorna** gombra. Az RSS-hírcsatornát létrehozó példát később ismertetjük.

5. Az alkalmazás regisztrálásához jelölje be az **egyéni alkalmazás** jelölőnégyzetet a bal alsó sarokban. Adja meg az Azure Storage-fiókhoz tartozó, az alkalmazásadatok naplózására szolgáló [kapcsolatok karakterláncát](../../storage/common/storage-configure-connection-string.md) . További információ a Storage-fiókok létrehozásáról: [Storage-fiók létrehozása, kezelése vagy törlése](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>További lépések

* Első lépések a [weblap](custom-decision-service-get-started-browser.md) vagy [okostelefon alkalmazás](custom-decision-service-get-started-app.md) optimalizálásához.
* Részletes példa az [oktatóanyagra](custom-decision-service-tutorial-news.md) .
* Az [API-referencia](custom-decision-service-api-reference.md) még további információkat tartalmaz a nyújtott funkciókról.
