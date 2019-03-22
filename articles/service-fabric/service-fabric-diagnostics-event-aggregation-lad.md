---
title: Az Azure Service Fabric esemény összesítés a Linuxos Azure Diagnostics használatával |} A Microsoft Docs
description: További tudnivalók összesítése és események gyűjtése a monitorozást és diagnosztikát az Azure Service Fabric-fürtök LAD segítségével.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: ca6be2b08c9a38813b2c98eef7e5696bd61fd074
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893033"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Események összesítése és -gyűjteményt Linuxos Azure Diagnostics használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Egy Azure Service Fabric-fürtön futtatja, esetén érdemes egy központi helyen összes csomópontja a naplók gyűjtését. A naplók kellene egy központi helyen segítségével elemezheti és a fürtben, vagy az alkalmazások és szolgáltatások a fürtben futó problémák elhárítása.

Fel-és naplók gyűjtése az egyik módja, hogy Linuxos Azure Diagnostics (LAD) bővítménye, amely feltölti a naplókat az Azure Storage, és a naplók elküldése az Azure Application Insights és az Event Hubs lehetősége is van. Használhatja egy külső folyamatba, olvassa az eseményeket a storage-ból, és helyezze el őket olyan elemzési felhőplatform-megoldás, mint például [naplózza az Azure Monitor](../log-analytics/log-analytics-service-fabric.md) vagy egy másik log-elemzési megoldás.

## <a name="log-and-event-sources"></a>Napló- és esemény-források

### <a name="service-fabric-platform-events"></a>A Service Fabric platformot események
A Service Fabric bocsát ki a több-az-beépített naplók keresztül [lttng érhető el](https://lttng.org), beleértve a működési események vagy futtatókörnyezeti események. Ezek a naplók a helyre, amely meghatározza a fürt Resource Manager-sablonban vannak tárolva. Vagy állítsa be a tárfiók részleteit, keresse meg a címke **AzureTableWinFabETWQueryable** , és keressen **StoreConnectionString**.

### <a name="application-events"></a>Alkalmazásesemények
 Amikor szándékkal, a szoftver az alkalmazások és szolgáltatások kódból Ön által megadott kibocsátott események. Használhat bármilyen naplózási megoldás, amely írja a szöveges naplófájlokat – például lttng érhető el. További információ a nyomkövetési az alkalmazás az lttng érhető el dokumentációjában talál.

[A helyi gép fejlesztési telepítőjének szolgáltatások monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>A diagnosztikai bővítmény telepítése
Naplók gyűjtése az első lépés, hogy a diagnosztikai bővítményt a virtuális gépek, a Service Fabric-fürt minden egyes telepíti. A diagnosztikai bővítmény naplókat gyűjti össze az egyes virtuális Gépeken, és feltölti őket az Ön által megadott tárfiókot. 

Állítsa be a diagnosztikai bővítményt a virtuális gépek a fürt szeretné telepíteni a fürt létrehozása során, **diagnosztikai** való **a**. Miután a fürt létrehozása a portálon, hogy a megfelelő módosításokat a Resource Manager-sablon használatával Ez a beállítás nem módosítható.

Ez konfigurálja a LAD ügynök megadott naplófájlok figyeléséhez. Minden alkalommal, amikor új sort a rendszer hozzáfűzi a fájl, egy megadott tároló (tábla) küldött syslog-bejegyzést hoz létre.


## <a name="next-steps"></a>További lépések

1. Milyen eseményeket kapcsolatos hibák elhárítása során meg kell vizsgálni részletes ismertetése: [lttng érhető el dokumentáció](https://lttng.org/docs) és [használatával LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [A Log Analytics-ügynök telepítése](service-fabric-diagnostics-event-analysis-oms.md) annak érdekében, metrikákat, a fürtön üzembe helyezett tárolók figyelése és a naplók megjelenítése 
