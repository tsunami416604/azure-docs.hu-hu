---
title: Mi az Azure Service Health?
description: Személyre szabott információk arról, hogy az Azure-alkalmazások milyen hatással vannak a jelenlegi és jövőbeli Azure-szolgáltatásokkal kapcsolatos problémákra és karbantartásra.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87499772"
---
# <a name="what-is-azure-service-health"></a>Mi az Azure Service Health?

Az Azure számos lehetőséget kínál a felhőalapú erőforrások állapotának folyamatos tájékoztatására. Ezek az információk olyan aktuális és jövőbeli problémákat foglalnak magukban, mint például a szolgáltatás befolyásoló eseményei, a tervezett karbantartás és a rendelkezésre állást esetlegesen befolyásoló egyéb változások.

A Azure Service Health három külön kisebb szolgáltatás kombinációja.

Az Azure [status](azure-status-overview.md) szolgáltatás az Azure-ban az Azure Status (állapot) **[lapján](https://status.azure.com)** értesíti a szolgáltatások leállásáról. A lap az összes Azure-régióban az összes Azure-szolgáltatás állapotának globális nézetét jeleníti meg. Az állapot lap jól használható az incidensek széles körű hatásával kapcsolatban, de határozottan azt javasoljuk, hogy az Azure-beli felhasználók az Azure szolgáltatás állapotát használják az Azure-incidensek és-karbantartás tekintetében.

A [szolgáltatás állapota](service-health-overview.md) személyre szabott áttekintést nyújt az Ön által használt Azure-szolgáltatások és-régiók állapotáról. Ez a legjobb hely az kimaradások, a tervezett karbantartási tevékenységek és más egészségügyi tanácsadók felé irányuló kommunikációra, mivel a hitelesített Service Health felhasználói élmény tudja, hogy mely szolgáltatásokat és erőforrásokat használja jelenleg. A Service Health használatának legjobb módja az, ha Service Health-riasztásokat állít be, amelyek a választott kommunikációs csatornákon értesítik Önt az Ön által használt Azure-szolgáltatások és -régiók működését befolyásoló hibákról, tervezett karbantartásokról és egyéb változásokról.

A [Resource Health](resource-health-overview.md) információt nyújt az egyes Felhőbeli erőforrások állapotáról, például egy adott virtuálisgép-példányról. Az Azure Monitor használatával a riasztásokat úgy is konfigurálhatja, hogy az Ön által használt felhőerőforrások rendelkezésre állásával kapcsolatos változásokról küldjön értesítést. Resource Health a Azure Monitor Notifications szolgáltatással együtt az erőforrások percek alatt elérhetővé válik, és gyorsan megbecsülheti, hogy a probléma oka az, hogy az Ön oldalán vagy egy Azure-platform eseményeivel kapcsolatos problémáról van-e szó.

Ezek a szolgáltatások együttesen átfogó képet nyújtanak az Azure állapotáról, az Ön igényeinek legjobban megfelelő részletességgel.

**Az Azure állapotjelző oldal, az Azure Service Health és az Azure Resource Health áttekintése**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]