---
title: Az alkalmazás - Azure kognitív szolgáltatások regisztrálása |} Microsoft Docs
description: Egy részletes útmutató hogyan kell regisztrálni egy új alkalmazást az Azure egyéni döntési szolgáltatással
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348962"
---
# <a name="register-your-application"></a>Alkalmazás regisztrálása

Egyéni döntési szolgáltatás az alkalmazás használatához regisztrálni a portálon. Ez a cikk azt ismerteti, hogyan.

1. Lépjen a [első oldal](https://ds.microsoft.com/) egyéni döntési szolgáltatás. Kattintson a menüszalagon **saját Portal**, az ábrán a kijelölt:

    ![A portál](./media/portal.png)

    Ha már nincs bejelentkezve, a portál felszólítja, hogy jelentkezzen be az a [Microsoft-fiók](https://account.microsoft.com/account). Miután bejelentkezett, a portál megjeleníti a Microsoft-fiókjával a lap jobb felső sarkában.

2. Az alkalmazás regisztrálásához kattintson a **új alkalmazás** gombra.

3. A párbeszédpanelen válassza ki az alkalmazás alkalmazás azonosítója. Egyéni döntési szolgáltatás minden alkalmazáshoz egyedi azonosító szükséges. Ha valaki más már hajtott végre ezt az Azonosítót, a rendszer megkérdezi, hogy válasszon egy másikat.

4. Adjon meg egy művelet API. Ez a beállítás egy RSS vagy Atom hírcsatornát, melyekkel a rendelkezésre álló tartalom egyéni döntési szolgáltatáshoz az alkalmazásra vonatkozóan. Adjon meg egy nevet a hírcsatorna, és az URL-címét, amelyről kiszolgált. Ehhez később ezt a lépést, kattintson a **hírcsatornák** gombra, majd a **új adatcsatorna** gombra. Egy példa, amely létrehoz egy RSS-hírcsatorna a későbbiekben olvashat.

5. Az alkalmazás regisztrálásához jelölje ki a **egyéni alkalmazás** a bal alsó sarkában található jelölőnégyzetek bejelölésével. Adjon meg egy [kapcsolati karakterlánc](../../storage/common/storage-configure-connection-string.md) az az Azure storage-fiók, ahol az alkalmazás adatokat a rendszer naplózza. A storage-fiók létrehozásával kapcsolatos további információkért lásd: [létrehozása, kezelése és a tárfiók törlése](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>További lépések

* Első lépések optimalizálása [egy weblap](custom-decision-service-get-started-browser.md) vagy [alkalmazással](custom-decision-service-get-started-app.md).
* Munka – egy [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb például.
* Tekintse át a [API-referencia](custom-decision-service-api-reference.md) további információt a megadott funkciót.