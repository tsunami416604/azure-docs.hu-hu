---
title: Az Azure Apps-ajánlat véleményezési visszajelzéseinek kezelése a kereskedelmi piactéren
description: Az Azure-alkalmazások ajánlatára vonatkozó visszajelzések kezelése az Azure Piactéren, az AppSource-ban vagy a Felhőszolgáltató (CSP) programon keresztül történő listázáshoz vagy értékesítéshez a Microsoft Partner Center Kereskedelmi piactér portálján keresztül.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279793"
---
# <a name="handling-review-feedback"></a>Értékelési visszajelzések kezelése

Ez a cikk bemutatja, hogyan érheti el a Microsoft Azure Piactér felülvizsgálati csapata által használt Azure DevOps-környezetet. Ha a **Microsoft felülvizsgálati** lépése során kritikus problémák at talál az Azure-alkalmazásajánlatban, bejelentkezhet ebbe a rendszerbe, hogy részletes információkat jelenítsen meg ezekről a problémákról (tekintse át a visszajelzést). Miután kijavította ezeket a problémákat, újra be kell küldenie az ajánlatot, hogy továbbra is közzétegye az Azure Marketplace-en. Az alábbi ábra azt mutatja be, hogy ez a visszajelzési folyamat hogyan kapcsolódik a közzétételi folyamathoz.

![Visszajelzési folyamat áttekintése](./media/review-feedback-process.png)

A felülvizsgálati problémákra általában lekéréses kérelemként (PR) hivatkoznak. Minden PR egy online [Azure DevOps](https://azure.microsoft.com/services/devops/) (korábban Visual Studio Team Services (VSTS)) elemhez kapcsolódik, amely a probléma részleteit tartalmazza. Az alábbi képen egy példa látható a Partnerközpont élményére, ha problémákat talál az értékelések során. 

![Közzététel állapota](./media/publishing-status.png)

A PR, amely konkrét részleteket tartalmaz a benyújtásra, a "Minősítési jelentés megtekintése" linkre kerül. Összetett helyzetekben a felülvizsgálati és támogatási csapatok e-mailt is kaphatnak.

## <a name="azure-devops-access"></a>Azure DevOps-hozzáférés

A Partnerközpontban a "fejlesztői" szerepkörhöz hozzáféréssel rendelkező összes felhasználó hozzáférhet az ellenőrzési visszajelzésekben hivatkozott PR-elemek megtekintéséhez.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>A lekéréses kérelem áttekintése

Az alábbi eljárással áttekintheti a lekéréses kérelemben dokumentált problémákat.

1. A Közzétételi lépések űrlap **Microsoft-ellenőrzési** szakaszában kattintson egy PR-hivatkozásra a böngésző elindításához, és keresse meg a PR **Áttekintés** (kezdőlap) lapját. Az alábbi képen egy példa a kritikus kérdés kezdőlapja a Contoso minta alkalmazás ajánlat. Ez az oldal hasznos összefoglaló információkat tartalmaz az Azure-alkalmazásban található ellenőrzési problémákról.

    [![Lekéréses kérelem kezdőlapja](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kattintson a képre, hogy bontsa ki.*

1. (Nem kötelező) Az ablak jobb oldalán, a **Házirendek**szakaszban kattintson a problémaüzenetre (ebben a példában: **A házirend érvényesítése nem sikerült)** a probléma alacsony szintű részleteinek vizsgálatához, beleértve a kapcsolódó naplófájlokat is. A hibák általában a naplófájlok alján jelennek meg.
1. A kezdőlap bal oldalán található menüben válassza a **Fájlok** lehetőséget az ajánlat technikai eszközeit tartalmazó listafájlok megjelenítéséhez. A Microsoft-ellenőrzőknek megjegyzéseket kellett volna fűzniük a felfedezett kritikus problémákhoz. A következő példában két probléma került felderítésre.

    [![Lekéréses kérelem kezdőlapja](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kattintson a képre, hogy bontsa ki.*

1. Kattintson az egyes megjegyzéscsomóponta a bal oldali fa navigálni a megjegyzés keretében a környező kódot. Javítsa ki a forráskódot a csapat projektjében a megjegyzésben leírt probléma kijavításához.

>[!Note]
>Az ajánlat technikai eszközei nem szerkeszthetők a felülvizsgálati csapat Azure DevOps-környezetében. A közzétevők számára ez a tartalmazott forráskód írásvédett környezete. A microsoftos felülvizsgálati csapat javára azonban hagyhat válaszokat a megjegyzésekre.

   A következő példában a közzétevő áttekintette, javította és válaszolt az első problémára.

   ![Első fix és megjegyzés válasz](./media/first-comment-reply.png)

## <a name="next-steps"></a>További lépések

Miután kijavította az ellenőrzési PR(ek)ben dokumentált kritikus problémákat, újra közzé kell [tennie az Azure-alkalmazásajánlatát.](./create-new-azure-apps-offer.md#publish)
