---
title: Power BI alkalmazás ajánlatának technikai információi | Azure piactér
description: A Microsoft AppSource Marketplace Power BI alkalmazásra vonatkozó technikai információ mezőinek konfigurálása.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b012ac012c7de3c6428ecbe2d2b922a2a9aa0523
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141776"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI alkalmazások technikai információi lap

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a Power BI alkalmazás-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Power bi alkalmazás-létrehozás áttekintése című](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) témakör utasításait.

Az **új ajánlat** oldalon a **technikai információk** lapon megadhatja a Power bi telepítőcsomag URL-címét és az új ajánlat érvényesítéséhez szükséges egyéb információkat.  A kezdeti kiadás esetében az összes Power BI alkalmazás ingyenes, és letölthető a AppSource webhelyről. Ezért nem határozhatja meg az ajánlat típusához tartozó készletezési egységeket (SKU-ket).

![A technikai információ lap](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Technikai információk mezői 

A **technikai információk** lapon végezze el az alábbi táblázatban leírt mezőket. A mező feliratának végén található csillag (*) érték azt jelenti, hogy a mező megadása kötelező.

|        Mező          |  Leírás                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Telepítő URL-címe\***     | Power BI létrehozza ezt az URL-címet az alkalmazás közzétételekor és az éles környezetbe való előléptetése során.  További információ: [alkalmazások közzététele irányítópultokkal és jelentésekkel Power BIban](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Érvényesítési utasítások**  |  Ha szeretné, adja meg a Microsoft ellenőrzési csapatának az alkalmazás konfigurálásához, összekapcsolásához és teszteléséhez szükséges utasításokat (legfeljebb 3 000 karakter). Az adatok összekapcsolásának tesztelésére szolgáló jellemző konfigurációs beállításokat, fiókokat, paramétereket és egyéb információkat is megadhat. Ezek az információk csak az ellenőrzési csapat számára láthatók, és csak ellenőrzési célokra használhatók.  |
| **Az alkalmazás Power BI tartalomkezelő csomagként lett létrehozva?** | Ez a mező jelenleg csak belsőleg használatos. Hagyja meg a **nem**érték alapértelmezett beállítását. Ha az **Igen**értékre módosítja a beállítást, leállíthatja a közzétételi folyamatot.  |  
|  |  |


## <a name="next-steps"></a>További lépések

A [kirakat részletei](./cpp-storefront-details-tab.md) lapon adja meg az alkalmazás marketing-és jogi információit.

