---
title: Közzététel Azure alkalmazást offer – Azure Marketplace-en |} A Microsoft Docs
description: A folyamat és az Azure Marketplace-en az Azure application ajánlat közzétételének lépéseit ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: 2adf07cf2337611b9136af47ce6a35b617e2e9ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177032"
---
# <a name="publish-azure-application-offer"></a>Az Azure application ajánlat közzététele

Az adatokat azáltal, hogy az ajánlat létrehozása után a **új ajánlat** lap, közzéteheti az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható a fő lépések egy "az élő esemény indításra" ajánlat a közzétételi folyamat.

![Az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat sorolja fel és közzétételi lépésre ismerteti, és egy becsült minden egyes időtartam biztosít.  Időpontokban becslése a "days" munkanapon belül vannak meghatározva, amely zárja ki a hétvégéket és a munkaszüneti napokat.

|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | < 15 perc    | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Befolyásolt bevétel beállítások érvényesítése | < 15 perc  | Az ajánlat Azure-Erőforrás kihasználtsága attribution be van jelölve.             |
| Tanúsítvány                  | < 1 nap     | Az ajánlat az Azure hitelesítő csapata által elemzett. Az ajánlat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat vizsgálja. Az ajánlat be van jelölve, hogy megfelel-e az összes megfelelőségi feltételeket megtekintéséhez. További információkért lásd: [Előfeltételek](./cpp-prerequisites.md). Ha problémát talál, visszajelzés érkezett. |
| Teszt meghajtó ellenőrzése          | < 2 óra   | (Nem kötelező) Ha jelen egy Test Drive, a Microsoft ellenőrzi, hogy azt telepíthető és replikálja.  |
| Formátumokat támogató csomagolási és Érdeklődők generálása regisztráció | < 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az átfutási rendszerek konfigurált és telepített. |
|  Közzétevő jóváhagyás             |  Manuális    | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat már elérhető előzetes verzióban.  Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  Miután ellenőrizte az ajánlatot, válassza ki a **élő** , az ajánlat helyezheti át a következő lépéssel. |
| A Microsoft áttekintése                | 7 – 14 nap | A Microsoft azok alkalmazásfüggőségeit áttekinti az Azure-alkalmazásokat, és e-mailt küld, ha problémák.  Ebben a lépésben hossza attól függ, hogy az alkalmazást, a felderített hibák, és hogyan azonnal reagálhat rájuk bonyolultságát.  |
| Élő                           | < 1 nap | Az ajánlat nyilvánosan, a megadott régióban replikálja, és nyilvánosan elérhetővé. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

 
Figyelheti a közzétételi folyamat a **állapot** az ajánlatra, a Cloud Partner portálra a lap.

![Az Azure-alkalmazás ajánlat állapota lap](./media/offer-status-tab.png)

Miután elvégezte a közzétételi folyamat, az ajánlat jelenik meg a [Microsoft Azure Marketplace-alkalmazás kategóriához](https://azuremarketplace.microsoft.com/marketplace/apps/).



## <a name="errors-and-review-feedback"></a>Hibák és a felülvizsgálati visszajelzés

Az ajánlat közzétételi állapotának megjelenítése mellett a **állapota** lap is megjeleníti a hibaüzenetek és visszajelzései a **Microsoft felülvizsgálati** . lépés.  Felülvizsgálat problémák általában, a lekéréses kérelmet (PR) hivatkozott.  Minden lekéréses kérelmet kapcsolódik egy online Visual Studio Team Services (vsts-ben, átnevezett [Azure DevOps](https://azure.microsoft.com/services/devops/)) elemet, amely tartalmazza a probléma részleteit.  Az alábbi képen látható a Pull-kérelem felülvizsgálati hivatkozást egy példát.  Összetett helyzetekben a felülvizsgálat és támogatási csapatok előfordulhat, hogy e-mailben. 

![Tekintse át visszajelzés megjelenítésének állapota lap](./media/status-tab-ms-review.png)

Az ajánlat keretében a közzétételi folyamat folytatása előtt meg kell oldania az összes jelentett hiba.  A következő ábra szemlélteti, hogyan viszonyul a visszajelzés folyamat a közzétételi folyamat.

![VSTS-visszajelzés a közzétételi lépéseket](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS-hozzáférés

Felülvizsgálat visszajelzés hivatkozott VSTS elemek megtekintéséhez, közzétevők kell biztosítani megfelelő jogosultságokkal.  Ellenkező esetben az új közzétevők kap egy `401 - Not Authorized` válasz lap.  Az ajánlat áttekintése VSTS rendszer leegyszerűsödött, hajtsa végre az alábbi lépéseket:

1. Gyűjtse össze a következő információkat:
    - A közzétevő neve és azonosítója
    - Csomag típusa (Azure-alkalmazás), a neve, és az SKU-azonosítója
    - A lekéréses kérelem mutató hivatkozást, például: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Az URL-cím az értesítési üzenet vagy a 401-es választ lap címe lekérhetők.
    - A közzétételi szervezet szeretne hozzáférést biztosítani, hogy a felhasználók e-mail-címeit.  Ezek a tulajdonos cím közzétevőként a Cloud Partner portálra a regisztráció során megadott tartalmaznia kell.
2. Hozzon létre egy támogatási eseményt.  A címsorban látható, a Cloud Partner portálra, válassza ki a **súgó** gombra, majd válassza a **támogatási** a menüből.  Alapértelmezett web browser kell indítsa el, és nyissa meg a Microsoft új támogatási incidens lapot.  (Előfordulhat, hogy kell jelentkeznie.)
3. Adja meg a **Problem type** , **marketplace bevezetési** és **kategória** , **hozzáférési probléma**, majd válassza ki **indítása kérelem**.

    ![Támogatási jegy kategória](./media/support-incident1.png)

4. A **1 / 2. lépés** lapon adja meg a kapcsolattartási adatokat, és válassza **Folytatás**.
5. A **2. lépés 2** csoportjában adja meg az incidens címe (például `Request VSTS access`), és adja meg az első (feljebb) lépésben az összegyűjtött információkat.  Olvassa el és fogadja el a szerződést, majd válassza ki **küldés**.

Ha az incidens létrehozása sikeres volt, egy megerősítő oldal jelenik meg.  Mentse a megerősítő információkat referenciaként.  A Microsoft támogatási néhány munkanapon belül kell válaszolnia a hozzáférési kérelem.


## <a name="next-steps"></a>További lépések

Egy Azure-alkalmazás közzététele után is [létező ajánlat frissítése](./cpp-update-existing-offer.md) megfelelően a változó üzleti vagy technikai követelményeket. 
