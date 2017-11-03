---
title: "Azure-készletben szolgáltatásokat kínáló |} Microsoft Docs"
description: "Felhő operátorként a felhasználók számára szolgáltatást is kínál."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7a54771d99f2719fcc345496b152a5d3acc02121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Az ajánlat Azure verem szolgáltatások áttekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

[A Microsoft Azure verem](azure-stack-poc.md) egy hibrid felhő platform, amely lehetővé teszi az adatközpontban a szolgáltatások nyújtásához. Szolgáltató szolgáltatást is kínál a bérlők számára. Egy üzleti vagy kormányzati ügynökség kínálhat alkalmazottai számára a helyszíni szolgáltatásokhoz. A szolgáltatások által biztosított tartalmazza, azonban nem csak:

- Platform (PaaS) szolgáltatás, például a alkalmazásszolgáltatások, Mobile Apps, az API Apps, API-függvények, SQL, MySQL.

Még akkor is kombinálhatja services integrálására és összetett megoldások a különböző felhasználók számára.

Ezeket a szolgáltatásokat telepíthet a felhasználók számára, létre kell hoznia [tervek ajánlatok és kvóták](azure-stack-plan-offer-quota-overview.md). A felhasználók majd előfizethetnek a ajánlatok szolgáltatások használatával.

## <a name="plan-your-service-offers"></a>A szolgáltatási ajánlatok tervezése

Amikor arra készül, az ajánlatokat, vegye figyelembe a következő szempontokat:

**Próbaajánlatokra**: új felhasználók, akik ezután frissítheti a további szolgáltatásokat vonzerőt próbaajánlatokra is használhatja. Próbaverziós ajánlat, hozzon létre egy kis [referenciaterv](azure-stack-plan-offer-quota-overview.md#base-plan) egy választható nagyobb bővítmény tervvel.

**Kapacitástervezés**: Előfordulhat, hogy a felhasználók nagy mennyiségű erőforrást grabbing és eltömődés a rendszer az összes felhasználó számára. Teljesítmény érdekében is [konfigurálása a tervek kvótával rendelkező](azure-stack-plan-offer-quota-overview.md#plans) cap-használatra.

**Meghatalmazott szolgáltatók**: biztosíthat mások képes ajánlatok létrehozni a környezetben. Például ha egy szolgáltató, akkor [delegálása](azure-stack-delegated-provider.md) ezt a lehetőséget a viszonteladók számára. Vagy, ha egy szervezet, delegálhatja más osztályok leányvállalatai.

## <a name="next-steps"></a>Következő lépések
[További tudnivalók az ajánlatokat, tervek, kvóták és előfizetések](azure-stack-plan-offer-quota-overview.md)

