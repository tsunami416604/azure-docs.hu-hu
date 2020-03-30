---
title: Azure-alkalmazásajánlat közzététele | Azure Piactér
description: Ismerteti az Azure-alkalmazásajánlat azure-piacon való közzétételének folyamatát és lépéseit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280099"
---
# <a name="publish-azure-application-offer"></a>Azure-alkalmazásajánlat közzététele

Miután létrehozott egy ajánlatot az **Új ajánlat** lapon található információk megadásával, közzéteheti az ajánlatot. A **közzétételi** folyamat elindításához válassza a Közzététel lehetőséget.

Az alábbi ábra a közzétételi folyamat fő lépéseit mutatja be a "go live" ajánlathoz.

![Közzétételi lépések ajánlata](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat felsorolja és ismerteti az egyes közzétételi lépéseket, és időbecslést ad az egyes lépésekkel.  A "napokban" megadott időbecslések munkanapokként vannak meghatározva, amelyek nem tartalmazzák a hétvégéket és a munkaszüneti napokat.

|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek érvényesítése         | < 15 min    | Az ajánlati információk és az ajánlatbeállítások érvényesítve vannak.                        |
| Befolyásoló bevételi beállítások ellenőrzése | < 15 min  | Az azure-beli erőforrás-használati hozzárendelés az ajánlat ellenőrzi.             |
| Tanúsítvány                  | < 1 nap     | Az ajánlatot az Azure certification team elemzi. A csapat vírusok, rosszindulatú programok, biztonsági megfelelőség és biztonsági problémák szempontjából ellenőrzi az ajánlatot. Ellenőrzi, hogy az ajánlat megfelel-e az összes jogosultsági feltételnek. További információt az [Előfeltételek](./cpp-prerequisites.md)című témakörben talál. A probléma esetén visszajelzést kap. |
| A tesztvezetés érvényesítése          | < 2 óra   | (Nem kötelező) Ha van tesztmeghajtó, a Microsoft ellenőrzi, hogy telepíthető és replikálható-e.  |
| Csomagolás és ólomtermelés regisztrációja | < 1 óra  | Az ajánlat technikai eszközeit az ügyfelek számára csomagolják, és az érdeklődőrendszereket konfigurálják és telepítik. |
|  Kiadói kijelentkezés             |  Kézi    | Végső megjelenítői áttekintés és megerősítés az ajánlat élesedése előtt. Az ajánlat már elérhető előzetes verzióhoz.  Az ajánlat üzembe helyezhető a kiválasztott előfizetések (az ajánlatinformációs lépésekben), annak ellenőrzéséhez, hogy megfelel-e az összes követelménynek.  Miután ellenőrizte az ajánlatot, válassza az **Élő ugrás** lehetőséget, hogy az ajánlat a következő lépésre léphessen. |
| Microsoft-ellenőrzés                | 7 - 14 nap | A Microsoft holisztikusan áttekinti az Azure-alkalmazást, és e-maileket, ha problémákat fedezfel.  A lépés hossza az alkalmazás összetettségétől, a feltárt problémáktól és attól függ, hogy ön milyen gyorsan válaszol rájuk.  |
| Élő                           | < 1 nap | Az ajánlat kiadásra kerül, replikálódik a megadott régiókba, és elérhetővé válik a nyilvánosság számára. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

A közzétételi folyamat figyelheti az **állapot** lapon az ajánlat a Cloud Partner Portal.

![Állapot lap egy Azure-alkalmazásajánlathoz](./media/offer-status-tab.png)

A közzétételi folyamat befejezése után az ajánlat a [Microsoft Azure Piactér alkalmazáskategóriájában](https://azuremarketplace.microsoft.com/marketplace/apps/)jelenik meg.

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](../../cloud-solution-providers.md) ban talál.

## <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

Az ajánlat közzétételi állapotának megjelenítése mellett az **Állapot** lap hibaüzeneteket és visszajelzéseket is jelenít meg minden olyan közzétételi lépésről, ahol probléma merül fel.  Ha a probléma kritikus, akkor a közzététel megszakad.  Ezután ki kell javítania a jelentett problémát,(ka)t, és újra közzé kell tennie az ajánlatot.  Mivel a **Microsoft felülvizsgálati** lépés az ajánlat és a hozzá tartozó technikai eszközök (különösen az Azure Resource Manager-sablon) átfogó áttekintését jelenti, a problémák általában lekéréses kérelem (PR) hivatkozásként jelennek meg.  A psz-ek megtekintésének és az azokra való válaszadásnak a magyarázata a [Véleményezési visszajelzések kezelése](./cpp-handling-review-feedback.md)című témakörben látható.


## <a name="next-steps"></a>További lépések

Ha hibákat észlelt egy vagy több közzétételi lépésben, ki kell javítania őket, és újra közzé kell tennie az ajánlatot.  Ha kritikus problémák merülnek fel a **Microsoft felülvizsgálati** lépés, a felülvizsgálati visszajelzést a Microsoft felülvizsgálati csapat Azure DevOps-tárházának elérésével kell [kezelnie.](./cpp-handling-review-feedback.md)

Miután egy Azure-alkalmazás sikeresen közzétett, [frissítheti a meglévő ajánlatot,](./cpp-update-existing-offer.md) hogy tükrözze a változó üzleti vagy műszaki követelményeknek. 
