---
title: A közzétételi lehetőség meghatározása az Azure Marketplace-en | Azure piactér
description: Ez a cikk ismerteti a megfelelőségi feltételeket, és szeretné ismerni az alkalmazások közzététele az Azure piactér közzétételi követelmények partnerek.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/13/2018
ms.author: pabutler
ms.openlocfilehash: 4864e2c040ad6771ad187269de43d5978fc6324f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876058"
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
| [**Virtual machines (Virtuális gépek)** ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. |
| [**Megoldási sablonok**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | Akkor használja a megoldás sablonjának típusát, ha a megoldáshoz egy egyszerű virtuális gépen kívül további üzembe helyezési és konfigurálási automatizálásra van szükség. |
| [**Felügyelt alkalmazások**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | Használja az Azure-alkalmazást: felügyelt alkalmazás-ajánlat típusa, ha a következő feltételek szükségesek: <br> <ul> <li> Az ügyfél előfizetésen alapuló megoldását egy virtuális gép vagy egy teljes IaaS-alapú megoldás használatával helyezheti üzembe. </li> <li>Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje. </li> <ul> |
| [**SaaS-alkalmazások**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | A SaaS app Offer típus használatával engedélyezheti az ügyfél számára, hogy előfizetésként vásárolja meg SaaS-alapú, technikai megoldását. |
| [**Tároló ajánlatok**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | Ha a megoldás egy, a Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép, akkor használja a Container ajánlat típusát. |
| [**IoT Microsoft Edge-modulok**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Azure IoT Edge modulok a IoT Edge által kezelt legkisebb számítási egységek, és tartalmazhatnak Microsoft-szolgáltatásokat (például Azure Stream Analytics), harmadik féltől származó szolgáltatásokat vagy a saját megoldásokra vonatkozó kódokat. |
| [**Tanácsadási szolgáltatások**](https://docs.microsoft.com/azure/marketplace/consulting-services) | A tanácsadási szolgáltatások segítségével az ügyfeleket összekapcsolhatja a szolgáltatásokkal az Azure, a Dynamics 365 vagy a Power Suite szolgáltatások használatának támogatásához és bővítéséhez.|


Végül az egyszeri bejelentkezésre vonatkozó követelményekkel kapcsolatos további információkért tekintse meg az [**Azure ad-követelményeket**](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad) a beállítások és az ajánlati típusok listázásával.

## <a name="next-steps"></a>További lépések

*   Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
*   Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.

