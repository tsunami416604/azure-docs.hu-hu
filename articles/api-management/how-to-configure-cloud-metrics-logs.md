---
title: Felhőalapú metrikák és naplók konfigurálása az Azure API Management saját üzemeltetésű átjáróhoz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Felhőbeli metrikákat és naplókat az Azure API Management saját üzemeltetésű átjárója számára
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254528"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Felhőalapú metrikák és naplók konfigurálása az Azure API Management saját üzemeltetésű átjáróhoz

Ez a cikk részletesen ismerteti a felhőalapú metrikák és naplók konfigurálását a saját üzemeltetésű [átjáróhoz](./self-hosted-gateway-overview.md).

A saját üzemeltetésű átjárót egy API Management szolgáltatáshoz kell társítani, és a 443-es porton kimenő TCP/IP-kapcsolatot igényel az Azure-hoz. Az átjáró kihasználja a kimenő kapcsolatokat, hogy telemetria küldjön az Azure-ba, ha erre van konfigurálva. 

## <a name="metrics"></a>Mérőszámok
Alapértelmezés szerint a saját üzemeltetésű átjáró a [felhőben](api-management-howto-use-azure-monitor.md)lévő felügyelt átjáróval megegyező számú metrikát bocsát ki [Azure monitoron](https://azure.microsoft.com/services/monitor/)keresztül. 

A szolgáltatás engedélyezhető vagy letiltható az `telemetry.metrics.cloud` átjáró üzembe helyezésének ConfigMap található kulccsal. Alább látható az elérhető konfigurációk részletezése:

| Mező  | Alapértelmezett | Leírás |
| ------------- | ------------- | ------------- |
| telemetria. Metrics. Cloud  | `true` | Engedélyezi a Azure Monitor való naplózást. Az érték lehet `true` `false` . |


Íme egy példa konfiguráció:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

A saját üzemeltetésű átjáró jelenleg a következő metrikákat bocsátja Azure Monitor:

| Metrika  | Leírás |
| ------------- | ------------- |
| Kérelmek  | API-kérelmek száma az adott időszakban |
| Az átjáró kéréseinek időtartama | A kérelem átjáróhoz való megérkezése és a teljes válasz elküldése között eltelt ezredmásodpercek száma |
| A háttérbeli kérelmek időtartama | A háttérrendszer I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása)  |

## <a name="logs"></a>Naplók

A saját üzemeltetésű átjáró jelenleg nem küld [diagnosztikai naplókat](./api-management-howto-use-azure-monitor.md#activity-logs) a felhőbe. A helyi átjáró üzembe helyezésekor azonban [helyileg is konfigurálhatja és](how-to-configure-local-metrics-logs.md) megtarthatja a naplókat. 

Ha az [Azure Kubernetes szolgáltatásban](https://azure.microsoft.com/services/kubernetes-service/)átjáró van üzembe helyezve, engedélyezheti [Azure monitor a](../azure-monitor/insights/container-insights-overview.md) tárolók számára, hogy begyűjtsön naplókat a tárolóból, és megtekintse őket a log Analyticsban. 


## <a name="next-steps"></a>További lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* A [naplók helyi konfigurálásának és](how-to-configure-local-metrics-logs.md) megőrzésének ismertetése
