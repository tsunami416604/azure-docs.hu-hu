---
title: Az Azure-alkalmazások véleményezési visszajelzéseinek kezelése | Azure Piactér
description: Bemutatja, hogyan használhatja az Azure DevOps-ot az Azure-alkalmazásajánlatokra vonatkozó visszajelzések kezelésére az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285299"
---
# <a name="handling-review-feedback"></a>Értékelési visszajelzések kezelése

Ez a cikk bemutatja, hogyan érheti el a Microsoft Azure Piactér felülvizsgálati csapata által használt Azure DevOps-környezetet.  Ha a **Microsoft felülvizsgálati** lépése során kritikus problémák at talál az Azure-alkalmazásajánlatban, bejelentkezhet ebbe a rendszerbe, hogy részletes információkat jelenítsen meg ezekről a problémákról (tekintse át a visszajelzést).  Miután kijavította ezeket a problémákat, újra be kell küldenie az ajánlatot, hogy továbbra is közzétegye az Azure Marketplace-en.  Az alábbi ábra azt mutatja be, hogy ez a visszajelzési folyamat hogyan kapcsolódik a közzétételi folyamathoz.

![Közzétételi lépések az Azure DevOps-visszajelzéssel](./media/pub-flow-vsts-access.png)

A felülvizsgálati problémákra általában lekéréses kérelemként (PR) hivatkoznak.  Minden PR egy online [Azure DevOps](https://azure.microsoft.com/services/devops/) (korábban Visual Studio Team Services (VSTS)) elemhez kapcsolódik, amely a probléma részleteit tartalmazza.  Az alábbi képen egy példa látható egy felülvizsgálat PR-hivatkozás.  Összetett helyzetekben a felülvizsgálati és támogatási csapatok e-mailt is kaphatnak. 

![Az Állapot lap az ellenőrzési visszajelzések megjelenítése](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps-hozzáférés

Az ellenőrzési visszajelzésben hivatkozott PR-elemek megtekintéséhez a kiadóknak először megfelelő engedélyt kell kapniuk.  Ellenkező esetben az új `401 - Not Authorized` közzétevők válaszoldalt kapnak a psz-ek megtekintésekor.  Az Azure DevOps-tárházhoz való hozzáférés kéréséhez hajtsa végre az alábbi lépéseket:

1. Gyűjtse össze a következő információkat:
    - A közzétevő neve és azonosítója
    - Ajánlat típusa (Azure-alkalmazás), ajánlat neve és termékváltozat-azonosító
    - A lekéréses kérelem `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` hivatkozás, például: Ez az URL-cím leolvasható az értesítési üzenetből vagy a 401-es válaszoldal címéből.
    - A kiadószervezet azon személyeinek e-mail címe(i), akiknek hozzáférést szeretne adni.  Ennek a listának tartalmaznia kell a tulajdonos címét(eke)t, amelyet megadott, amikor közzétevőként regisztrál a Cloud Partner Portalon.
2. Hozzon létre egy támogatási incidenst.  A Cloud Partner Portal címsorában válassza a **Súgó gombot,** majd válassza a menü **Támogatás parancsát.**  Az alapértelmezett webböngészőnek el kell indulnia, és meg kell navigálnia a Microsoft új támogatási incidensoldalára.  (Előfordulhat, hogy először be kell jelentkeznie.)
3. Adja meg a **probléma típusát** **piactéri bevezetésként** és **kategória** **access problémaként,** majd válassza a **Kérés indítása**lehetőséget.

    ![Támogatási jegy kategória](./media/support-incident1.png)

4. **Az 1/2 lépésben** adja meg kapcsolattartási adatait, és válassza a **Folytatás**gombot.
5. **A 2/2 lépésben** adja meg az `Request Azure DevOps access`incidens címét (például ), és adja meg az első lépésben (fent) gyűjtött adatokat.  Olvassa el és fogadja el a szerződést, majd válassza a **Küldés lehetőséget.**

Ha az incidens létrehozása sikeres volt, egy megerősítő oldal jelenik meg.  A megerősítő információk mentése ezen az oldalon referenciaként.  A Microsoft támogatási csoportja néhány munkanapon belül válaszol a hozzáférési kérelemre.


## <a name="reviewing-the-pull-request"></a>A lekéréses kérelem áttekintése 

Az alábbi eljárással áttekintheti a lekéréses kérelemben dokumentált problémákat.

1. A **Közzétételi lépések** űrlap **Microsoft-ellenőrzési** szakaszában kattintson egy PR-hivatkozásra a böngésző elindításához, és keresse meg a PR **Áttekintés** (kezdőlap) lapját.  Az alábbi képen egy példa kritikus kérdés kezdőlapja a Contoso minta alkalmazás ajánlat.  Ez az oldal hasznos összefoglaló információkat tartalmaz az Azure-alkalmazásban található ellenőrzési problémákról.  

    [![Lekéréses kérelem kezdőlapja](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kattintson a képre, hogy bontsa ki.*
    
2. (Nem kötelező) Az ablak jobb oldalán, a **Házirendek**szakaszban kattintson a problémaüzenetre (ebben a példában: **A házirend érvényesítése nem sikerült)** a probléma alacsony szintű részleteinek vizsgálatához, beleértve a kapcsolódó naplófájlokat is.  A hibák általában a naplófájlok alján jelennek meg.

3. A kezdőlap bal oldalán található menüben válassza a **Fájlok** lehetőséget az ajánlat technikai eszközeit tartalmazó listafájlok megjelenítéséhez.  A Microsoft-ellenőrzőknek megjegyzéseket kellett volna fűzniük a felfedezett kritikus problémákhoz.  A következő példában két probléma került felderítésre. 

    [![Lekéréses kérelem kezdőlapja](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kattintson a képre, hogy bontsa ki.*

4. Kattintson az egyes megjegyzéscsomóponta a bal oldali fa navigálni a megjegyzés keretében a környező kódot.  Javítsa ki a forráskódot a csapat projektjében a megjegyzésben leírt probléma kijavításához.

> [!Note]
> Az ajánlat technikai eszközei nem szerkeszthetők a felülvizsgálati csapat Azure DevOps-környezetében.  A közzétevők számára ez a tartalmazott forráskód írásvédett környezete.  A microsoftos felülvizsgálati csapat javára azonban hagyhat válaszokat a megjegyzésekre.

   A következő példában a közzétevő áttekintette, javította és válaszolt az első problémára.

   ![Első fix és megjegyzés válasz](./media/first-comment-reply.png)


## <a name="next-steps"></a>További lépések

Miután kijavította az ellenőrzési PR(ek)ben dokumentált kritikus problémákat, újra közzé kell [tennie az Azure-alkalmazásajánlatát.](./cpp-publish-offer.md)
