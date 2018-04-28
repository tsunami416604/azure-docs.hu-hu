---
title: A figyelő Azure DC/OS-fürtről - Datadog
description: Az Azure Tárolószolgáltatás-fürt Datadog a figyelheti. A DC/OS webes felhasználói felület segítségével a Datadog ügynökök telepítésére a fürthöz.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 029f36e4362704fcec240f6e88da5c96e903c317
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Egy Azure tároló szolgáltatás DC/OS fürt Datadog figyelése

Ebben a cikkben azt Datadog ügynökök telepíti az Azure Tárolószolgáltatás-fürt minden ügynök csomópontján. Ehhez a konfigurációhoz szüksége lesz egy fiókra Datadog. 

## <a name="prerequisites"></a>Előfeltételek
[Helyezzen üzembe](container-service-deployment.md) és [csatlakoztasson](../container-service-connect.md) egy, az Azure Container Service által konfigurált fürtöt. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Ugrás a [ http://datadoghq.com ](http://datadoghq.com) egy Datadog fiók beállítása. 

## <a name="datadog"></a>Datadog
Datadog egy figyelési szolgáltatás, amely a figyelési adatokat gyűjt a tárolók skálázása az Azure Tárolószolgáltatás-fürt belül. Datadog rendelkezik egy Docker integrációs irányítópultot, ahol láthatja adott mérőszámok belül a tárolók. A tárolók gyűjtött metrikák Processzor, memória, hálózati és i/o szerint vannak rendszerezve. Datadog metrikák felosztja a tárolók és a képeket. Egy példa a felhasználói felület néz a CPU-használat nem éri el.

![Datadog felhasználói felület](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>A marathon segítségével Datadog telepítés konfigurálása
Ezeket a lépéseket bemutatja, hogyan konfigurálhatja és telepítheti a fürtben a marathon segítségével Datadog alkalmazásokat. 

A DC/OS felhasználói felületén keresztül elérni [ http://localhost:80/ ](http://localhost:80/). Egyszer a DC/OS felhasználói felületének keresse meg a "Universe" amelyek a bal alsó részén, majd keresse meg a "Datadog", majd kattintson a "Telepítés".

![A DC/OS Universe belül Datadog csomag](./media/container-service-monitoring/datadog1.png)

Most már a konfiguráció befejezéséhez szüksége lesz egy Datadog fiók vagy egy ingyenes próbafiókot. Amennyiben balra Datadog webhely megjelenését jelentkezett, és integrációk Ugrás -> majd [API-k](https://app.datadoghq.com/account/settings#api). 

![Datadog API-kulcs](./media/container-service-monitoring/datadog2.png)

Ezután adjon meg az API-kulcs a DC/OS Universe belül Datadog konfigurációját. 

![A DC/OS Universe Datadog konfiguráció](./media/container-service-monitoring/datadog3.png) 

A fenti konfigurációban példányok 10000000 értékre van beállítva, ha új csomópontot a fürthöz hozzáadott Datadog automatikusan telepíteni fogja az ügynök ahhoz a csomóponthoz. Ez az egy átmeneti megoldás. Miután telepítette a csomag kell keresse meg a Datadog webhelyre, és keresse "[irányítópultok](https://app.datadoghq.com/dash/list)." Ott látni fogja a egyéni és integrációs irányítópultok. A [Docker irányítópult](https://app.datadoghq.com/screen/integration/docker) lesz szüksége a fürt figyelési tároló metrikákat. 

