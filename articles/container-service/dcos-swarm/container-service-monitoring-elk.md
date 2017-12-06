---
title: "Egy Azure DC/OS-fürtről - ELK verem figyelése"
description: "A DC/OS fürtben Azure Tárolószolgáltatás-fürt ELK (Elasticsearch Logstash és Kibana) a figyelheti."
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: b378fc38233534df74582388e6e832d40f431d11
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>A figyelő egy ELK az Azure Tárolószolgáltatás-fürt

Ebben a témakörben bemutatjuk, hogyan központi telepítése az Azure Tárolószolgáltatásban DC/OS-fürtön a ELK (Elasticsearch, Logstash, Kibana) verem. 

## <a name="prerequisites"></a>Előfeltételek
[Telepítése](container-service-deployment.md) és [csatlakozás](../container-service-connect.md) egy Azure Tárolószolgáltatásban konfigurált DC/OS-fürtben. A DC/OS-irányítópult és a Marathon szolgáltatások [Itt](container-service-mesos-marathon-ui.md). Is telepítheti a [Marathon terheléselosztó](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK verem Elasticsearch, Logstash és egy teljes körű verem használható figyelésére és a fürt naplóinak elemzése biztosító Kibana.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>A DC/OS-fürtről a ELK verem konfigurálása
A DC/OS felhasználói felületén keresztül elérni [http://localhost:80 /](http://localhost:80/) egyszer a DC/OS felhasználói felületének navigáljon **Universe**. Keressen, és telepítse a Elasticsearch Logstash és Kibana, a DC/OS Universe és a megadott sorrendben. További konfigurációjával kapcsolatos Ha a **speciális telepítési** hivatkozásra.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Egyszer a ELK tárolók és a használatra, lehetővé kell tenni Kibana elérhető Marathon-LB keresztül. Navigáljon a **szolgáltatások** > **kibana**, és kattintson a **szerkesztése** alább látható módon.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Kapcsolót **JSON üzemmódot** és görgessen le a címkék szakaszban.
Hozzá kell adnia egy `"HAPROXY_GROUP": "external"` bejegyzés itt látható az alábbi.
Miután rákattintott **módosítása**, a tároló újraindul.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Ha szeretné ellenőrizni, hogy Kibana regisztrálva van-e a haproxy esetén irányítópulton szolgáltatásként, akkor port 9090 futó haproxy esetén az ügynök fürtön 9090 port megnyitásához.
Alapértelmezés szerint azt nyissa meg a portok: 80-as, 8080-as, és a 443-as, a DC/OS-ügynök fürtben.
Nyissa meg a portot, és adja meg a nyilvános értékeléséhez utasításai [Itt](container-service-enable-public-access.md).

A haproxy esetén irányítópult eléréséhez, nyissa meg a Marathon-LB admin illesztő: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Keresse meg az URL-címet, miután kell megjelennie a haproxy esetén irányítópult alább látható módon, és megjelenik egy tételt a Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


A port 5601 van telepítve, Kibana irányítópult eléréséhez nyissa meg a portot 5601 kell. Kövesse az utasításokat [Itt](container-service-enable-public-access.md). Nyissa meg a következő Kibana irányítópult: `http://localhost:5601`.

## <a name="next-steps"></a>Következő lépések

* Rendszer- és napló továbbítás és a telepítés [napló kezelése a DC/OS rendelkező ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Szűrje naplókat, lásd: [naplók szűrése és ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

