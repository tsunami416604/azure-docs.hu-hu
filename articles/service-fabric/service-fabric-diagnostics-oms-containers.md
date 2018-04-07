---
title: Az Azure Service Fabric Naplóelemzési a tárolók figyelése |} Microsoft Docs
description: Log Analytics használata Azure Service Fabric-fürtök futó tárolók figyelése.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>A Naplóelemzési tárolók figyelése
 
Ez a cikk tároló figyelését a fürt beállításához szükséges lépéseket ismerteti. A további információkért lásd: [tárolók figyelése a Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Ez a részletes oktatóanyaga megtekintéséhez is követheti [figyelő Windows tárolók a Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>A felügyeleti megoldás tároló beállítása

> [!NOTE]
> A beállított Naplóelemzési a fürt számára, valamint rendelkezik a csomópontjára telepíti az OMS-ügynököt kell. Ha nem, kövesse a lépéseket [Naplóelemzési beállítása](service-fabric-diagnostics-oms-setup.md) és [adja hozzá az OMS-ügynököt a fürthöz](service-fabric-diagnostics-oms-agent.md) első.

1. Ha a fürt készen áll a Naplóelemzési és az OMS-ügynököt, a tároló üzembe helyezése. Várjon, amíg a tárolók telepíthető, mielőtt továbblép a következő lépéssel.

2. A Azure piactérről, keresse meg a *tároló figyelésére szolgáló megoldás* , majd kattintson a a **tároló figyelésére szolgáló megoldás** erőforrás, a figyelés és felügyelet alatt megjelenő kategóriát.

    ![Tárolómegoldások hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. A megoldás belül ugyanazon a munkaterületen a fürt már létrehozott létrehozása. Ezt a változtatást automatikusan elindítja az ügynököt, hogy a tároló docker adatgyűjtés indítása. Mintegy 15 percre leáll vagy tette meg kell jelennie a bejövő naplók és a statisztikák mentése könnyű megoldást.

## <a name="next-steps"></a>További lépések
* További információk a Service Fabric - tároló vezénylési [Service Fabric és a tárolók](service-fabric-containers-overview.md)
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* Log Analytics beállítása konfigurálása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) szabályok észlelésére és diagnosztika