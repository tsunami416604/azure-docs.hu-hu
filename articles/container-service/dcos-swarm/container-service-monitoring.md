---
title: ELAVULT Azure DC/OS-fürt figyelése – Datadoggal
description: Azure Container Service-fürt figyelése a Datadoggal. A DC/OS webes felhasználói felület segítségével telepítse a Datadoggal-ügynököket a fürtön.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166145"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>ELAVULT Azure Container Service DC/OS-fürt figyelése a Datadoggal

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben a Datadoggal-ügynököket a Azure Container Service-fürt összes ügynök-csomópontjára telepíteni fogjuk. Ehhez a konfigurációhoz szüksége lesz egy Datadoggal-fiókra. 

## <a name="prerequisites"></a>Előfeltételek
[Helyezzen üzembe](container-service-deployment.md) és [csatlakoztasson](../container-service-connect.md) egy, az Azure Container Service által konfigurált fürtöt. Fedezze fel a [Marathon felhasználói felületét](container-service-mesos-marathon-ui.md). [https://datadoghq.com](https://datadoghq.com) A datadoggal-fiók beállításához nyissa meg a következőt:. 

## <a name="datadog"></a>Datadog
A datadoggal egy figyelési szolgáltatás, amely a Azure Container Service-fürtön belüli tárolók figyelési adatait gyűjti. A datadoggal rendelkezik egy Docker-integrációs irányítópulttal, ahol megtekintheti a tárolókban található konkrét mérőszámokat. A tárolók által gyűjtött mérőszámokat a CPU, a memória, a hálózat és az I/O rendezi. A datadoggal tárolók és képek számára osztja szét a metrikákat. Alább látható egy példa arra, hogy a felhasználói felület hogyan néz ki a CPU-használathoz.

![Datadoggal felhasználói felület](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Datadoggal-telepítés konfigurálása a Marathon segítségével
Ezek a lépések bemutatják, hogyan konfigurálhat és helyezhet üzembe Datadoggal-alkalmazásokat a fürtön a Marathon segítségével. 

A DC/OS felhasználói felületét `http://localhost:80/`a használatával érheti el. Egyszer a DC/OS felhasználói felületen navigáljon a "Universe" elemre, amely a bal alsó sarokban található, majd keressen rá a "Datadoggal" kifejezésre, és kattintson a "telepítés" gombra.

![Datadoggal csomag a DC/OS-Univerzumon belül](./media/container-service-monitoring/datadog1.png)

Most, hogy elvégzi a konfigurálást, szüksége lesz egy Datadoggal-fiókra vagy egy ingyenes próbaverziós fiókra. Miután bejelentkezett a Datadoggal webhelyre, keresse fel a bal oldalt, és válassza az integrációk-> majd az [API](https://app.datadoghq.com/account/settings#api)-k lehetőséget. 

![Datadoggal API-kulcs](./media/container-service-monitoring/datadog2.png)

Ezután adja meg az API-kulcsot a DC/OS univerzumban található Datadoggal-konfigurációba. 

![Datadoggal-konfiguráció a DC/OS-univerzumban](./media/container-service-monitoring/datadog3.png) 

A fenti konfigurációs példányok értéke 10000000, így valahányszor új csomópont kerül a fürt Datadoggal, a rendszer automatikusan telepíti az ügynököt a csomópontra. Ez egy átmeneti megoldás. A csomag telepítése után térjen vissza a Datadoggal webhelyére, és keresse meg az "[irányítópultok](https://app.datadoghq.com/dash/list)" kifejezést. Ekkor megjelenik az egyéni és integrációs irányítópultok. A [Docker irányítópultján](https://app.datadoghq.com/screen/integration/docker) megjelenik a fürt figyeléséhez szükséges összes tároló-metrika. 

