---
title: Azure SaaS-alkalmazás ajánlat létrehozása |} Az Azure Marketplace-en
description: Hogyan lehet egy SaaS-alkalmazás ajánlat létrehozása az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 646e9bf844477b3d8e1c4c42fb5956e015805433
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833502"
---
# <a name="create-a-new-saas-application-offer"></a>Hozzon létre egy új SaaS-alkalmazásra vonatkozó ajánlat

Ez a cikk azt ismerteti, hogyan hozhat létre, és a egy SaaS-application (alkalmazás) ajánlat bejegyzés közzététele az Azure Marketplace-en.

> [!IMPORTANT] 
> SaaS-ajánlat funkció az áttelepítés alatt a [Microsoft Partner Centeren](https://partner.microsoft.com/dashboard/directory).  Minden új gyártó kell használnia a Partner Center új SaaS-ajánlatok létrehozására és kezelésére a meglévő ajánlatok.  Az SaaS-ajánlatok kiadók migrálása folyamatban van batchwise a Cloud Partner portálra, a Partner Center.  A Cloud Partner portálra azt jelzik, ha meghatározott meglévő ajánlatok áttelepítette állapotüzeneteket jelenít meg.


## <a name="offer-process"></a>Az ajánlat folyamat

A következő ábrán egy SaaS-alkalmazás ajánlat létrehozásának folyamatát.

![Egy SaaS-ajánlat létrehozásának folyamata](./media/saas-offer-process-overview.png)

## <a name="offer-components"></a>Az ajánlat összetevők

Az SaaS-alkalmazás az ajánlat az alábbi táblázatban ismertetett öt szakaszokban áll:

|  **Eszközcsoport**   |  **Leírás**  |
|  ---------------   |  ---------------  |
|    Ajánlat beállításai  |  Segítségével konfigurálhatja a SaaS-alkalmazás egyedi azonosítóval.                 |
|  Technikai információ    |  Konfigurálhatja a Szolgáltatottszoftver-megoldás típusát, és adja meg a kapcsolat adatait az alkalmazás.                |
|  Csatorna adatai      |   Adja meg a csatorna adatokat például a GTM-anyagok és a névjegyeket.                |
|  Kipróbálás        |   Egy szolgáltatás, amely lehetővé teszi a vevők definiálása az opcionális szakasz tesztelje ajánlatát, mielőtt a vásárolja meg.                |
|  Storefront részletei       | Marketing, jogi és elégtelen felügyeleti eszközök és előírásokat tartalmazza.  <ul><li> Marketing eszközök tartalmazzák az ajánlat nevét, leírását és emblémák</li> <li> Jogi eszközök közé tartozik egy adatvédelmi szabályzatát, használati feltételeit és egyéb jogi dokumentáció</li>  <li> Elégtelen felügyeleti házirend lehetővé teszi, hogy adja meg, hogyan kezelje az Azure Marketplace-en a végfelhasználói portálon vezet.</li> </ul> |
| Kapcsolatok            | Támogatási kapcsolattartó és házirend információkat tartalmaz |

## <a name="new-offer-form"></a>Új ajánlat űrlap

Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/), majd válassza ki **+ új ajánlat** a bal oldali menüsáv a. Válassza ki az új ajánlat menü **SaaS-alkalmazások** jeleníti meg az új ajánlat űrlapot, és indítsa el az eszközöket egy új SaaS-alkalmazás ajánlat meghatározása.

![Az SaaS-alkalmazásokhoz új ajánlat menü](./media/azure-new-saas-offer.png)

## <a name="next-steps"></a>További lépések

Az ajánlat SaaS típusú az új ajánlat oldal nyújt lapokat és az űrlap mezőit, amelyek segítségével hozzon létre egy új ajánlatot. Az alábbi cikkekben egyes használatát ismerteti a lapon adható meg az eszközintelligencia-csoportok és az új ajánlat segítő szolgáltatás.

- [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
- [Technikai információs lap](./cpp-technical-info-tab.md)
- [Csatornainformációs lap](./cpp-channel-info-tab.md)
- [Tesztverzió lap](./cpp-testdrive-tab.md)
- [Áruház részletei lap](./cpp-storefront-tab.md)
- [Névjegyek lap](./cpp-contacts-tab.md)
