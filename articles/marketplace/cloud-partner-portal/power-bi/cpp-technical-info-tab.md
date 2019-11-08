---
title: Power BI alkalmazás ajánlatának technikai információi | Azure piactér
description: A Microsoft AppSource Marketplace Power BI alkalmazásra vonatkozó technikai információ mezőinek konfigurálása.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 02c98e38f26fff73d02dc4c2b838ad3428cd2996
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819028"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI alkalmazások technikai információi lap

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

