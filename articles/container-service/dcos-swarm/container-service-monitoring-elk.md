---
title: (ELAVULT) Azure DC/OS-fürt - ELK stack figyelése
description: Az elk-t (Elasticsearch, Logstash és Kibana) az Azure Container Service-fürtöt egy DC/OS-fürt monitorozására.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998131"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(ELAVULT) A figyelő az Azure Container Service-fürt az elk-val

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan lehet az elk-t (Elasticsearch, Logstash, a Kibana) stack az Azure Container Service DC/OS fürt üzembe helyezése. 

## <a name="prerequisites"></a>Előfeltételek
[Üzembe helyezése](container-service-deployment.md) és [csatlakozás](../container-service-connect.md) egy Azure Container Service által konfigurált DC/OS-fürt. Ismerje meg, a DC/OS-irányítópult és a Marathon-szolgáltatás [Itt](container-service-mesos-marathon-ui.md). Is telepítheti a [Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>Elk-t (Elasticsearch, Logstash, Kibana)
Az ELK stack az Elasticsearch, Logstash és Kibana, amelyek segítségével figyelheti és elemezheti a naplókat a fürtben található egy teljes körű stack biztosító kombinációja.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Az ELK stack konfigurálása DC/OS-fürtön
A DC/OS felhasználói felületen keresztüli eléréséhez [ http://localhost:80/ ](http://localhost:80/) egyszer a DC/OS felhasználói felületen navigáljon **Universe**. Keresése és telepítése az Elasticsearch, Logstash és Kibana, a DC/OS universe rendszerben, és ebben a meghatározott sorrendben. Többet is megtudhat a konfigurációval kapcsolatos Ha a **speciális telepítési** hivatkozásra.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Miután az elk-val tárolók és a rendszer működőképességét, hogy a Marathon-LB keresztül érhetők el a Kibana engedélyeznie kell. Navigáljon a **szolgáltatások** > **kibana**, és kattintson a **szerkesztése** alább látható módon.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Váltsa át a **JSON üzemmódot** , és görgessen le a címkék szakaszban.
Hozzá kell adnia egy `"HAPROXY_GROUP": "external"` bejegyzés itt látható módon az alábbi.
Miután rákattint **üzembe helyezése változásokat**, a tároló újraindul.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Győződjön meg arról, hogy a Kibana regisztrálva van szolgáltatásként, a HAPROXY irányítópultján szeretne, ha meg kell nyitnia a portot 9090 az ügynök fürtön HAPROXY futása port 9090.
Alapértelmezés szerint nem megnyitni portok: 80-as, 8080-as, és a 443-as, a DC/OS-ügynök fürtben.
Nyisson meg egy portot, és adja meg a nyilvános felmérése utasításokat [Itt](container-service-enable-public-access.md).

A HAPROXY irányítópult eléréséhez, nyissa meg a Marathon-LB rendszergazdai felület található: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Keresse meg az URL-címet, miután visszalépve megtekintheti a HAPROXY irányítópult, ahogy az alábbi, és a kibana olyan tétel megjelenik.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


A Kibana irányítópultját, 5601 portra van telepítve, amelyre szüksége, nyissa meg az 5601. Kövesse az utasításokat [Itt](container-service-enable-public-access.md). Nyissa meg a Kibana irányítópultját a következő: `http://localhost:5601`.

## <a name="next-steps"></a>További lépések

* Rendszer- és naplók továbbítását és a telepítés: [Naplókezelés a DC/OS az elk-val](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Naplók szűrése, lásd: [naplók szűrése az elk-val](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

