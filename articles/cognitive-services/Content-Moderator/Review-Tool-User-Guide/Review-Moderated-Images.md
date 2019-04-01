---
title: Használja a vizsgálóeszközt - Content Moderator keresztül tartalom értékelések
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan a felülvizsgálati eszköz lehetővé teszi, hogy az emberi moderátorok egy web portal-rendszerképeket áttekintéséhez.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758486"
---
# <a name="create-human-reviews"></a>Emberi ellenőrző létrehozása

Ebben az útmutatóban megtudhatja, hogyan állíthat be [felülvizsgálatok](../review-api.md#reviews) a felülvizsgálati eszköz webhelyen. Értékelések tárolja, és az emberi moderátorok mérje fel a tartalom megjelenítése. A moderátorok is az alkalmazott címkék alter és szükség szerint a saját egyéni címkék alkalmazása. Amikor egy felhasználó a felülvizsgálat befejeződött, az eredményeket egy megadott visszahívási végpont érkeznek, és a tartalmat a webhely törlődik.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.

## <a name="image-reviews"></a>Képekre vonatkozó vélemények

1. Nyissa meg a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/), jelölje be a **próbálja** lapra, és tekintse át az egyes rendszerképek feltöltése.
1. Miután a feltöltött képek végzett feldolgozás, nyissa meg a **tekintse át** lapra, és válassza **lemezkép**.

    ![Chrome böngészőben a vizsgálóeszközt látható az a képen tekintse át opció kiemelésével](images/review-images-1.png)

    A képek megjelenítése minden olyan címkét, az automatikus moderálási folyamat hozzá van rendelve. A lemezképek, a felülvizsgálati eszköz keresztül beküldött más részt vevő felülvizsgálóknak nem láthatók el.

1. Szükség esetén, helyezze át a **megjeleníthető értékelések** a lemezképek, a képernyőn megjelenő számát csúszka (1). Kattintson a **címkézett** vagy **címkézetlen** gombok (2) a képek rendezése ennek megfelelően. Kattintson a Váltás a be- vagy kikapcsolása címke panel (3).

    ![Chrome böngészőben a vizsgálóeszközt felülvizsgálatra megcímkézett képek megjelenítése](images/review-images-2.png)

1. További részletek egy rendszerkép, kattintson a miniatűr, és válassza a három pontra a **részleteinek megtekintéséhez**. Kép egy a csapaton rendelhet a **áthelyezése** beállítás (lásd: a [csapatok](./configure.md#manage-team-and-subteams) szakaszban tudhat meg többet az alcsoportok).

    ![Egy olyan rendszerképre a nézet részletei lehetőség kiemelésével](images/review-images-3.png)

1. Tallózással keresse meg a kép moderálás adatait részleteit megjelenítő oldalon.

    ![A jóváhagyás részleteit egy külön panelen felsorolt kép](images/review-images-4.png)

1. Miután ellenőrizte és a címke hozzárendeléseinek frissítése, igény szerint, kattintson a **tovább** az értékelések elküldéséhez. Miután elküldött, öt másodpercen belül kell kattintania a **előző** gombra kattintva térjen vissza az előző képernyőre, és tekintse át a lemezképeket újra. Ezt követően a képek nem lesznek a küldési várólista és a **előző** gombot már nem érhető el.

## <a name="text-reviews"></a>Szövegekre vonatkozó vélemények

Szöveg áttekinti a lemezkép felülvizsgálatok hasonlóan működnek. Helyett tartalmat tölt fel, egyszerűen írja vagy illessze be a szöveget (legfeljebb 1024 karakter). Ezután a Content Moderator elemzi a szöveget, és alkalmazza a címkéket (mellett egyéb moderálás információkat, például TRÁGÁR és a személyes adatok). Szövegekre vonatkozó vélemények, az alkalmazott címkék bekapcsolására, illetve egyéni címkék alkalmazásához a felülvizsgálat elküldése előtt.

![A felülvizsgálati eszköz ábrázoló képernyőkép megjelölt szöveget a Chrome böngésző ablakában](../images/reviewresults_text.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban útmutatóból megtudhatta, hogyan állíthatja be, és használja a Content Moderator felülvizsgálatok [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com). Ezután tekintse meg a [REST API-útmutató](../try-review-api-review.md) vagy a [.NET SDK útmutatóját](../moderation-reviews-quickstart-dotnet.md) megtudhatja, hogyan hozhat létre programozott módon az értékelések.