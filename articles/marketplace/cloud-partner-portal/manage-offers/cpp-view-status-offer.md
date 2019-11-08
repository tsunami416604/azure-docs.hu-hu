---
title: A Piactéri ajánlatok állapotának megtekintése | Azure piactér
description: Az Azure-és AppSource-piactéren elérhető ajánlatok állapotának megtekintése a Cloud Partner Portal használatával
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826707"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Az Azure Marketplace-és AppSource-ajánlatok közzétételi állapotának megtekintése

Miután létrehozott egy ajánlatot, és különösen a közzétételi folyamat során, megtekintheti az ajánlat állapotát a Cloud Partner Portalban.  A teljes közzététel állapota a portál [**összes ajánlat**](../portal-tour/cpp-all-offers-page.md) és [**jóváhagyás**](../portal-tour/cpp-approvals-page.md) lapján érhető el.  Az egyes ajánlatokhoz a következő állapotmutatók egyikét kell megjeleníteni.  

|            status              |   Leírás                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Az ajánlat létrejött, de a közzétételi folyamat nem indult el.            |
| **Közzététel folyamatban**        | Az ajánlat a közzétételi folyamat lépésein dolgozik.   |
| **Sikertelen közzététel**             | Az ellenőrzés során kritikus probléma merült fel, vagy a Microsoft felülvizsgálta azt. |
| **Közzététel megszakítva**           | A közzétevő megszakította az ajánlat-közzétételi folyamatot.  Ez az állapot nem sorolja fel egy meglévő ajánlatot a piactéren. | 
| **A közzétevő kijelentkezése vár** | Az ajánlatot a Microsoft felülvizsgálta, és most a közzétevő végső ellenőrzését várja. |
| **Törölték**                   | A piactéren korábban közzétett ajánlat el lett távolítva.      | 
|  |  |


## <a name="publishing-status-details"></a>Közzétételi állapot részletei 

Az ajánlat állapotával kapcsolatos további részletek az **új ajánlat** oldal **állapot** lapján találhatók a közzétételi folyamat során.  Ezen a lapon az adott ajánlat típusához tartozó összes közzétételi lépés szerepel.  *Vegye figyelembe, hogy a szám és a konkrét lépések gyakran eltérnek az ajánlatok típusai között.*  Ezen a lapon a Microsoft érvényesítési és felülvizsgálati lépéseiben felmerülő esetleges problémák is megadhatók, amelyek gyakran a közzétevő beavatkozását igénylik a közzétételi folyamat folytatásához.  Az alábbi képen például egy új virtuálisgép-ajánlat **állapotjelző** lapja látható. 

![A VM-ajánlat állapot lapja](./media/vm-offer-pub-steps1.png)

A tanácsadási szolgáltatás következő példájának **állapotjelző** lapja, amely egy jelentett hibát jelez a vezető felügyeleti beállításokban.  Mivel a tanácsadási szolgáltatásokhoz az ólom-felügyelet szükséges, ezt a hibát a közzététel folytatása előtt ki kell javítani.

![A tanácsadási szolgáltatás állapot lapja hibaüzenetet jelenít meg](./media/consulting-service-error.png)

Egy Azure-alkalmazás utolsó példájának állapota kritikus Microsoft-felülvizsgálati problémát mutat be.  Egy olyan, az Azure DevOps elemre mutató gyors hivatkozást tartalmaz, amely részletes információkat tartalmaz erről a felülvizsgálati hibáról.  További információ: Azure- [alkalmazás ajánlatának közzététele](cpp-publish-offer.md).

![A felülvizsgálati problémát mutató Azure-alkalmazás állapotjelző lapja](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>További lépések

A függőben lévő problémák kijavítani vagy az ajánlati beállítások frissítéséhez [frissítenie kell egy ajánlatot](./cpp-update-offer.md). 
