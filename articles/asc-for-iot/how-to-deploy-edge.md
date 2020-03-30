---
title: Az Azure Security Center telepítése az IoT Edge modulhoz| Microsoft dokumentumok
description: Ismerje meg, hogyan telepíthet egy Azure Security Center for IoT biztonsági ügynök ioT Edge.Learn about how to deploy an Azure Security Center for IoT security agent on IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964039"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése az IoT Edge-eszközön


**Az Azure Security Center for IoT** modul átfogó biztonsági megoldást nyújt az IoT Edge-eszközökhöz.
A biztonsági modul összegyűjti, összesíti és elemzi az operációs rendszer és a tárolórendszer nyers biztonsági adatait, és végrehajtható biztonsági javaslatokat és riasztásokat elemez.
További információ: [Security module for IoT Edge](security-edge-architecture.md).

Ebben a cikkben megtudhatja, hogyan telepíthet egy biztonsági modult az IoT Edge-eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul telepítése

Az alábbi lépésekkel üzembe helyezheti az Azure Security Center for IoT biztonsági modult az IoT Edge számára.

### <a name="prerequisites"></a>Előfeltételek

1. Az IoT Hubban győződjön meg arról, hogy az eszköz [iotedge-eszközként van regisztrálva.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Az Azure Security Center for IoT Edge modul megköveteli, hogy az [AuditD keretrendszer](https://linux.die.net/man/8/auditd) telepítve van az IoT Edge-eszközön.

    - Telepítse a keretrendszert a következő parancs futtatásával az IoT Edge-eszközön:
   
    `sudo apt-get install auditd audispd-plugins`

    - Ellenőrizze, hogy a Naplózás aktív-e a következő parancs futtatásával: 
   
    `sudo systemctl status auditd`<br>
    - A várt válasz:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés az Azure Portal használatával

1. Az Azure Portalon nyissa meg a **Marketplace-t.**

1. Válassza **a dolgok internete**lehetőséget, majd keresse meg az Azure Security Center for **IoT-t,** és jelölje ki.

   ![Válassza ki az Azure Security Center for IoT-t](media/howto/edge-onboarding-8.png)

1. A központi telepítés konfigurálásához kattintson a **Létrehozás** gombra. 

1. Válassza ki az IoT Hub **Azure-előfizetését,** majd válassza ki az **IoT Hubot.**<br>Válassza **a Telepítés eszközre** lehetőséget egyetlen eszköz célzásához, vagy válassza **a Telepítés skálán** lehetőséget több eszköz célzásához, majd kattintson a **Létrehozás gombra.** A nagy méretű üzembe helyezésről további információt [a Telepítés című témakörben talál.](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor) 

    >[!Note] 
    >Ha a **Telepítés méretarányosan**lehetőséget választotta, adja hozzá az eszköz nevét és adatait, mielőtt folytatná a **Modulok hozzáadása** lapot az alábbi utasításokban.     

Az IoT Edge üzembe helyezésének befejezéséhez hajtsa végre az IoT Edge-et az IoT-alapú IoT-központszámára. 

#### <a name="step-1-modules"></a>1. lépés: Modulok

1. Válassza ki az **AzureSecurityCenterforIoT modult.**
1. A **Modulbeállítások** lapon módosítsa a **nevet** **azureiotsecurity**névre.
1. A **Madárváltozók** lapon szükség esetén adjon hozzá egy változót (például hibakeresési szintet).
1. A **Tároló létrehozása beállításai** lapon adja hozzá a következő konfigurációt:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. A **Modul ikerbeállítások** lapján adja hozzá a következő konfigurációt:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Válassza a **Frissítés** lehetőséget.

#### <a name="step-2-runtime-settings"></a>2. lépés: Futásidejű beállítások

1. Válassza a **Futásidejű beállítások lehetőséget.**
1. Az **Edge Hub csoportban**módosítsa a **képet** **mcr.microsoft.com/azureiotedge-hub:1.0.8.3.**
1. A **Beállítások létrehozása ellenőrzése** a következő konfigurációra van beállítva: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Kattintson a **Mentés** gombra.
   
1. Válassza a **Tovább lehetőséget.**

#### <a name="step-3-specify-routes"></a>3. lépés: Útvonalak megadása 

1. Az **Útvonalak megadása** lapon győződjön meg arról, hogy rendelkezik egy útvonallal (explicit vagy implicit), amely továbbítja az üzeneteket az **azureiotsecurity** modulból **$upstream** a következő példák szerint. Csak akkor, ha az útvonal a helyén van, válassza a **Tovább**gombot.

   Példa útvonalak:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Válassza a **Tovább lehetőséget.**

#### <a name="step-4-review-deployment"></a>4. lépés: A telepítés áttekintése

- A **Telepítés áttekintése** lapon tekintse át a központi telepítési információkat, majd válassza a **Létrehozás** lehetőséget a központi telepítés befejezéséhez.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémát tapasztal, a tárolónaplók a legjobb módja az IoT Edge biztonsági modul eszköz állapotának megismerésének. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e és működnek-e a várt módon

1. Futtassa a következő parancsot az IoT Edge-eszközön:
    
    `sudo docker ps`
   
1. Ellenőrizze, hogy a következő tárolók futnak-e:
   
   | Név | KÉP |
   | --- | --- |
   | azureiotsecurity (azureiotsecurity) | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Ha a minimálisan szükséges tárolók nincsenek jelen, ellenőrizze, hogy az IoT Edge-telepítési jegyzék fájlja igazodik-e az ajánlott beállításokhoz. További információ: [Deploy IoT Edge module.](#deployment-using-azure-portal)

### <a name="inspect-the-module-logs-for-errors"></a>A modulnaplók ban hibák at keresnek
   
1. Futtassa a következő parancsot az IoT Edge-eszközön:

   `sudo docker logs azureiotsecurity`
   
1. További részletes naplók, adja hozzá a következő környezeti változó az **azureiotsecurity** modul központi telepítése: `logLevel=Debug`.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a konfigurációs beállításokról, folytassa a modulkonfiguráció útmutatójával. 
> [!div class="nextstepaction"]
> [Modulkonfiguráció útmutatója](./how-to-agent-configuration.md)
