---
title: Határozza meg a közzététel lehetőséget az Azure Marketplace-en |} Az Azure
description: Ez a cikk ismerteti a megfelelőségi feltételeket, és szeretné ismerni az alkalmazások közzététele az Azure piactér közzétételi követelmények partnerek.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/13/2018
ms.author: qianw211
ms.openlocfilehash: 29909e9fba209ee892bc5fd3b0c08026ee5250b0
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075481"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

Egyszer, [vonatkozó közzétételi lehetőség](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type), válassza ki, amely jelenleg az ajánlatot a rendszer készen áll. 

Az ajánlat közzétételi objektumot fog létrehozni, közzététele és kezelése a rendszer a [Cloud Partner Portalon](https://cloudpartner.azure.com). A *ajánlattípus* határozza meg az ajánlat struktúra, amely tartalmazza a metaadatok, összetevők és egyéb tartalmak bemutatására az ajánlatot a piactéren.

Mielőtt létrehozhatna egy ajánlatot, ki kell választania egy ajánlat típusát. Az ajánlat típusát a típusú megoldás, alkalmazás, vagy a szolgáltatási ajánlat, amely a közzétenni kívánt, valamint Microsoft-termékek és szolgáltatások igazítást felel meg. 

Egyetlen ajánlattípusra konfigurálható eltérően a Cloud Partner portálra ahhoz, hogy különböző közzétételi beállítások, a hívások műveletet, a kiépítés, vagy díjszabása. A közzétételi beállítások és az ajánlat típusú konfigurációs is igazítása az ajánlat jogosult-e és technikai követelményeket. 

Mindenképpen tekintse át a kirakat, illetve típus jogosultsági követelményeknek és a műszaki közzétételi követelmények az ajánlat létrehozása előtt.

## <a name="list-of-offer-types"></a>Az ajánlat típusok listája

Az Azure Marketplace-ajánlat típusok az alábbi táblázatban láthatók.

| **Csomag típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [**Virtual machines (Virtuális gépek)**](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | Virtuális berendezés az előfizetéshez társított az ügyfelek központi telepítéséhez használja a virtuális gép ajánlat típusát. |
| [**Megoldássablonok**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | A megoldás sablontípus-ajánlat akkor használja, ha a megoldáshoz szükséges további telepítési és konfigurációs automation túl egy egyszerű virtuális Gépet. |
| [**Felügyelt alkalmazások**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | Használja az Azure-alkalmazás: az ajánlat alkalmazástípus kezelése, ha az alábbi feltételek szükségesek: <br> <ul> <li> Vagy egy előfizetés-alapú megoldás üzembe helyezése egy virtuális gép vagy a teljes IaaS-alapú megoldás segítségével az ügyfelek számára. </li> <li>Ön vagy az ügyfél szükséges, hogy a megoldás egy partner kezeli-e. </li> <ul> |
| [**SaaS-alkalmazásokhoz**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | Az SaaS-alkalmazás ajánlattípus használatával engedélyezhető az ügyfelek a SaaS-alapú, technikai megoldás egy előfizetés vásárlása. |
| [**Tároló-ajánlatok**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | A tároló ajánlattípusra használja, amikor a megoldás üzembe egy Kubernetes-alapú Azure tárolószolgáltatás, Docker-tároló rendszerképét. |
| [**IoT a Microsoft Edge-modulok**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Az Azure IoT Edge-modulok IoT Edge által kezelt legkisebb számítási egységek, és a Microsoft-szolgáltatások (például az Azure Stream Analytics), 3. fél szolgáltatások vagy a saját megoldásspecifikus kódját is tartalmazhat. |
| [**Tanácsadási szolgáltatások**](https://docs.microsoft.com/azure/marketplace/consulting-services) | Az Azure Marketplace tanácsadási szolgáltatások segítségével való csatlakozáshoz ügyfeleket támogatja, és bővítheti használata során az Azure-szolgáltatásokkal. |
| [**Az AppSource-ajánlatok**](https://docs.microsoft.com/azure/marketplace/appsource-offer-publishing-guide) | Az appsource-ban kirakat tartalmaz, amely létrehozása vagy bővítése a Dynamics 365, az Office 365, a Power bi-ban és a Power Apps ajánlatok. |

Végül tekintse meg [ **Azure AD-követelményeket** ](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad) a beállítások listázása és típusok egyszeri bejelentkezés követelményekről további információt kínálnak.

## <a name="next-steps"></a>További lépések

*   Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
*   Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.
*   Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com) hozhat létre és konfigurálja az ajánlatát.
