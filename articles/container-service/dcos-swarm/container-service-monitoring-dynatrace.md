---
title: (ELAVULT) A figyelő Azure DC/OS-fürt – dynatrace-szel
description: A dynatrace-szel egy Azure Container Service DC/OS-fürt monitorozására. A dynatrace-szel OneAgent telepítheti a DC/OS-irányítópult használatával.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002002"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(ELAVULT) Egy Azure Container Service DC/OS-fürtön a dynatrace-szel SaaS és felügyelt figyelése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan helyezhet üzembe a [Dynatrace](https://www.dynatrace.com/) OneAgent figyelése az Azure Container Service-fürt összes ügynökcsomópontjára. Ez a konfiguráció a dynatrace-szel SaaS és felügyelt fiók van szükség. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS és felügyelt
Dynatrace egy olyan natív figyelési megoldás nagymértékben dinamikus tároló és a fürt környezetekhez. Lehetővé teszi, hogy jobban optimalizálása a tárolók üzembe helyezésének és memórialefoglalások valós idejű használati adatok használatával. Automatikusan felügyelő automatizált alapérték szerinti mérési, a probléma korrelációs és a kiváltó észlelését biztosító alkalmazás-és infrastruktúra képes legyen.

A következő ábrán látható a dynatrace-szel felhasználói felületén:

![Dynatrace felhasználói felület](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Előfeltételek 
[Üzembe helyezése](container-service-deployment.md) és [csatlakozás](./../container-service-connect.md) Azure Container Service által konfigurált fürthöz. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Lépjen a [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) a dynatrace-szel SaaS-fiók beállításához.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>A marathon segítségével konfigurálhatja a dynatrace-szel telepítést
Ezek a lépések bemutatják, hogyan konfigurálhatja és telepítheti a dynatrace-szel alkalmazásokat a fürtön a Marathonnal.

1. A DC/OS felhasználói felületen keresztüli eléréséhez [ http://localhost:80/ ](http://localhost:80/). Egyszer a DC/OS felhasználói felületen navigáljon a **Universe** lapra, és keressen **Dynatrace**.

    ![A DC/OS universe rendszerben a dynatrace-szel](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. A konfigurálás befejezéséhez szüksége van a dynatrace-szel SaaS-fiók vagy egy ingyenes próbafiókot. Miután bejelentkezik a dynatrace-szel irányítópulton, válassza ki a **üzembe helyezése Dynatrace**.

    ![PaaS-integrációval Dynatrace beállítása](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Válassza ki a lapon **PaaS közötti integráció beállítása**. 

    ![Dynatrace API-jogkivonat](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Az API-jogkivonat írja be a DC/OS universe rendszerben a dynatrace-szel OneAgent konfigurációra. 

    ![A DC/OS universe rendszerben a dynatrace-szel OneAgent konfiguráció](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Állítsa a példányok futtatni kívánt csomópontok száma. Ha nagyobb értékre is működik, de a DC/OS tovább próbálkozunk új példányok megkereséséhez, amíg el nem, hogy ténylegesen. Igény szerint is állíthatja ezt értékének például 1000000. Ebben az esetben, amikor új csomópontot ad hozzá a fürtöt, Dynatrace automatikusan egy ügynököt helyez üzembe az új csomóponthoz, fáradozunk további példányok üzembe DC/OS áron.

    ![A DC/OS Universe-példányokban a dynatrace-szel konfiguráció](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>További lépések

Miután telepítette a csomagot, lépjen vissza a dynatrace-szel irányítópultra. A fürtben lévő tárolók különböző használati mérőszámait feltárhatja. 