---
title: Közzétételi útmutató ajánlat típusa szerint – Microsoft kereskedelmi piactér
description: Ez a cikk a Microsoft kereskedelmi piactéren elérhető ajánlati típusokat ismerteti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/14/2020
ms.openlocfilehash: 72acd5c3c5fbab08b2e97421689b4f562b2ae2e8
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119718"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

Ez a cikk a kereskedelmi piactéren elérhető ajánlati típusokat ismerteti. Az *ajánlat típusa* határozza meg az ajánlat struktúráját, amely tartalmazza azokat a metaadatokat, összetevőket és egyéb tartalmakat, amelyek az ajánlatnak a piactéren való bemutatására szolgálnak.

Az ajánlat létrehozása előtt és a [közzétételi lehetőség](determine-your-listing-type.md)kiválasztása után ki kell választania az ajánlat bemutatására használni kívánt ajánlat típusát is. Az ajánlat típusa megegyezik a közzétenni kívánt megoldás, alkalmazás vagy szolgáltatási ajánlat típusával, valamint a Microsoft-termékekkel és-szolgáltatásokkal való összehangolásával.

Egyetlen ajánlat típusa konfigurálható különböző módon a különböző közzétételi beállítások, a műveletek, a kiépítés vagy a díjszabás lehetővé tételéhez. A közzétételi lehetőség és az ajánlat típusának konfigurációja is igazodik az ajánlat támogathatóságához és a technikai követelményekhez.

Győződjön meg arról, hogy az ajánlat létrehozása előtt tekintse át a kirakati és az ajánlat típusának támogathatósági követelményeit, valamint a technikai közzétételi követelményeket.

## <a name="list-of-offer-types"></a>Ajánlati típusok listája

Az Azure Marketplace-ajánlatok típusai az alábbi táblázatban találhatók.

| **Ajánlat típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [**Virtuális gépek**](./marketplace-virtual-machines.md) | Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. |
| [**Megoldássablonok**](./marketplace-solution-templates.md) | Használja a megoldás sablont (más néven Azure-alkalmazás), ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen kívül. A megoldási sablonok számos különböző Azure-erőforrást alkalmazhatnak, többek között a virtuális gépeket is beleértve.  |
| [**Felügyelt alkalmazások**](./marketplace-managed-apps.md) | Használja az Azure-alkalmazást: felügyelt alkalmazás-ajánlat típusa, ha a következő feltételek szükségesek: <br> <ul> <li> Az ügyfél előfizetésen alapuló megoldását egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával helyezheti üzembe. </li> <li>Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje. </li> <ul> |
| [**SaaS-alkalmazás**](./partner-center-portal/create-new-saas-offer.md) | A SaaS app Offer típus használatával engedélyezheti az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldását. |
| [**Tároló ajánlatok**](./marketplace-containers.md) | Ha a megoldás egy, a Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép, akkor használja a Container ajánlat típusát. |
| [**Azure IoT Edge modulok**](./iot-edge-module.md) | Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| [**Tanácsadói szolgáltatások**](./consulting-services.md) | A tanácsadási szolgáltatások segítségével az ügyfeleket összekapcsolhatja a szolgáltatásokkal az Azure, a Dynamics 365 vagy a Power Suite szolgáltatások használatának támogatásához és bővítéséhez.|
| [**Office 365, Dynamics 365 és Power BI**](./appsource-offer-publishing-guide.md) | Közzéteheti a Dynamics 365, az Office 365, a Power BI és a Power apps szolgáltatásra épülő vagy kiterjeszthető AppSource ajánlatokat.|
| [**Integrált megoldások**](./integrated-solutions-for-publishers.md) | Olyan integrált, iparághoz igazított megoldásokat tehet közzé, amelyek egyetlen ajánlatként ötvözik a technológiát és a szolgáltatásokat.|

Az egyszeri bejelentkezésre vonatkozó követelményekről és az ajánlatok típusairól az [**Azure ad-követelmények**](./enable-appsource-marketplace-using-azure-ad.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>Következő lépések

- Tekintse át az ajánlat típusának (a következő szakaszoknak) a megfelelő cikkben szereplő támogathatósági követelményeket az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez.
- Tekintse át a közzétételi mintákat a kirakati példákkal, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
