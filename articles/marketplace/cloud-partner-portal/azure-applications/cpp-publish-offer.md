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
ms.openlocfilehash: cafda8a48f9160c80edb02c3452035f912958bc7
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098221"
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

Az ajánlat közzétételi állapotának megjelenítése mellett az **állapot** lapon megjelenik a hibaüzenetek és visszajelzései olyan közzétételi lépéseket, ahol a rendszer észlelt egy problémát.  Ha a probléma fontos, majd közzététele megszakítva.  Kell majd javítsa ki a jelentett hiba/hibák meghatározásához, és tegye közzé újra az ajánlatot.  Mivel a **Microsoft felülvizsgálati** lépés egy kiterjedt át kell tekinteni az ajánlat és a kapcsolódó technikai eszközökre (különösen az Azure Resource Manager-sablon) jelöli, a problémák általában bemutatják, a lekéréses kérelmet (PR) hivatkozásokat.  Megtekintése, és ezek a lekéréses kérelmek megválaszolásához magyarázatát lásd [kezelésének áttekintése visszajelzés](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>További lépések

Egy vagy több közzétételi lépéseket, hibát, ha kell, javítsa ki azokat, és közzé az ajánlatot még.  Ha a kritikus fontosságú problémáit a program hibát a **áttekintése a Microsoft** lépésben kell [kezelni a felülvizsgálati visszajelzés](./cpp-handling-review-feedback.md) elérése a Microsoft által tekintse át az Azure DevOps-tárház csapata.

Miután egy Azure-alkalmazáshoz sikeresen közzé lett téve, akkor is [a létező ajánlat frissítése](./cpp-update-existing-offer.md) megfelelően a változó üzleti vagy technikai követelményeket. 
