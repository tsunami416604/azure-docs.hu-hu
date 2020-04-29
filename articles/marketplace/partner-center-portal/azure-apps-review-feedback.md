---
title: Az Azure apps ajánlatra vonatkozó felülvizsgálati visszajelzések a kereskedelmi piactéren
description: Az Azure-alkalmazások felülvizsgálati visszajelzésének kezelése az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztül a Microsoft partner Center kereskedelmi piactér portálján.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279793"
---
# <a name="handling-review-feedback"></a>Értékelési visszajelzések kezelése

Ez a cikk azt ismerteti, hogyan érheti el az Microsoft Azure Marketplace felülvizsgálati csapat által használt Azure DevOps-környezetet. Ha a **Microsoft felülvizsgálati** lépése során kritikus problémák léptek fel az Azure-alkalmazás ajánlatában, bejelentkezhet a rendszerbe a problémák részletes adatainak megtekintéséhez (visszajelzés megtekintése). Az összes probléma kijavítása után újból el kell küldenie az ajánlatát, hogy továbbra is közzé lehessen tenni az Azure piactéren. A következő ábra azt szemlélteti, hogy a visszajelzési folyamat hogyan kapcsolódik a közzétételi folyamathoz.

![Visszajelzési folyamat áttekintése](./media/review-feedback-process.png)

A felülvizsgálati problémák jellemzően lekéréses kérelemként (PR) vannak hivatkozva. Mindegyik PR egy online [Azure-DevOps](https://azure.microsoft.com/services/devops/) (korábban a Visual Studio Team Services (vsts) nevű) elemhez van társítva, amely a probléma részleteit tartalmazza. Az alábbi képen egy példa látható a partner Center-élményre, ha az ellenőrzések során problémák merülnek fel. 

![Közzététel állapota](./media/publishing-status.png)

A beküldéssel kapcsolatos konkrét adatokat tartalmazó PR-t a "tanúsítási jelentés megtekintése" hivatkozásra kattintva lehet megemlíteni. Összetett helyzetekben a felülvizsgálati és támogatási csapat is elküldheti e-mailben.

## <a name="azure-devops-access"></a>Azure DevOps-hozzáférés

A partner Centerben a "fejlesztői" szerepkörhöz hozzáféréssel rendelkező összes felhasználó hozzáférhet a felülvizsgálati visszajelzések című témakörben hivatkozott PR-elemek megtekintéséhez.

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

A következő eljárással ellenőrizheti a lekéréses kérelemben dokumentált problémákat.

1. A közzétételi lépések űrlap **Microsoft felülvizsgálati** szakaszaiban kattintson egy PR-hivatkozásra a böngésző elindításához, és keresse meg a PR **Áttekintés** (Kezdőlap) lapját. Az alábbi ábrán egy példa látható a contoso minta app-ajánlat kritikus fontosságú kezdőlapján. Ez az oldal hasznos összegző információkat tartalmaz az Azure-alkalmazásban található felülvizsgálati problémákról.

    [![Lekéréses kérelem kezdőlapja](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kattintson a képre a kibontáshoz.*

1. Választható Az ablak jobb oldalán, a **szabályzatok**szakaszban kattintson a probléma üzenetre (ebben a példában a **házirend ellenőrzése nem sikerült**) a probléma alacsony szintű részleteinek vizsgálatához, beleértve a kapcsolódó naplófájlokat is. A hibák általában a naplófájlok alján jelennek meg.
1. A Kezdőlap bal oldali menüjében válassza a **fájlok** lehetőséget, hogy megjelenjenek az ajánlat technikai eszközeiből álló fájlok. A Microsoft felülvizsgálók számára a felderített kritikus problémák leírását ismertető megjegyzésekkel kell rendelkeznie. A következő példában két probléma merült fel.

    [![Lekéréses kérelem kezdőlapja](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kattintson a képre a kibontáshoz.*

1. Kattintson a bal oldali fában található minden Megjegyzés csomópontra a környező kód kontextusában található megjegyzéshez. Javítsa ki a forráskódját a csapat projektjeiben a megjegyzésben leírt probléma kijavításához.

>[!Note]
>Az ajánlat technikai eszközei nem szerkeszthetők a felülvizsgálati csapat Azure DevOps-környezetében. A kiadók esetében ez egy írásvédett környezet a foglalt forráskódhoz. A válaszokat azonban a Microsoft felülvizsgálati csapatának javára is elhagyhatja a megjegyzésekben.

   A következő példában a közzétevő áttekintette, kijavította és megválaszolta az első problémát.

   ![Első javítás és Megjegyzés válasza](./media/first-comment-reply.png)

## <a name="next-steps"></a>További lépések

Miután javította a PR (k) felülvizsgálatával kapcsolatos kritikus problémákat, újra közzé kell tennie az [Azure-alkalmazás ajánlatát](./create-new-azure-apps-offer.md#publish).
