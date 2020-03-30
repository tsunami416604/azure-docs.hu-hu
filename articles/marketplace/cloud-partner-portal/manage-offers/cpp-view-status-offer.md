---
title: Marketplace-ajánlatok állapotának megtekintése | Azure Piactér
description: Az ajánlatok állapotának megtekintése az Azure- és AppSource-piactereken a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275968"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Az Azure Marketplace és az AppSource-ajánlatok közzétételi állapotának megtekintése

Miután létrehozott egy ajánlatot, és különösen a közzétételi folyamat során, megtekintheti az ajánlat állapotát a Cloud Partner Portal.After you create a offer, and especially during the publishing process, you can view the status of your offer in the Cloud Partner Portal.  A teljes közzétételi állapot a portál [**Minden ajánlat**](../portal-tour/cpp-all-offers-page.md) és [**jóváhagyás**](../portal-tour/cpp-approvals-page.md) lapján érhető el.  Az egyes ajánlatainkban meg kell jelennie az alábbi állapotjelzők egyikének.  

|            status              |   Leírás                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Az ajánlat létrejött, de a közzétételi folyamat még nem kezdődött el.            |
| **Közzététel folyamatban**        | Az ajánlat a közzétételi folyamat lépésein halad keresztül.   |
| **A közzététel nem sikerült**             | A Microsoft ellenőrzése vagy ellenőrzése során egy kritikus problémát fedeztek fel. |
| **Közzététel megszakítva**           | A közzétevő megszakította az ajánlat közzétételi folyamatát.  Ez az állapot nem szünteti meg a piactéren meglévő ajánlatot. | 
| **Kijelentkezésre vára** | Az ajánlatot a Microsoft felülvizsgálta, és most a közzétevő végső ellenőrzésére vár. |
| **Listáról**                   | Egy korábban közzétett ajánlatot eltávolítottak a piactéren.      | 
|  |  |


## <a name="publishing-status-details"></a>Közzétételi állapot részletei 

További részletek az ajánlat állapotáról, ahogy a közzétételi folyamaton keresztül halad, az **Új ajánlat** lap **Állapot** lapján találhatók.  Ez a lap az adott ajánlattípus összes közzétételi lépését sorolja fel.  *Vegye figyelembe, hogy a szám és a konkrét lépések gyakran eltérőek az ajánlattípusok között.*  Ez a lap a Microsoft ellenőrzési és ellenőrzési lépései által felvetett, még lezáratlan problémákat is jelzi, amelyek gyakran a közzétételi folyamat folytatása előtt a közzétevő általvégrehajtott műveleteket igényelnek.  Az alábbi képen például egy új virtuálisgép-ajánlat **Állapot** lapja látható. 

![Állapot lap a virtuálisgép-ajánlathoz](./media/vm-offer-pub-steps1.png)

A következő példa **állapot** lap egy tanácsadó szolgáltatás, amely egy jelentett hiba az érdeklődőkezelési beállításokat.  Mivel az érdeklődőkezelés szükséges a tanácsadási szolgáltatásokhoz, ezt a hibát a közzététel folytatása előtt ki kell javítani.

![Állapot lap a hibát megjelenítő tanácsadási szolgáltatáshoz](./media/consulting-service-error.png)

Egy Azure-alkalmazás végső példa állapota egy kritikus Microsoft-felülvizsgálati problémát jelenít meg.  Egy gyorshivatkozást tartalmaz az Azure DevOps-elemhez, amely részletes információkat tartalmaz az ellenőrzéssel kapcsolatos problémáról.  További információ: [Publish Azure application offer](cpp-publish-offer.md).

![Az Azure-alkalmazás Állapot lapja az ellenőrzési problémával](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>További lépések

A fennálló problémák kijavításához vagy az ajánlatbeállítások frissítéséhez frissítenie kell [egy ajánlatot.](./cpp-update-offer.md) 
