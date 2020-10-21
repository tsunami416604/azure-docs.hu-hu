---
title: Naplók beszerzése az Azure arc-kompatibilis adatkezelő hibáinak megoldásához
description: Szolgáltatási naplók beszerzése az Azure arc-kompatibilis adatkezelő hibáinak megoldásához.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320202"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Az Azure arc-kompatibilis adatszolgáltatások naplóinak beolvasása

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

A továbblépés előtt a következőkre lesz szüksége:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [Telepítési utasítások](./install-client-tools.md).
* Rendszergazdai fiók az Azure arc-kompatibilis adatszolgáltatások vezérlőjébe való bejelentkezéshez.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Az Azure arc-kompatibilis adatszolgáltatások naplóinak beolvasása

Az Azure arc-kompatibilis adatszolgáltatások naplóit az összes hüvelyben vagy adott hüvelyben hibaelhárítási célból érheti el. Ezt elvégezheti a szabványos Kubernetes-eszközökkel, például a `kubectl logs` paranccsal, vagy ebben a cikkben az eszközt fogja használni, amely megkönnyíti az [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] összes napló egyszerre történő beolvasását.

1. Jelentkezzen be az adatkezelőbe egy rendszergazdai fiókkal.

   ```console
   azdata login
   ```

2. A naplók kiírásához futtassa a következő parancsot:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Például:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Az adatkezelő létrehozza a naplófájlokat az aktuális munkakönyvtárban egy nevű alkönyvtárban `logs` . 

## <a name="options"></a>Beállítások

`azdata arc dc debug copy-logs` a a következő beállításokat biztosítja a kimenet kezeléséhez.

* A naplófájlokat egy másik könyvtárba írja a paraméter használatával `--target-folder` .
* Tömörítse a fájlokat úgy, hogy kihagyja a `--skip-compress` paramétert.
* A memóriaképek kihagyása és a memóriaképek belefoglalása `--exclude-dumps` . Ez a módszer nem ajánlott, hacsak Microsoft ügyfélszolgálata nem kérte a memóriaképek megadását. A memóriakép készítése megköveteli, hogy az adatvezérlő beállítása `allowDumps` `true` az adatvezérlő létrehozási idejére legyen beállítva.
* Szűrés a naplók összegyűjtéséhez csak egy adott Pod ( `--pod` ) vagy tároló ( `--container` ) néven.
* A (z `--resource-kind` ) és paraméter átadásával szűrheti egy adott egyéni erőforrás naplóinak gyűjtését `--resource-name` . A `resource-kind` paraméter értékének az egyéni erőforrás-definíciós nevek egyikének kell lennie, amelyet a parancs kérhet le `kubectl get customresourcedefinition` .

Ezeket a paramétereket a következő példában lecserélheti `<parameters>` . 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Példa:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Példa a mappa-hierarchiára. A mappa-hierarchiát a pod name, majd a Container, majd a tárolón belüli címtár-hierarchia alapján rendezi a rendszer.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Következő lépések

[azdata arc DC hibakeresési naplók](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)