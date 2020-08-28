---
title: Rövid útmutató – az Azure Spring Cloud apps naplóival, metrikákkal és nyomkövetéssel való monitorozása
description: A log streaming, a log Analytics, a metrikák és a nyomkövetés használatával figyelheti a Piggymetrics-minta alkalmazásokat az Azure Spring Cloud-on.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9f0ba36abac045d1deb6b4179fbfb6aa32d4dc9a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009760"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Gyors útmutató: Azure Spring Cloud-alkalmazások figyelése naplókkal, metrikákkal és nyomkövetéssel

Az Azure Spring Cloud beépített monitorozási funkciójával könnyedén képes az összetett problémák hibakeresésére és figyelésére. Az Azure Spring Cloud egyesíti a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insightsával](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Ez az integráció hatékony naplókat, metrikákat és elosztott nyomkövetési képességet biztosít a Azure Portalból. Bemutatjuk, hogy miként használhatók a log streaming, a Log Analytics, a metrikák és az elosztott nyomkövetés a telepített PiggyMetrics-alkalmazásokkal.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az előző lépéseket: 

* [Azure Spring Cloud-példány kiépítése](spring-cloud-quickstart-provision-service-instance.md)
* [A konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md)
* [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Naplók

Az Azure Spring Cloud naplóit kétféleképpen tekintheti meg: valós idejű naplók **naplózása** alkalmazás-példányon vagy **log Analytics** a speciális lekérdezési képességgel rendelkező összesített naplókhoz.

### <a name="log-streaming"></a>Naplózási adatfolyam

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

A log streaming az Azure CLI-ben a következő paranccsal használható.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Ekkor a következőhöz hasonló naplók láthatók:

[![Folyamatos naplózás az Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) -ből](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> `az spring-cloud app logs -h`A használatával több paramétert és a log stream funkcióit is megismerheti.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Naplók beszerzése Azure Toolkit for IntelliJ használatával:

1. Válassza az **Azure Explorer**, majd a **Spring Cloud**elemet.

1. Kattintson a jobb gombbal a futó alkalmazásra.

1. Válassza a **folyamatos átviteli naplók** lehetőséget a legördülő listából.

   ![Adatfolyam-naplók kiválasztása](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Válassza a **példány**lehetőséget.

   ![Példány kiválasztása](media/spring-cloud-intellij-howto/select-instance.png)
    
1. A folyamatos átviteli napló a kimeneti ablakban jelenik meg.

   ![Adatfolyam-napló kimenete](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Ugrás a **szolgáltatásra | Áttekintés** oldalon, majd a **figyelés** szakaszban válassza a **naplók** lehetőséget. Kattintson a **Futtatás** elemre az Azure Spring Cloud-beli lekérdezések egyikén. 

   [![Naplók Analytics-bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Ezután megjelennek a szűrt naplók. A lekérdezések írásához további útmutatást az [Azure log Analytics dokumentációjában](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) talál.

   [![Naplók Analytics-lekérdezés ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Mérőszámok

1. Ugrás a **szolgáltatásra | Áttekintés** lapon válassza a **metrikák** lehetőséget a **figyelés** szakaszban. Adja hozzá az első mérőszámot a metrika lehetőség kiválasztásával `system.cpu.usage` , és **Metric** `Avg` a teljes CPU-használat idővonalának megjelenítéséhez. **Aggregation**

   [![Metrikák bejegyzése ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. A fenti eszköztár **szűrő hozzáadása** gombjára kattintva `App=Gateway` megtekintheti a CPU-használatot csak az **átjáró** alkalmazás esetében.

   [![Szűrő használata mérőszámokban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Zárja be a fent létrehozott szűrőt, kattintson a **felosztás alkalmazása** elemre, és válassza `App` az **értékek** lehetőséget a különböző alkalmazások CPU-használatának megjelenítéséhez.

   [![Felosztás alkalmazása mérőszámokban ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Elosztott nyomkövetés

1. Ugrás a **szolgáltatásra | Áttekintés** lapon válassza az **elosztott nyomkövetés** lehetőséget a **figyelés** szakaszban. Ezután kattintson a jobb oldalon található **alkalmazás-hozzárendelés megtekintése** fülre.

   [![Elosztott nyomkövetési bejegyzés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Mostantól megtekintheti a Piggymetrics-alkalmazások közötti hívások állapotát. 

   [![Elosztott nyomkövetés áttekintése ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Az **átjáró** és a **fiók** közötti hivatkozásra kattintva további részleteket tekinthet meg, például a leglassabb hívásokat http-módszerekkel.

   [![Elosztott nyomkövetés ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Végül kattintson a **teljesítmény vizsgálata** lehetőségre a nagyobb teljesítményű beépített teljesítmény-elemzések megismeréséhez.

   [![Elosztott nyomkövetési teljesítmény ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot a Cloud Shellban:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Az előző lépésekben az erőforráscsoport alapértelmezett nevét is megadhatja. Az alapértelmezett beállítás törléséhez futtassa a következő parancsot a Cloud Shellban:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Spring Cloud szolgáltatáson kívüli figyelési képességről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Diagnosztikai szolgáltatások](diagnostic-services.md) 
>  [Elosztott nyomkövetés](spring-cloud-tutorial-distributed-tracing.md) 
>  [Adatfolyam-naplók valós időben](spring-cloud-howto-log-streaming.md)
