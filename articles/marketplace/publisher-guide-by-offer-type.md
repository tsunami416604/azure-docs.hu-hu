---
title: Közzétételi útmutató ajánlat típusa szerint – Microsoft kereskedelmi piactér
description: Ez a cikk a Microsoft kereskedelmi piactéren elérhető ajánlati típusokat ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/29/2020
ms.openlocfilehash: 2dfdfaae2c07adc7d9193ffb00f8549bd4090a6e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578461"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

Ez a cikk a kereskedelmi piactéren elérhető ajánlati típusokat ismerteti. Az *ajánlat típusa* határozza meg az ajánlat struktúráját, amely tartalmazza a kereskedelmi piactéren bemutatott metaadatokat, összetevőket és egyéb tartalmakat.

A [közzétételi lehetőség](determine-your-listing-type.md)kiválasztása után ki kell választania az ajánlat típusát, mielőtt megkezdené az ajánlat létrehozását a partner Centerben. Az ajánlat típusa megegyezik a közzétenni kívánt megoldás, alkalmazás vagy szolgáltatási ajánlat típusával, valamint a Microsoft-termékekkel és-szolgáltatásokkal való összehangolásával.

A különböző közzétételi lehetőségek, listaelemek, kiépítés vagy díjszabás lehetővé tételéhez többféle módon is konfigurálhat egyetlen ajánlatot. A közzétételi lehetőség és az ajánlat típusának konfigurációja is igazodik az ajánlat támogathatóságához és a technikai követelményekhez.

Ügyeljen arra, hogy az ajánlat létrehozása előtt tekintse át az online áruház és az ajánlat típusra vonatkozó támogathatósági követelményeit, valamint a technikai közzétételi követelményeket.

## <a name="list-of-offer-types"></a>Ajánlati típusok listája

A következő táblázat a partner Center kereskedelmi Piactéri ajánlatának típusait mutatja be.

| **Ajánlat típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [Azure-alkalmazás: **felügyelt alkalmazás**](./marketplace-managed-apps.md) | Használja az Azure-alkalmazást: felügyelt alkalmazási ajánlat típusa, ha a következő feltételek szükségesek: <br> <ul> <li> Az ügyfél előfizetésen alapuló megoldását egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával helyezheti üzembe. </li> <li>Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje. </li> <ul> |
| [Azure-alkalmazás: **megoldás sablonja**](./marketplace-solution-templates.md) | Az Azure-alkalmazás: megoldás-sablon típusú ajánlat típusa akkor használható, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen kívül. A megoldási sablonok számos különböző Azure-erőforrást alkalmazhatnak, többek között a virtuális gépeket is beleértve.  |
  | [**Azure-tároló**](./marketplace-containers.md) | Használja az Azure Container ajánlat típusát, ha a megoldás egy Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép. |
| [**Azure-beli virtuális gép**](./marketplace-virtual-machines.md) | Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. |
| [**Tanácsadási szolgáltatás**](./consulting-services.md) | A tanácsadási szolgáltatások segítségével az ügyfeleket összekapcsolhatja a szolgáltatásokkal az Azure, a Dynamics 365 vagy a Power Suite szolgáltatások használatának támogatásához és bővítéséhez.|
| [**Dynamics 365**](./appsource-offer-publishing-guide.md) | Közzéteheti a Dynamics 365 Business Central, a Dynamics 365 Customer engagement, a Power apps és a Finance és az Operations alkalmazások szolgáltatásait felépítő AppSource-ajánlatokat.|
| [**IoT Edge modul**](./iot-edge-module.md) | Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| [**Power BI alkalmazás <br/> Microsoft 365**](./appsource-offer-publishing-guide.md) | Közzéteheti azokat a AppSource-ajánlatokat, amelyek Power BI és Microsoft 365 kiépítésére vagy kiterjesztésére épülnek.|
| [**Szolgáltatásként szolgáló szoftver**](plan-saas-offer.md) | A szolgáltatott szoftver (SaaS) ajánlat típusa lehetővé teszi az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldásait. |
| [**Integrált megoldások**](./integrated-solutions-for-publishers.md) | Olyan integrált, iparághoz igazított megoldásokat tehet közzé, amelyek egyetlen ajánlatként ötvözik a technológiát és a szolgáltatásokat.|

Az egyszeri bejelentkezésre vonatkozó követelményekről és az ajánlatok típusairól az [**Azure ad-követelmények**](./enable-appsource-marketplace-using-azure-ad.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

- Az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez tekintse át a megfelelő cikkben szereplő támogathatósági követelményeket.
- Tekintse át az egyes online áruházak közzétételi mintáit, példákat arra, hogy a megoldás hogyan mutasson az ajánlat típusára és a konfigurációra.
