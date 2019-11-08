---
title: Az Azure-alkalmazások felülvizsgálati visszajelzésének feldolgozása | Azure piactér
description: Ez a cikk azt ismerteti, hogyan használható az Azure DevOps az Azure Marketplace-re vonatkozó Azure-alkalmazási ajánlatok felülvizsgálati visszajelzésének kezeléséhez.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827590"
---
# <a name="handling-review-feedback"></a>Értékelési visszajelzések kezelése

Ez a cikk azt ismerteti, hogyan érheti el az Microsoft Azure Marketplace felülvizsgálati csapat által használt Azure DevOps-környezetet.  Ha a **Microsoft felülvizsgálati** lépése során kritikus problémák léptek fel az Azure-alkalmazás ajánlatában, bejelentkezhet a rendszerbe a problémák részletes adatainak megtekintéséhez (visszajelzés megtekintése).  Az összes probléma kijavítása után újból el kell küldenie az ajánlatát, hogy továbbra is közzé lehessen tenni az Azure piactéren.  A következő ábra azt szemlélteti, hogy a visszajelzési folyamat hogyan kapcsolódik a közzétételi folyamathoz.

![Közzétételi lépések az Azure DevOps-visszajelzéssel](./media/pub-flow-vsts-access.png)

A felülvizsgálati problémák jellemzően lekéréses kérelemként (PR) vannak hivatkozva.  Mindegyik PR egy online [Azure-DevOps](https://azure.microsoft.com/services/devops/) (korábban a Visual Studio Team Services (vsts) nevű) elemhez van társítva, amely a probléma részleteit tartalmazza.  Az alábbi ábrán egy példa látható a felülvizsgálati PR-referenciára.  Összetett helyzetekben a felülvizsgálati és támogatási csapat is elküldheti e-mailben. 

![A felülvizsgálati visszajelzéseket megjelenítő állapot lap](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps-hozzáférés

Ha meg szeretné tekinteni a felülvizsgálati visszajelzésben hivatkozott PR-elemeket, a közzétevőnek először megfelelő jogosultságot kell biztosítania.  Ellenkező esetben az új közzétevők `401 - Not Authorized` válasz lapot kapnak, amikor a rendszer megpróbálja megtekinteni a következőt:.  Ha hozzáférést szeretne kérni ehhez az Azure DevOps-tárházhoz, hajtsa végre a következő lépéseket:

1. Gyűjtse össze a következő információkat:
    - A közzétevő neve és azonosítója
    - Ajánlat típusa (Azure-alkalmazás), ajánlat neve és SKU-azonosítója
    - A pull-kérelem hivatkozása, például: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` ez az URL-cím az értesítési üzenetből vagy az 401-válasz oldal címéből kérhető le.
    - A közzétételi szervezet azon tagjainak e-mail-címe (i), akik számára hozzáférést kíván biztosítani.  Ennek a listának tartalmaznia kell a tulajdonosi címeket, amelyeket a Cloud Partner Portal közzétevőként való regisztrálásakor adott meg.
2. Hozzon létre egy támogatási eseményt.  A Cloud Partner Portal címsorában válassza a **Súgó** gombot, majd a menüben válassza a **támogatás** lehetőséget.  Az alapértelmezett webböngészőnek el kell indítania a programot, és meg kell keresnie a Microsoft új támogatási incidens oldalát.  (Előfordulhat, hogy először be kell jelentkeznie.)
3. A **probléma típusaként** adja meg a **piactér** bevezetését és a **kategóriát** **hozzáférési problémaként**, majd válassza a **kérelem indítása**lehetőséget.

    ![Támogatási jegy kategóriája](./media/support-incident1.png)

4. Az **1. lépés 2. lépése** lapon adja meg a kapcsolattartási adatait, és válassza a **Folytatás**lehetőséget.
5. A **2. lépésben** válassza ki az incidens címét (például `Request Azure DevOps access`), és adja meg az első lépésben összegyűjtött adatokat (fentiekben).  Olvassa el és fogadja el a szerződést, majd válassza a **Submit (Küldés**) lehetőséget.

Ha az incidens létrehozása sikeres volt, egy megerősítést kérő lap jelenik meg.  Mentse a megerősítő információkat ezen az oldalon a hivatkozáshoz.  A Microsoft ügyfélszolgálata csapatnak néhány munkanapon belül válaszolnia kell a hozzáférési kérelmére.


## <a name="reviewing-the-pull-request"></a>A lekéréses kérelem áttekintése 

A következő eljárással ellenőrizheti a lekéréses kérelemben dokumentált problémákat.

1. A **közzétételi lépések** űrlap **Microsoft felülvizsgálati** szakaszában kattintson egy PR-hivatkozásra a böngésző elindításához, és keresse meg a PR **Áttekintés** (Kezdőlap) lapját.  Az alábbi ábrán egy példa kritikus probléma kezdőlapja látható a contoso minta alkalmazás ajánlatához.  Ez az oldal hasznos összegző információkat tartalmaz az Azure-alkalmazásban található felülvizsgálati problémákról.  

    [![pull-kérelem kezdőlapja](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kattintson a képre a kibontáshoz.*
    
2. Választható Az ablak jobb oldalán, a **szabályzatok**szakaszban kattintson a probléma üzenetre (ebben a példában a **házirend ellenőrzése nem sikerült**) a probléma alacsony szintű részleteinek vizsgálatához, beleértve a kapcsolódó naplófájlokat is.  A hibák általában a naplófájlok alján jelennek meg.

3. A Kezdőlap bal oldali menüjében válassza a **fájlok** lehetőséget, hogy megjelenjenek az ajánlat technikai eszközeiből álló fájlok.  A Microsoft felülvizsgálók számára a felderített kritikus problémák leírását ismertető megjegyzésekkel kell rendelkeznie.  A következő példában két probléma merült fel. 

    [![pull-kérelem kezdőlapja](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kattintson a képre a kibontáshoz.*

4. Kattintson a bal oldali fában található minden Megjegyzés csomópontra a környező kód kontextusában található megjegyzéshez.  Javítsa ki a forráskódját a csapat projektjeiben a megjegyzésben leírt probléma kijavításához.

> [!Note]
> Az ajánlat technikai eszközei nem szerkeszthetők a felülvizsgálati csapat Azure DevOps-környezetében.  A kiadók esetében ez egy írásvédett környezet a foglalt forráskódhoz.  A válaszokat azonban a Microsoft felülvizsgálati csapatának javára is elhagyhatja a megjegyzésekben.

   A következő példában a közzétevő áttekintette, kijavította és megválaszolta az első problémát.

   ![Első javítás és Megjegyzés válasza](./media/first-comment-reply.png)


## <a name="next-steps"></a>További lépések

Miután javította a PR (k) felülvizsgálatával kapcsolatos kritikus problémákat, újra közzé kell tennie az [Azure-alkalmazás ajánlatát](./cpp-publish-offer.md).
