---
title: (ELAVULT) Azure DC/OS-fürt figyelése - Dynatrace
description: Egy Azure Container Service DC/OS-fürt figyelése a Dynatrace segítségével. Telepítse a Dynatrace OneAgent a DC/OS irányítópulthasználatával.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277743"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(ELAVULT) Azure Container Service DC/OS-fürt figyelése Dynatrace SaaS/Managed segítségével

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan telepítheti a [Dynatrace](https://www.dynatrace.com/) OneAgent az Azure Container Service-fürt összes ügynökcsomópontjának figyeléséhez. Ehhez a konfigurációhoz szüksége van egy fiókra a Dynatrace SaaS/Managed szolgáltatással. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Felügyelt
A Dynatrace egy natív felhőalapú figyelési megoldás rendkívül dinamikus tároló- és fürtkörnyezetekben. Lehetővé teszi a tárolók központi telepítésének és a memórialefoglalásoknak a valós idejű használati adatok használatával történő optimalizálását. Képes automatikusan meghatározni az alkalmazás- és infrastruktúra-problémákat automatizált alapvonalolás, problémakorreláció és kiváltó ok-észlelés biztosításával.

Az alábbi ábra a Dynatrace felhasználói felületét mutatja:

![Dynatrace felhasználói felület](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Előfeltételek 
[Telepítsen](container-service-deployment.md) és [csatlakozzon](./../container-service-connect.md) az Azure Container Service által konfigurált fürthöz. Fedezze fel a [Marathon UI](container-service-mesos-marathon-ui.md). Nyissa [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) meg a Dynatrace SaaS-fiók beállítását.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Dynatrace üzembe helyezés konfigurálása a Marathon segítségével
Ezek a lépések bemutatják, hogyan konfigurálhatja és telepítheti a Dynatrace-alkalmazásokat a fürtre a Marathon segítségével.

1. A DC/OS felhasználói [http://localhost:80/](http://localhost:80/)felületét a segítségével érheti el. Miután a DC / OS UI, keresse meg a **Világegyetem** fülre, majd keresse meg a **Dynatrace**.

    ![Dynatrace a DC/OS univerzumban](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. A konfiguráció befejezéséhez Dynatrace SaaS-fiókra vagy ingyenes próbafiókra van szükség. Miután bejelentkezett a Dynatrace irányítópultjára, válassza **a Dynatrace telepítése**lehetőséget.

    ![Dynatrace A PaaS-integráció beállítása](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. A lapon válassza a **PaaS-integráció beállítása**lehetőséget. 

    ![Dynatrace API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Adja meg az API-jogkivonatot a Dynatrace OneAgent konfigurációba a DC/OS univerzumban. 

    ![Dynatrace OneAgent konfiguráció a DC/OS univerzumban](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Állítsa be a példányokat a futtatni kívánt csomópontok számára. A nagyobb szám beállítása is működik, de a DC/OS folyamatosan megpróbálja megtalálni az új példányokat, amíg ténylegesen el nem éri ezt a számot. Ha szeretné, ezt is beállíthatja 1000000 értékre. Ebben az esetben, amikor egy új csomópontot ad hozzá a fürthöz, a Dynatrace automatikusan telepít egy ügynököt az adott új csomópontra, a DC/OS árán, amely folyamatosan további példányokat próbál telepíteni.

    ![Dynatrace konfiguráció a DC/OS Universe-példányokban](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>További lépések

Miután telepítette a csomagot, lépjen vissza a Dynatrace irányítópultra. A fürtön belüli tárolók különböző használati metrikáit is megismerheti. 
