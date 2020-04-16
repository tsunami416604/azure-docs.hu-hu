---
title: A Microsoft kereskedelmi piactér közzétételi útmutatója ajánlattípus szerint
description: Ez a cikk a Microsoft kereskedelmi piacterén elérhető ajánlattípusokat ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 6521f19ea566719fee39c238adda8e3a068e2c25
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419289"
---
# <a name="publishing-guide-by-offer-type"></a>Közzétételi útmutató ajánlattípusok szerint

Miután [úgy döntött, a közzétételi lehetőség,](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)készen áll, hogy válassza ki az ajánlat típusát, amely et használni fogja az ajánlat bemutatására.

 Az *ajánlattípus* határozza meg az ajánlat szerkezetét, amely tartalmazza a metaadatokat, összetevőket és az ajánlat a piactéren használt egyéb tartalmakat.

Az ajánlat létrehozása előtt ki kell választania egy ajánlattípust. Az ajánlattípusa megfelel a közzétenni kívánt megoldás, alkalmazás vagy szolgáltatásajánlat típusának, valamint a Microsoft-termékekhez és -szolgáltatásokhoz való igazításának.

Egyetlen ajánlattípus eltérő módon konfigurálható a különböző közzétételi beállítások, cselekvésre való felhívások, kiépítés vagy árképzés engedélyezéséhez. A közzétételi lehetőség és az ajánlattípus konfigurációja is igazodik az ajánlat jogosultsági és technikai követelményeihez.

Ügyeljen arra, hogy tekintse át a kirakat és az ajánlat típusának jogosultsági követelményeit és a technikai közzétételi követelményeket az ajánlat létrehozása előtt.

## <a name="list-of-offer-types"></a>Ajánlattípusok listája

Az Azure Marketplace-ajánlattípusok az alábbi táblázatban találhatók.

| **Ajánlat típusa**    | **Leírás**  |
| :------------------- | :-------------------|
| [**Virtuális gépek**](./marketplace-virtual-machines.md) | Használja a virtuális gép ajánlat típusát, amikor üzembe helyez egy virtuális berendezést az ügyfélhez társított előfizetéshez. |
| [**Megoldássablonok**](./marketplace-solution-templates.md) | Használja a megoldássablont (más néven Azure-alkalmazás) ajánlattípust, ha a megoldás egyetlen virtuális gépen túl további üzembe helyezést és konfigurációs automatizálást igényel. A megoldássablonok számos különböző Azure-erőforrást alkalmazhatnak, beleértve, de nem kizárólagosan a virtuális gépeket.  |
| [**Felügyelt alkalmazások**](./marketplace-managed-apps.md) | Használja az Azure-alkalmazást: felügyelt alkalmazásajánlat típusa, ha a következő feltételek szükségesek: <br> <ul> <li> Vagy egy előfizetés-alapú megoldást telepít az ügyfél számára egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával. </li> <li>Önnek vagy az ügyfélnek szüksége van arra, hogy a megoldást egy partner irányítsa. </li> <ul> |
| [**SaaS-alkalmazás**](./marketplace-saas-applications-technical-publishing-guide.md) | A SaaS-alkalmazásajánlat típusával lehetővé teszi az ügyfél számára, hogy előfizetésként megvásárolja a SaaS-alapú, technikai megoldást. |
| [**Konténer ajánlatok**](./marketplace-containers.md) | Használja a Container-ajánlat típusát, ha a megoldás egy Kubernetes-alapú Azure-tárolószolgáltatásként kiépített Docker-tárolórendszerkép. |
| [**Azure IoT Edge-modulok**](./iot-edge-module.md) | Az Azure IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Az Azure Stream Analytics), a harmadik féltől származó szolgáltatásokat vagy a saját megoldásspecifikus kódot. |
| [**Tanácsadói szolgáltatások**](./consulting-services.md) | A tanácsadási szolgáltatások segítségével összekapcsolhatja ügyfeleit az Azure, a Dynamics 365 vagy a Power Suite-szolgáltatások támogatásával és bővítésével.|
| [**Office 365, Dynamics 365 és Power BI**](./appsource-offer-publishing-guide.md) | Közzéteheti a Dynamics 365, az Office 365, a Power BI és a Power Apps alkalmazásra épülő AppSource-ajánlatokat.|
| [**Integrált megoldások**](./integrated-solutions-for-publishers.md) | Olyan integrált, iparághoz igazodó megoldásokat tehet közzé, amelyek egyetlen ajánlatként egyesítik a technológiát és a szolgáltatásokat.|

Az egyszeri bejelentkezési követelményekről a beállítási lehetőségek és az ajánlattípusok alapján az [**Azure AD-követelmények című témakörben**](./enable-appsource-marketplace-using-azure-ad.md)talál.

## <a name="next-steps"></a>További lépések

- Tekintse át a jogosultsági követelményeket az ajánlat típusának megfelelő cikkben (következő szakaszok) az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez.
- Tekintse át a közzétételi minták at kirakat példákat, hogyan a megoldás leképezése egy ajánlat típusát és konfigurációját.
