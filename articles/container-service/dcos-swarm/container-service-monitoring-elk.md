---
title: (ELAVULT) Azure DC/OS fürt figyelése – ELK-verem
description: Egy DC/OS-fürt figyelése az Azure Container Service-fürt ELK (Elasticsearch, Logstash és Kibana) használatával.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277761"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(ELAVULT) Elk-el egy Azure Container Service-fürt figyelése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan telepítheti az ELK (Elasticsearch, Logstash, Kibana) verem egy DC/OS fürt az Azure Container Service-ben. 

## <a name="prerequisites"></a>Előfeltételek
[Az](container-service-deployment.md) Azure Container Service által konfigurált DC/operációs rendszer fürt üzembe helyezése és [csatlakoztatása.](../container-service-connect.md) Fedezze fel a DC / OS műszerfal és marathon szolgáltatások [itt](container-service-mesos-marathon-ui.md). Telepítse a [Marathon Load Balancer-t](container-service-load-balancing.md)is.


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK verem az Elasticsearch, Logstash és Kibana kombinációja, amely egy végpontig verem, amely a fürt naplóinak figyelésére és elemzésére használható.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Az ELK-verem konfigurálása egy dc/os fürtön
Hozzáférés a DC / [http://localhost:80/](http://localhost:80/) OS UI keresztül Egyszer a DC / OS UI navigálni **Universe**. Keressen és telepítse elasticsearch, Logstash, és Kibana a DC / OS Univerzum és az adott sorrendben. Ha a **Speciális telepítés** hivatkozást, többet is megtudhat a konfigurációról.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2 között](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3 között](./media/container-service-monitoring-elk/elk3.PNG) 

Miután az ELK konténerek és a futás, akkor lehetővé kell tenni Kibana keresztül érhető el Marathon-LB. Nyissa meg a **Szolgáltatások** > **kibana**lapot, és kattintson **a Szerkesztés gombra** az alábbi módon.

![ELK4 között](./media/container-service-monitoring-elk/elk4.PNG)


Váltson **JSON módba,** és görgessen le a címkék szakaszig.
Meg kell adnia egy `"HAPROXY_GROUP": "external"` bejegyzést itt, ahogy az alábbiakban látható.
Ha a **Módosítások telepítése gombra**kattint, a tároló újraindul.

![ELK5 között](./media/container-service-monitoring-elk/elk5.PNG)


Ha ellenőrizni szeretné, hogy kibana szolgáltatásként van-e regisztrálva a HAPROXY irányítópulton, meg kell nyitnia a 9090-es portot az ügynökfürtön, mivel a HAPROXY a 9090-es porton fut.
Alapértelmezés szerint a DC/OS ügynökfürtben megnyitjuk a 80-as, 8080-as és 443-as portokat.
A kikötő megnyitására és a nyilvános értékelésre vonatkozó utasításokat [itt](container-service-enable-public-access.md)találja .

A HAPROXY irányítópult eléréséhez nyissa meg a `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`Marathon-LB felügyeleti felületet a következő helyen: .
Miután az URL-címhez navigált, látnia kell a HAPROXY irányítópultot az alábbiak szerint, és látnia kell a Kibana szolgáltatásbejegyzését.

![ELK6 között](./media/container-service-monitoring-elk/elk6.PNG)


Az 5601-es porton üzembe helyezett Kibana irányítópult eléréséhez meg kell nyitnia az 5601-es portot. Kövesse [az](container-service-enable-public-access.md)itt található utasításokat. Ezután nyissa meg a `http://localhost:5601`Kibana műszerfalat a következő helyen: .

## <a name="next-steps"></a>További lépések

* A rendszer- és alkalmazásnapló-továbbításról és -beállításról a [Naplókezelés egyenárammal/operációs rendszerrel ELK-val című témakörben](https://docs.mesosphere.com/1.8/administration/logging/elk/)található.

* A naplók szűréséhez olvassa el a [Naplók szűrése ELK-val](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

