---
title: Naplók és metrikák megtekintése a Kibana és a Grafana használatával
description: Naplók és metrikák megtekintése a Kibana és a Grafana használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d876862d8f41ab8df646bef051629fd45c4d4601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939624"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Naplók és metrikák megtekintése a Kibana és a Grafana használatával

A Kibana és a Grafana webes irányítópultok megállapításokat és átláthatóságot biztosítanak az Azure Arc-kompatibilis adatszolgáltatások által használt Kubernetes-névterekben.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>A fürt IP-címének beolvasása

Az irányítópultok eléréséhez le kell kérnie a fürt IP-címét. A helyes IP-cím beolvasásának módszere attól függően változik, hogy hogyan választotta a Kubernetes telepítését. A lenti beállításokkal megkeresheti a megfelelőt.

### <a name="azure-virtual-machine"></a>Azure virtuális gép

A nyilvános IP-címet a következő paranccsal kérheti le:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm-fürt

A fürt IP-címének lekéréséhez használja a következő parancsot:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AK vagy más elosztott terhelésű fürt

A környezet AK-ban vagy más elosztott terhelésű fürtben való figyeléséhez le kell kérnie a felügyeleti proxy szolgáltatás IP-címét. Ezzel a paranccsal kérheti le a **külső IP-** címet:

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>További tűzfal-konfiguráció

Előfordulhat, hogy meg kell nyitnia a portokat a tűzfalon a Kibana-és Grafana-végpontok eléréséhez.

Az alábbi példa bemutatja, hogyan teheti ezt meg egy Azure-beli virtuális gépen. Ezt akkor kell megtennie, ha a szkript használatával telepítette a Kubernetes.

Az alábbi lépések kiemelik, hogyan hozhat létre NSG-szabályt a Kibana és a Grafana végpontokhoz:

### <a name="find-the-name-of-the-nsg"></a>Az NSG-név megkeresése

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>A NSG-szabály hozzáadása

Ha megvan a NSG neve, hozzáadhat egy szabályt a következő parancs használatával:

```console
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Az Azure SQL felügyelt példányainak figyelése az Azure arc szolgáltatásban

Most, hogy már rendelkezik az IP-címmel, és elérhetővé tette a portokat, el kell tudnia érni a Grafana és a Kibana.

> [!NOTE]
>  Amikor a rendszer a Felhasználónév és a jelszó megadását kéri, adja meg az Azure arc-adatkezelő létrehozásakor megadott felhasználónevet és jelszót.

> [!NOTE]
>  A rendszer figyelmeztetést küld a tanúsítványra, mert az előzetes verzióban használt tanúsítványok önaláírt tanúsítványok.

Az alábbi URL-minta használatával érheti el az Azure SQL felügyelt példányának naplózási és figyelési irányítópultját:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

A kapcsolódó irányítópultok a következők:

* "Az Azure SQL felügyelt példány Metrikái"
* "Gazdagép-csomópont Metrikái"
* "Gazda hüvely mérőszámai"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Figyelő Azure Database for PostgreSQL nagy kapacitású – Azure arc

A következő URL-minta használatával férhet hozzá a PostgreSQL nagy kapacitású naplózási és figyelési irányítópultokhoz:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

A kapcsolódó irányítópultok a következők:

* "Postgres mérőszámok"
* "Postgres táblázat Metrikái"
* "Gazdagép-csomópont Metrikái"
* "Gazda hüvely mérőszámai"

## <a name="next-steps"></a>Következő lépések
- [A metrikák és naplók feltöltésének](upload-metrics-and-logs-to-azure-monitor.md) kipróbálása Azure monitor
- További információ a Grafana:
   - [Bevezetés](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana alapjai](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana oktatóanyagok](https://grafana.com/tutorials/grafana-fundamentals/#1)
- További információ a Kibana
   - [Bevezetés](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana útmutató](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Bevezetés az irányítópult szövegéig az adatvizualizációkkal a Kibana-ben](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana-irányítópultok létrehozása](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

