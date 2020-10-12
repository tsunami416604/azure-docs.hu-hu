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
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936046"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Az Azure arc-kompatibilis adatszolgáltatások naplóinak beolvasása

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure arc-kompatibilis adatszolgáltatási naplók lekéréséhez szüksége lesz az Azure-beli adatcli-eszközre. [Telepítési utasítások](./install-client-tools.md)

A rendszergazdaként be kell jelentkeznie az Azure arc használatára képes adatszolgáltatás-kezelő szolgáltatásba.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Az Azure arc-kompatibilis adatszolgáltatások naplóinak beolvasása

Az Azure arc-kompatibilis adatszolgáltatások naplóit az összes hüvelyben vagy adott hüvelyben hibaelhárítási célból érheti el.  Ezt megteheti a szabványos Kubernetes-eszközökkel, például a `kubectl logs` paranccsal vagy ebben a cikkben az Azure-beli adatcli-eszköz használatával, amely megkönnyíti az összes napló egyszeri beolvasását.

Először győződjön meg róla, hogy bejelentkezett az adatvezérlőbe.

```console
azdata login
```

Ezután futtassa a következő parancsot a naplók kiírásához:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

A naplófájlok az aktuális munkakönyvtárban lesznek létrehozva, alapértelmezés szerint a "naplók" nevű alkönyvtárban.  A naplófájlokat egy másik könyvtárba is exportálhatja a paraméter használatával `--target-folder` .

A (z) paraméter kihagyása mellett dönthet úgy is, hogy tömöríti a fájlokat `--skip-compress` .

A memóriaképek kiváltásával kihagyhatja a memóriaképeket `--exclude-dumps` , de ez nem ajánlott, kivéve, ha Microsoft ügyfélszolgálata a memóriaképek kérését kérte.  A memóriakép készítése megköveteli, hogy az adatvezérlő beállítása `allowDumps` `true` az adatvezérlő létrehozási idejére legyen beállítva.

Dönthet úgy is, hogy szűréssel gyűjti a naplókat egy adott Pod ( `--pod` ) vagy tároló ( `--container` ) név alapján.

Azt is megteheti, hogy szűréssel gyűjti a naplókat egy adott egyéni erőforráshoz a és a ismerhető átadásával `--resource-kind` `--resource-name` .  A `resource-kind` paraméter értékének az egyes egyéni erőforrás-definíciós nevek egyikének kell lennie, amelyeket a parancs kérhet le `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Példa a mappa-hierarchiára.  Vegye figyelembe, hogy a mappa-hierarchiát a pod Name neve, majd a tároló, majd a tárolón belüli címtár-hierarchia rendezi.

```console
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