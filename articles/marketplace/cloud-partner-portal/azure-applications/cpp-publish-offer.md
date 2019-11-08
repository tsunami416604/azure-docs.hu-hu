---
title: Azure-alkalmazás ajánlatának közzététele | Azure piactér
description: Útmutató Azure-alkalmazások Azure Marketplace-en való közzétételének folyamatához és lépéseihez.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826166"
---
# <a name="publish-azure-application-offer"></a>Azure-alkalmazásajánlat közzététele

Miután létrehozta az ajánlatot az **új ajánlat** oldalon található információk megadásával, közzéteheti az ajánlatot. A közzétételi folyamat elindításához válassza a **Közzététel** lehetőséget.

A következő ábra a közzétételi folyamat fő lépéseit mutatja be, amelyekkel az ajánlat "Go Live".

![Ajánlat-közzétételi lépések](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat felsorolja és leírja az egyes közzétételi lépéseket, és az egyes lépések befejezésének időpontját jeleníti meg.  A "Days" időszakra vonatkozó becslések munkanapokként vannak meghatározva, ami hétvégi és ünnepnapokat is kizár.

|  **Közzétételi lépés**           | **Idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | < 15 perc    | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Befolyásolt bevételi beállítások ellenőrzése | < 15 perc  | Az ajánlathoz tartozó Azure-Erőforrás-használati jóváírás be van jelölve.             |
| Tanúsítvány                  | < 1 nap     | Az ajánlatot az Azure minősítési csapat elemzi. Az ajánlat vírusok, kártevő szoftverek, biztonsági megfelelőségi és biztonsági problémák vizsgálatára szolgál. A rendszer ellenőrzi, hogy az ajánlat megfelel-e az összes jogosultsági feltételnek. További információ: [Előfeltételek](./cpp-prerequisites.md). A rendszer visszajelzést küld, ha problémát talál. |
| Tesztelési meghajtó ellenőrzése          | < 2 óra   | Választható Ha van elérhető tesztvezetés, a Microsoft ellenőrzi, hogy telepíthető és replikálható-e.  |
| Csomagolás és vezető generáció regisztrációja | < 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva és üzembe vannak állítva. |
|  Közzétevő kijelentkezése             |  Kézi    | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlat már elérhető előzetes verzióra.  Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  Az ajánlat ellenőrzése után válassza az **élő indítás** lehetőséget, hogy ajánlata a következő lépésre léphet. |
| Microsoft-áttekintés                | 7-14 nap | A Microsoft holisztikusan áttekinti az Azure-alkalmazást és e-maileket, ha problémák merülnek fel.  Ennek a lépésnek a hossza az alkalmazás összetettsége, a feltárt problémák és a rájuk vonatkozó azonnali reagálás függvénye.  |
| Élő                           | < 1 nap | Az ajánlat megjelent, replikálva van a megadott régiókban, és elérhetővé válik a nyilvánosság számára. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

A közzétételi folyamatot a Cloud Partner Portal az ajánlat **állapot** lapján figyelheti.

![Az Azure app-ajánlat állapot lapja](./media/offer-status-tab.png)

A közzétételi folyamat befejezése után az ajánlat a [Microsoft Azure Marketplace alkalmazás kategóriában](https://azuremarketplace.microsoft.com/marketplace/apps/)jelenik meg.

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](../../cloud-solution-providers.md) című témakört.

## <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

Az ajánlat közzétételi állapotának megjelenítése mellett az **állapot** lap a hibaüzeneteket és a visszajelzéseket is megjeleníti minden olyan közzétételi lépésnél, ahol probléma merült fel.  Ha a probléma kritikus, akkor a közzététel megszakad.  Ezután ki kell javítania a jelentett problémát (ka) t, majd újra közzé kell tennie az ajánlatot.  Mivel a **Microsoft felülvizsgálati** lépése az ajánlat és a hozzá kapcsolódó technikai eszközök (különösen a Azure Resource Manager sablon) átfogó áttekintését jelenti, a problémák általában lekéréses kérelmek (PR) hivatkozásaiként jelennek meg.  A következő témakörből megtudhatja, hogyan tekintheti meg és válaszolhatja meg ezeket a jogcímeket: [felülvizsgálati visszajelzések feldolgozása](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>További lépések

Ha a közzétételi lépések közül egy vagy több hibát észlelt, ki kell javítania őket, és újra közzé kell tennie az ajánlatot.  Ha a **Microsoft felülvizsgálati** lépésben kritikus problémák léptek fel, [a felülvizsgálati visszajelzést](./cpp-handling-review-feedback.md) a Microsoft felülvizsgálati csapat Azure DevOps-tárházának elérésével kell kezelnie.

Az Azure-alkalmazások sikeres közzétételekor [frissítheti a meglévő ajánlatot](./cpp-update-existing-offer.md) , hogy tükrözze az üzleti vagy műszaki követelmények módosítását. 
