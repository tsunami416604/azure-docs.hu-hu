---
title: Az Azure áttekintése visszajelzés az alkalmazásról – Azure Marketplace-en kezelése |} A Microsoft Docs
description: Azt ismerteti, hogyan használható az Azure DevOps Azure-alkalmazásokat, amelyek felülvizsgálati visszajelzés kezelése az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 98ab9b25c59af194a1a81c600c373b2180272e10
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106953"
---
# <a name="handling-review-feedback"></a>Felülvizsgálat visszajelzés kezelése

Ez a cikk bemutatja, hogyan érhető el az Azure DevOps-környezetben használja a Microsoft Azure Marketplace-en csapatától.  Ha az Azure-alkalmazás ajánlatban alatt található kritikus fontosságú problémáit a **Microsoft felülvizsgálati** lépésben is jelentkezik be a rendszer az ezekkel kapcsolatos részletes információkért, (tekintse át a visszajelzés).  Után ezek a problémák elhárításához, újból el kell küldenie az ajánlat az Azure piactéren közzéteheti a folytatáshoz.  A következő ábra szemlélteti, hogyan viszonyul a visszajelzés folyamat a közzétételi folyamat.

![VSTS-visszajelzés a közzétételi lépéseket](./media/pub-flow-vsts-access.png)

Felülvizsgálat problémák általában, a lekéréses kérelmet (PR) hivatkozott.  Minden egyes lekéréses kérelem kapcsolódik egy online [Azure DevOps](https://azure.microsoft.com/services/devops/) (korábbi nevén Visual Studio Team Services (VSTS)) elemet, amely tartalmazza a probléma részleteit.  Az alábbi képen látható a Pull-kérelem felülvizsgálati hivatkozást egy példát.  Összetett helyzetekben a felülvizsgálat és támogatási csapatok előfordulhat, hogy is e-mailben. 

![Tekintse át visszajelzés megjelenítésének állapota lap](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>VSTS-hozzáférés

Felülvizsgálat visszajelzés hivatkozott PR elemek megtekintéséhez, közzétevők először biztosítani kell megfelelő jogosultságokkal.  Ellenkező esetben az új közzétevők kap egy `401 - Not Authorized` válasz lapon megtekintheti a lekéréses kérelmek tett kísérlet során.  Kérjen hozzáférést az Azure DevOps-tárházba, hajtsa végre az alábbi lépéseket:

1. Gyűjtse össze a következő információkat:
    - A közzétevő neve és azonosítója
    - Csomag típusa (Azure-alkalmazás), a neve, és az SKU-azonosítója
    - A lekéréses kérelem mutató hivatkozást, például: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Az URL-cím az értesítési üzenet vagy a 401-es választ lap címe lekérhetők.
    - A közzétételi szervezet szeretne hozzáférést biztosítani, hogy a felhasználók e-mail-címeit.  A listában szerepelnie kell a tulajdonos cím közzétevőként a Cloud Partner portálra a regisztráció során megadott.
2. Hozzon létre egy támogatási eseményt.  A címsorban látható, a Cloud Partner portálra, válassza ki a **súgó** gombra, majd válassza a **támogatási** a menüből.  Alapértelmezett web browser kell indítsa el, és nyissa meg a Microsoft új támogatási incidens lapot.  (Előfordulhat, hogy kell jelentkeznie.)
3. Adja meg a **Problem type** , **marketplace bevezetési** és **kategória** , **hozzáférési probléma**, majd válassza ki **indítása kérelem**.

    ![Támogatási jegy kategória](./media/support-incident1.png)

4. A **1 / 2. lépés** lapon adja meg a kapcsolattartási adatokat, és válassza **Folytatás**.
5. A **2. lépés 2** csoportjában adja meg az incidens címe (például `Request VSTS access`), és adja meg az első (feljebb) lépésben az összegyűjtött információkat.  Olvassa el és fogadja el a szerződést, majd válassza ki **küldés**.

Ha az incidens létrehozása sikeres volt, egy megerősítő oldal jelenik meg.  Mentse a megerősítő információkat referenciaként ezen az oldalon.  A Microsoft támogatási csapata néhány munkanapon belül kell válaszolnia a hozzáférési kérelem.


## <a name="reviewing-the-pull-request"></a>A lekéréses kérelem áttekintése 

Az alábbi eljárás segítségével áttekintheti a lekéréses kérelemben szereplő hibákat.

1. Az a **Microsoft felülvizsgálati** szakaszában **közzétételi lépéseket** alkotnak, indítsa el a böngészőt, és keresse meg a lekéréses kérelem hivatkozásra a **áttekintése** ezen tartalomcsapat (kezdőlap) lapján  Az alábbi képen egy példa kritikus probléma kezdőlapja a Contoso minta app ajánlat mutatja be.  Ezen a lapon található az Azure-alkalmazást a felülvizsgálati problémák hasznos összegző információit tartalmazza.  

    [ ![A lekéréses kérelem kezdőlapja](./media/pr-home-page-thumb.png) ](./media/pr-home-page.png)
    <br/> *Kattintson a képre kattintva bontsa ki.*
    
2. (Nem kötelező) A szakaszban, az ablak jobb oldalán **házirendek**, kattintson a probléma üzenet (ebben a példában: **Házirend érvényesítése nem sikerült**) a probléma, beleértve a kapcsolódó naplófájlok a kevésbé fontos részletek vizsgálatára.  A naplófájlok alján általában jelenik meg hibaüzenet.

3. Válassza a bal oldali a kezdőlap menüben **fájlok** megjelenítése alkotó ezt az ajánlatot a technikai eszközök fájlok listázása.  A Microsoft megtekinteni a felderített kritikus fontosságú problémáit leíró megjegyzések kell hozzáadni.  A következő példában két problémát észlelt. 

    [ ![A lekéréses kérelem kezdőlapja](./media/pr-files-page-thumb.png) ](./media/pr-files-page.png)
    <br/> *Kattintson a képre kattintva bontsa ki.*

4. Kattintson a bal oldali fában, keresse meg a megjegyzést a környezetben a környező kód minden megjegyzést csomópontján.  Javítsa ki a forráskódot a csapatprojektben leírtak szerint a megjegyzést a probléma.

> [!Note]
> Az ajánlat technikai eszközök a felülvizsgálati csapat Azure DevOps-környezetben már nem szerkesztheti.  A kiadók Ez az egy csak olvasható környezetben a tartalmazott forráskódja számára.  Azonban a megjegyzéseket, a Microsoft csapatától innovációval-válaszok hagyhatja.

   A következő példában a közzétevő rendelkezik vizsgálni, javítani és válaszolt az első kiadás.

   ![Javítás és a megjegyzés első válasz](./media/first-comment-reply.png)


## <a name="next-steps"></a>További lépések

Miután a kritikus fontosságú problémáit, a felülvizsgálat PR(s) dokumentálva, kell [újbóli közzététele az Azure-alkalmazás ajánlat](./cpp-publish-offer.md).
