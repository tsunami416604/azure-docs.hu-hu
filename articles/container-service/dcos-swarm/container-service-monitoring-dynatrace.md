---
title: "A figyelő Azure DC/OS-fürtről - Dynatrace |} Microsoft Docs"
description: "Egy Azure tároló szolgáltatás DC/OS fürtben Dynatrace a figyelheti. A DC/OS Irányítópult segítségével telepítheti a Dynatrace OneAgent."
services: container-service
documentationcenter: 
author: MartinGoodwell
manager: 
editor: 
tags: acs, azure-container-service
keywords: "Tárolók, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6fa23728680779e33eda7bb9aa8a01b9cad9a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Egy Azure tároló szolgáltatás DC/OS fürtben a Dynatrace SaaS/felügyelt figyelése
Ebben a cikkben azt megmutatja, hogyan telepítheti a [Dynatrace](https://www.dynatrace.com/) OneAgent figyelése az Azure Tárolószolgáltatás-fürt minden ügynök csomópontján. A Dynatrace SaaS/felügyelt fiók ehhez a konfigurációhoz szükség van. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/felügyelt
Dynatrace egy olyan felhőalapú natív figyelési megoldás magas dinamikus tároló és a fürt környezetekhez. Ez lehetővé teszi, hogy jobban optimalizálhatja a üzemelő tárolópéldányokat és memória-kiosztásokat valós idejű használati adatok használatával. Is képes automatikusan felügyelő alkalmazás és az infrastruktúra-problémák automatikus viszonyítási probléma korrelációs és kiváltó okának észlelési megadásával.

Az alábbi ábrán láthatók a Dynatrace felhasználói felületén:

![Dynatrace felhasználói felület](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Előfeltételek 
[Telepítése](container-service-deployment.md) és [csatlakozás](./../container-service-connect.md) állította be Azure Tárolószolgáltatási fürthöz. Ismerkedjen meg a [Marathon felhasználói felülettel](container-service-mesos-marathon-ui.md). Ugrás a [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) Dynatrace Szolgáltatottszoftver-fiók beállítása.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>A marathon segítségével Dynatrace telepítés konfigurálása
Ezeket a lépéseket megmutatja, hogyan konfigurálja és alkalmazza őket a marathon segítségével a fürt Dynatrace alkalmazásokat.

1. A DC/OS felhasználói felületén keresztül elérni [http://localhost:80 /](http://localhost:80/). Egyszer a DC/OS felhasználói felületének, keresse meg a **Universe** fülre, majd keresse meg a **Dynatrace**.

    ![A DC/OS Universe Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. A konfigurálás befejezéséhez szüksége egy Dynatrace Szolgáltatottszoftver-fiók vagy egy ingyenes próbafiókot. Miután bejelentkezik a Dynatrace irányítópultot, válassza ki a **telepítése Dynatrace**.

    ![A PaaS integrációs Dynatrace beállítása](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. A lapon válassza ki a **PaaS-integráció beállítása**. 

    ![Dynatrace API jogkivonat](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Adjon meg az API-token belül a DC/OS Universe Dynatrace OneAgent konfigurációját. 

    ![A DC/OS Universe Dynatrace OneAgent konfiguráció](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. A példányok állítsa a futtatni kívánt csomópontok száma. Nagyobb értékre is működik, de a DC/OS új példányok kereséséhez, amíg el nem, hogy a kívánt ténylegesen tovább próbálkozik. Ha szeretné, akkor is állíthatja ezt például 1000000 értékre. Ebben az esetben ha új csomópontot a fürthöz hozzáadott, Dynatrace automatikusan telepíti a az ügynök új csomóponton, a DC/OS folyamatosan további példányok telepítésének megkísérlése áron.

    ![A DC/OS Universe-példányok Dynatrace konfiguráció](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Következő lépések

Ha a csomag telepítése, lépjen vissza a Dynatrace irányítópult. Ismerje meg a másik a szoftverhasználati mérési adatok a tárolók a fürtön belül. 