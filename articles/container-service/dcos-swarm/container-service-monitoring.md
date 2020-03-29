---
title: (ELAVULT) Az Azure DC/OS-fürt figyelése – Datadog
description: Egy Azure Container Service-fürt figyelése datadog. A DC/OS webes felhasználói felület használatával telepítse a Datadog ügynökök a fürtre.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275255"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(ELAVULT) Azure Container Service DC/OS-fürt figyelése datadogmal

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben a Datadog-ügynököket az Azure Container Service-fürt összes ügynökcsomópontjára telepítjük. Szüksége lesz egy fiókot Datadog ehhez a konfigurációhoz. 

## <a name="prerequisites"></a>Előfeltételek
[Helyezzen üzembe](container-service-deployment.md) és [csatlakoztasson](../container-service-connect.md) egy, az Azure Container Service által konfigurált fürtöt. Fedezze fel a [Marathon UI](container-service-mesos-marathon-ui.md). Tovább, [https://datadoghq.com](https://datadoghq.com) hogy hozzanak létre egy Datadog számla. 

## <a name="datadog"></a>Datadog (Adatkutya)
A Datadog egy figyelési szolgáltatás, amely az Azure Container Service-fürtön belüli tárolókból gyűjti a figyelési adatokat. Datadog rendelkezik egy Docker-integrációs irányítópult, ahol megtekintheti a tárolókon belül adott metrikák. A tárolókból összegyűjtött mérőszámok cpu, memória, hálózat és i/o szerint vannak rendezve. Datadog osztja metrikák tárolók és képek. Egy példa arra, hogy hogyan néz ki a felhasználói felület a CPU-használathoz, az alábbiakban található.

![Datadog felhasználói felület](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Datadog üzembe helyezésének konfigurálása a Marathon segítségével
Ezek a lépések bemutatják, hogyan konfigurálhatja és telepítheti a Datadog-alkalmazásokat a fürtre a Marathon segítségével. 

A DC/OS felhasználói [http://localhost:80/](http://localhost:80/)felületét a segítségével érheti el. Miután a DC / OS UI navigálni a "Universe", amely a bal alsó sarokban, majd keressen a "Datadog", és kattintson a "Telepítés."

![Datadog csomag a DC / OS Univerzum](./media/container-service-monitoring/datadog1.png)

Most a konfiguráció befejezéséhez szüksége lesz egy Datadog fiókra vagy egy ingyenes próbafiókra. Miután bejelentkezett a Datadog webhelyre, balra néz, és az Integrációk -> majd [API-kat.](https://app.datadoghq.com/account/settings#api) 

![Datadog API kulcs](./media/container-service-monitoring/datadog2.png)

Ezután adja meg az API-kulcsot a Datadog konfigurációa a DC / OS Univerzumban. 

![Datadog konfiguráció a DC/OS univerzumban](./media/container-service-monitoring/datadog3.png) 

A fenti konfigurációs példányok vannak beállítva 10000000, így amikor egy új csomópont ot a fürtdatadog automatikusan telepíti az ügynököt, hogy a csomópont. Ez egy átmeneti megoldás. Miután telepítette a csomagot, vissza kell navigálnia a Datadog webhelyére, és meg kell találnia a "[Irányítópultokat](https://app.datadoghq.com/dash/list)". Innen egyéni és integrációs irányítópultok láthatók. A [Docker-irányítópult](https://app.datadoghq.com/screen/integration/docker) a fürt figyeléséhez szükséges összes tárolómetrikával rendelkezik. 

