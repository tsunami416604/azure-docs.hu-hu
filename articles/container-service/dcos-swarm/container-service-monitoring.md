---
title: (ELAVULT) A figyelő Azure DC/OS-fürt – Datadoggal
description: Az Azure Container Service-fürt a Datadoggal figyelése. A DC/OS webes felhasználói felület segítségével üzembe helyezése a Datadoggal ügynökök a fürtön.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d881a5c0f994b627b4c7c3da362672b3b887cd5e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996154"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(ELAVULT) Egy Azure Container Service DC/OS fürt megfigyelése a Datadoggal

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben azt Datadoggal ügynökök telepíti az Azure Container Service-fürt összes ügynökcsomópontjára. Szüksége lesz egy fiókra a Datadoggal ehhez a konfigurációhoz. 

## <a name="prerequisites"></a>Előfeltételek
[Helyezzen üzembe](container-service-deployment.md) és [csatlakoztasson](../container-service-connect.md) egy, az Azure Container Service által konfigurált fürtöt. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Lépjen a [ http://datadoghq.com ](http://datadoghq.com) a Datadoggal fiók beállításához. 

## <a name="datadog"></a>Datadoggal
Datadoggal egy olyan megfigyelő szolgáltatás, amely figyelési adatait gyűjti össze az Azure Container Service-fürtben található tárolókat. Datadoggal rendelkezik, ahol megtekintheti az adott mérőszámok belül a tárolókat a Docker integrációs irányítópult. A tárolók gyűjtött metrikák CPU, memória, hálózati és i/o szerint vannak rendezve. Datadoggal metrikák felosztja a tárolók és a képeket. Egy példa a felhasználói felület néz ki a CPU-használat nem éri el.

![Datadoggal felhasználói felület](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>A marathon segítségével konfigurálhatja a Datadoggal telepítést
Ezek a lépések bemutatják, a fürtön a Marathonnal Datadoggal alkalmazások központi telepítése és konfigurálása. 

A DC/OS felhasználói felületen keresztüli eléréséhez [ http://localhost:80/ ](http://localhost:80/). Egyszer a DC/OS felhasználói felületen navigáljon a "Universe", amely a bal alsó részén található, és keressen a "Datadoggal", és kattintson a "Telepítés".

![A DC/OS universe rendszerben található Datadoggal csomag](./media/container-service-monitoring/datadog1.png)

Most már a konfigurálás befejezéséhez szüksége lesz egy Datadoggal fiókot vagy egy ingyenes próbafiókot. A Datadoggal webhely tekintse meg a bal oldali jelentkezett be, és a Integrációk Ugrás -> majd [API-k](https://app.datadoghq.com/account/settings#api). 

![Datadoggal API-kulcs](./media/container-service-monitoring/datadog2.png)

A Datadoggal konfiguráció a DC/OS universe rendszerben található, írja be az API-kulcsot. 

![Datadoggal konfiguráció a DC/OS universe rendszerben](./media/container-service-monitoring/datadog3.png) 

A fenti konfigurációban példányok 10000000 vannak beállítva, így minden alkalommal, amikor egy új csomópontot ad a fürthöz Datadoggal automatikusan telepíti egy ügynököt a csomóponton. Ez az egy ideiglenes megoldás. Miután telepítette a csomagot kell lépjen vissza a Datadoggal webhelyet, és keresse meg "[irányítópultok](https://app.datadoghq.com/dash/list)." Itt láthatja, és egyéni integrációs irányítópultok. A [Docker irányítópult](https://app.datadoghq.com/screen/integration/docker) lesz szüksége a fürt figyelése tárolómetrikák. 

