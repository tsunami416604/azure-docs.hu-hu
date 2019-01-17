---
title: Marketplace-ajánlatok – Azure Marketplace-en állapotának megtekintése |} A Microsoft Docs
description: Az Azure és az AppSource-piactér használatával a Cloud Partner portálra szóló ajánlatok állapotának megtekintése
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
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
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: d7c2909e59643378e765fa51e2d261cbdc106822
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355612"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Az Azure Marketplace és AppSource ajánlatokat közzétételi állapotának megtekintése

Miután létrehozott egy ajánlatra, és különösen a közzétételi folyamat során, az ajánlat állapotát megtekintheti a Cloud Partner portálra.  A teljes közzétételi állapot érhető el a [ **összes kínál** ](../portal-tour/cpp-all-offers-page.md) és [ **jóváhagyások** ](../portal-tour/cpp-approvals-page.md) a portál oldalain.  Az alábbi Állapotjelzők egyik ajánlatok üzenet.  

|            status              |   Leírás                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Az ajánlat létre lett hozva, de a közzétételi folyamat még nem kezdődött meg.            |
| **A közzététel folyamatban**        | Az ajánlat a közzétételi folyamat lépéseit korongot működik.   |
| **Sikertelen közzététel**             | Egy kritikus problémát a Microsoft által felderített érvényesítési vagy felülvizsgálat alatt. |
| **Közzététele megszakítva**           | A közzétevő megszakította az ajánlat közzétételi folyamat.  Ebben az állapotban nem jegyzékből egy meglévő ajánlatot a piactéren. | 
| **Várakozás a publisher jelentkezzen ki** | Az ajánlat Microsoft felülvizsgálták, és most várja a végső ellenőrzés a közzétevő. |
| **Delisted**                   | A korábban közzétett ajánlatot a piactéren el lett távolítva.      | 
|  |  |


## <a name="publishing-status-details"></a>Közzétételi állapot részletei 

Részletesebb információkat tartalmaznak az ajánlat állapota a közzétételi folyamat előrehaladtával megtalálható a **állapot** lapján a **új ajánlat** lapot.  Ezen a lapon találhatók a közzétételi lépéseket az adott ajánlat típusát.  *Vegye figyelembe, hogy száma és különleges lépések gyakran eltérő típusú között.*  Ezen a lapon is azt jelzi, hogy bármely szálankénti függőben lévő problémák váltotta ki a Microsoft ellenőrzési és felülvizsgálati lépéseket, amelyek gyakran igényelnek a közzétevő művelet, a közzétételi folyamat folytatásához.  Ha például az alábbi képen a **állapot** fülre egy új virtuálisgép-ajánlat. 

![A Virtuálisgép-ajánlat állapota lap](./media/vm-offer-pub-steps1.png)

A következő példában **állapot** a tanácsadási szolgáltatást, és a jelentett hiba megjelenítése az érdeklődő beállítások lapján.  Mivel a szükséges tanácsadási szolgáltatások lead felügyelet, ez a hiba ki kell javítani, közzététel folytatása előtt.

![A tanácsadási szolgáltatást bemutató hiba állapota lap](./media/consulting-service-error.png)

Az Azure-alkalmazások utolsó példában állapotánál egy kritikus fontosságú Microsoft tekintse át a probléma.  Gyakori elérésű, tekintse át a problémával kapcsolatos részletes információkat tartalmaz a VSTS-elemre mutató hivatkozást tartalmaz.  További információkért lásd: [közzététele az Azure application ajánlat]().

![Tekintse át a probléma megjelenítő Azure-alkalmazáshoz lapja](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>További lépések

Javítsa ki a szálankénti függőben lévő problémák vagy az ajánlat beállításainak frissítése, kell [frissíteni az ajánlatot](./cpp-update-offer.md). 
