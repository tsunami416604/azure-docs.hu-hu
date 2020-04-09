---
title: Power BI Alkalmazásajánlat létrehozása | Azure Piactér
description: Power BI-alkalmazásajánlat létrehozása a Microsoft AppSource Piactérhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: c396468bff0edef5cfceccd3d3c3394662111c10
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984844"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI App-ajánlat létrehozása

>[!Important]
>2020. április 13-tól megkezdjük a Power BI-alkalmazásajánlatok felügyeletének áthelyezését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse a [Power BI-alkalmazások létrehozásának áttekintése](https://aka.ms/AzureCreatePBIServiceApp) című témakör utasításait.

Ez a cikk azokat a lépéseket sorolja fel, amelyeket az [AppSource](https://appsource.microsoft.com)új Power BI Alkalmazásajánlatának létrehozásához kell végrehajtani. Minden ajánlat saját entitásként jelenik meg az AppSource-ban. Amikor új ajánlatot hoz létre a [Cloud Partner Portalon,](https://cloudpartner.azure.com/)négy eszközcsoportot kell megadnia az ajánlathoz.

Az eszközcsoportokat az alábbi táblázat ismerteti:

|   Eszközcsoport      | Leírás                                                                         |
| ----------------   | ----------------                                                                    |
| Ajánlat beállításai     | Az elsődleges azonosítók és az ajánlat neve.                                      |
| Technikai információk     | Az alkalmazás nak az ügyfél Power BI-munkaterületén történő telepítéséhez használt telepítő URL-címe. Az URL-cím létrehozásáról a [Power BI App dokumentációjában](https://go.microsoft.com/fwlink/?linkid=2028636)olvashat bővebben. |
| Kirakat részletei | Marketing, jogi és érdeklődőkezelési eszközöket tartalmaz. A marketingeszközök tartalmazzák az ajánlat leírását és az emblémákat. A jogi eszközök közé tartozik az adatvédelmi szabályzat, a használati feltételek és egyéb jogi dokumentumok. Az érdeklődőkezelési házirend segítségével megadhatja, hogyan kezelje az érdeklődőket az AppSource felhasználói portálról. |
| Kapcsolatok           | Támogatási kapcsolattartóés házirend-információkat tartalmaz.                                     |

## <a name="new-offer-form"></a>Új ajánlat űrlap

Miután bejelentkezett a Felhőpartneri portálra, válassza az **Új ajánlat lehetőséget** a bal oldali ablaktáblában. Ezután az Új ajánlat űrlap megjelenítéséhez és az új alkalmazásajánlat eszközeinek meghatározásának megkezdéséhez válassza a **Power BI-alkalmazások**lehetőséget.

![A Power BI ajánlat menüeleme](./media/new-offer-menu.png)

> [!NOTE] 
> Ha a **Power BI-alkalmazások** beállítás nem jelenik meg, vagy nincs engedélyezve, a fióknak nincs engedélye az ajánlattípus létrehozására. Ellenőrizze, hogy megfelelt-e az ajánlattípus összes [előfeltételének,](./cpp-prerequisites.md) beleértve a fejlesztői fiók regisztrálását is.


## <a name="next-steps"></a>További lépések

Az alábbi cikkek a Power BI-alkalmazásajánlat-típus **új ajánlatlapjának** lapjait ismertetik. Minden cikk ismerteti az új Power BI-alkalmazásajánlat eszközcsoportjait és támogató szolgáltatásait.

-  [Ajánlatbeállítási lap](./cpp-offer-settings-tab.md)
-  [Technikai információs lap](./cpp-technical-info-tab.md)
-  [Áruház részletei lap](./cpp-storefront-details-tab.md)
-  [Névjegyek lap](./cpp-contacts-tab.md)
