---
title: Microsoft kereskedelmi piactér – közzétételi útmutató ajánlat típusa szerint
description: Ez a cikk a Microsoft kereskedelmi piactéren elérhető ajánlati típusokat ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 6521f19ea566719fee39c238adda8e3a068e2c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419289"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

A [közzétételi lehetőség](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)kiválasztása után kiválaszthatja az ajánlat bemutatására használni kívánt ajánlat típusát.

 Az *ajánlat típusa* határozza meg az ajánlat struktúráját, amely tartalmazza azokat a metaadatokat, összetevőket és egyéb tartalmakat, amelyek az ajánlatnak a piactéren való bemutatására szolgálnak.

Ajánlat létrehozása előtt ki kell választania egy ajánlat típusát. Az ajánlat típusa megegyezik a közzétenni kívánt megoldás, alkalmazás vagy szolgáltatási ajánlat típusával, valamint a Microsoft-termékekkel és-szolgáltatásokkal való összehangolásával.

A különböző közzétételi lehetőségek, a műveletek meghívása, a kiépítés vagy a díjszabás lehetővé tételéhez egy adott típusú ajánlat típusát különbözőképpen lehet konfigurálni. A közzétételi lehetőség és az ajánlat típusának konfigurációja is igazodik az ajánlat támogathatóságához és a technikai követelményekhez.

Győződjön meg arról, hogy az ajánlat létrehozása előtt tekintse át a kirakati és az ajánlat típusának támogathatósági követelményeit, valamint a technikai közzétételi követelményeket.

## <a name="list-of-offer-types"></a>Ajánlati típusok listája

Az Azure Marketplace-ajánlatok típusai az alábbi táblázatban találhatók.

| **Ajánlat típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [**Virtuális gépek**](./marketplace-virtual-machines.md) | Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. |
| [**Megoldássablonok**](./marketplace-solution-templates.md) | Használja a megoldás sablont (más néven Azure-alkalmazás), ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen kívül. A megoldási sablonok számos különböző Azure-erőforrást alkalmazhatnak, többek között a virtuális gépeket is beleértve.  |
| [**Felügyelt alkalmazások**](./marketplace-managed-apps.md) | Használja az Azure-alkalmazást: felügyelt alkalmazás-ajánlat típusa, ha a következő feltételek szükségesek: <br> <ul> <li> Az ügyfél előfizetésen alapuló megoldását egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával helyezheti üzembe. </li> <li>Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje. </li> <ul> |
| [**SaaS-alkalmazás**](./marketplace-saas-applications-technical-publishing-guide.md) | A SaaS app Offer típus használatával engedélyezheti az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldását. |
| [**Tároló ajánlatok**](./marketplace-containers.md) | Ha a megoldás egy, a Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép, akkor használja a Container ajánlat típusát. |
| [**Azure IoT Edge modulok**](./iot-edge-module.md) | Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| [**Tanácsadói szolgáltatások**](./consulting-services.md) | A tanácsadási szolgáltatások segítségével az ügyfeleket összekapcsolhatja a szolgáltatásokkal az Azure, a Dynamics 365 vagy a Power Suite szolgáltatások használatának támogatásához és bővítéséhez.|
| [**Office 365, Dynamics 365 és Power BI**](./appsource-offer-publishing-guide.md) | Közzéteheti a Dynamics 365, az Office 365, a Power BI és a Power apps szolgáltatásra épülő vagy kiterjeszthető AppSource ajánlatokat.|
| [**Integrált megoldások**](./integrated-solutions-for-publishers.md) | Olyan integrált, iparághoz igazított megoldásokat tehet közzé, amelyek egyetlen ajánlatként ötvözik a technológiát és a szolgáltatásokat.|

Az egyszeri bejelentkezésre vonatkozó követelményekről és az ajánlatok típusairól az [**Azure ad-követelmények**](./enable-appsource-marketplace-using-azure-ad.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

- Tekintse át az ajánlat típusának (a következő szakaszoknak) a megfelelő cikkben szereplő támogathatósági követelményeket az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez.
- Tekintse át a közzétételi mintákat a kirakati példákkal, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
