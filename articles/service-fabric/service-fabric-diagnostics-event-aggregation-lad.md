---
title: "Az Azure Service Fabric esemény összesítési rendelkező Linux Azure Diagnostics |} Microsoft Docs"
description: "További tudnivalók összesítésére és események gyűjtése LAD figyelési és diagnosztika Azure Service Fabric-fürt segítségével."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b70780b2e1d169aced3412f6fe6d13ad4dab82be
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Esemény összevonásának és a gyűjtemény Linux Azure Diagnostics használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Amikor egy Azure Service Fabric-fürtöt használ, célszerű gyűjteni a egy központi helyen található minden csomópontot. A naplók rendelkezik egy központi helyen segítségével elemezheti és a fürt, vagy a futó alkalmazások és szolgáltatások az adott fürtben található a problémák elhárításához.

Egy feltöltése és naplógyűjtéshez módja Linux Azure Diagnostics (LAD) bővítménye, amely Azure Storage naplók feltöltését, és naplókat küld Azure Application Insights vagy az Event Hubs lehetősége is van. Kiolvassa az eseményeket a tárolóból, és helyezze el őket egy analysis platform termék, például a külső folyamat is használhatja [OMS Naplóelemzési](../log-analytics/log-analytics-service-fabric.md) vagy egy másik napló elemzési megoldás.

## <a name="log-and-event-sources"></a>Naplófájl és esemény források

### <a name="service-fabric-platform-events"></a>A Service Fabric platform események
A Service Fabric bocsát ki néhány out-of-az-box naplók keresztül [LTTng](http://lttng.org), beleértve a működési események vagy futásidejű események. Ezek a naplók tárolására használt, amely a fürt Resource Manager-sablon meghatározza a hely. Futtasson, vagy állítsa be a tárfiókadatok, keresse meg a címke **AzureTableWinFabETWQueryable** , és keressen **StoreConnectionString**.

### <a name="application-events"></a>Alkalmazás-események
 Ha a szoftver tagolása leírt módon, hogy az alkalmazások és szolgáltatások kódból kibocsátott események. Szöveges naplófájlok – például LTTng ír naplózási megoldások is használhatja. További információ a nyomkövetési az alkalmazás LTTng dokumentációjában találhat.

[Figyelése és diagnosztizálása szolgáltatásai a helyi számítógép fejlesztési telepítő](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>A diagnosztika bővítmény telepítése
Az első lépés a naplók gyűjtésére központi telepítése a diagnosztika bővítmény minden egyes a Service Fabric-fürt a virtuális gépen. A diagnosztika bővítmény naplókat gyűjt mindegyik virtuális gépre, és feltölti a megadott tárfiók. 

A diagnosztika bővítményt telepíteni a virtuális gépeket a fürt fürt létrehozásának részeként, állítsa be **diagnosztika** való **a**. A fürt létrehozása után a beállítás a portál használatával, hogy a megfelelő módosításokat a Resource Manager-sablon nem módosítható.

Ez konfigurálja a LAD ügynök megadott naplófájlok figyeléséhez. Amikor egy új sort a rendszer hozzáfűzi a fájl, létrehoz egy syslog bejegyzést, amely a megadott tároló (tábla) küldött.


## <a name="next-steps"></a>Következő lépések

1. Milyen eseményeket meg kell vizsgálni a problémák elhárítása során részletes ismertetése: [LTTng dokumentáció](http://lttng.org/docs) és [használatával LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
2. [Állítsa be az OMS-ügynököt](service-fabric-diagnostics-event-analysis-oms.md) gyűjtse össze a metrikák érdekében a fürt telepített tárolók figyelése és jelenítheti meg a naplókat 