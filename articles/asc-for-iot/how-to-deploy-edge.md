---
title: Azure Security Center üzembe helyezése IoT Edge modulhoz | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy Azure Security Center a IoT biztonsági ügynökön a IoT Edgeon.
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
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: e85738c344189486726b4e7b7f5a76ab03c0ffa9
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991431"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése a IoT Edge eszközön


A **IoT modul Azure Security Center** átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A biztonsági modul az operációs rendszer és a tárolórendszer nyers biztonsági adatainak gyűjtését, összesítését és elemzését végezheti el a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.
További információ: [IoT Edge biztonsági modulja](security-edge-architecture.md).

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy biztonsági modult a IoT Edge eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul telepítése

A következő lépésekkel telepítheti a IoT Edge IoT biztonsági moduljának Azure Security Center.

### <a name="prerequisites"></a>Előfeltételek

1. A IoT Hub ellenőrizze, hogy az eszköz IoT Edge- [eszközként](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)van-e regisztrálva.

1. A IoT Edge modul Azure Security Center a [naplózott keretrendszer](https://linux.die.net/man/8/auditd) telepítése szükséges a IoT Edge eszközön.

    - A keretrendszer telepítéséhez futtassa a következő parancsot a IoT Edge eszközön:
   
    `sudo apt-get install auditd audispd-plugins`

    - A naplózott ellenőrzés aktív a következő parancs futtatásával: 
   
    `sudo systemctl status auditd`<br>
    - A várt válasz: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés Azure Portal használatával

1. A Azure Portal nyissa meg a **Marketplace**-t.

1. Válassza a **eszközök internetes hálózata**lehetőséget, majd keresse meg a **IoT Azure Security Center** , és válassza ki.

   ![IoT Azure Security Center kiválasztása](media/howto/edge-onboarding-8.png)

1. A telepítés konfigurálásához kattintson a **Létrehozás** gombra. 

1. Válassza ki a IoT Hub Azure- **előfizetését** , majd válassza ki a **IoT hub**.<br>Válassza a **telepítés egy eszközre** lehetőséget egyetlen eszköz kiválasztásához, vagy válassza a nagy **méretű üzembe helyezés** lehetőséget a több eszköz megcélzásához, majd kattintson a **Létrehozás**gombra. További információ a nagy léptékű üzembe helyezésről: a [központi](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)telepítés. 

    >[!Note] 
    >Ha a **központi telepítés méretezése**lehetőséget választotta, adja hozzá az eszköz nevét és részleteit, mielőtt továbblép a **modulok hozzáadása** lapra az alábbi utasításokban.     

A IoT számára három lépésben hozhat létre IoT Edge központi Azure Security Center telepítést. A következő szakasz végigvezeti a műveletet. 

#### <a name="step-1-add-modules"></a>1\. lépés: modulok hozzáadása

1. A **modulok hozzáadása** lap **üzembe helyezési modulok** területén kattintson a AzureSecurityCenterforIoT **beállítására** . 
   
1. Módosítsa a **nevet** a **azureiotsecurity**értékre.
1. Módosítsa a **rendszerkép URI-ját** a **MCR.microsoft.com/ascforiot/azureiotsecurity:1.0.0**értékre.
1. Ellenőrizze, hogy a **tároló létrehozási beállításai** érték a következőre van-e beállítva:      
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
1. Ellenőrizze, hogy be van-e jelölve a **Module Twin kívánt tulajdonságainak beállítása** elem, majd módosítsa a konfigurációs objektumot a következőre:
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. Kattintson a **Save** (Mentés) gombra.
1. Görgessen a lap aljára, majd válassza a **speciális Edge-futtatókörnyezet beállításainak konfigurálása**lehetőséget. 
   
1. Módosítsa a **képet** az **Edge Hub** alatt a **MCR.microsoft.com/azureiotedge-hub:1.0.8.3**értékre.

1. A **létrehozási beállítások** ellenőrzése a következőre van beállítva: 
         
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
1. Kattintson a **Save** (Mentés) gombra.
   
1. Kattintson a **Tovább** gombra.

#### <a name="step-2-specify-routes"></a>2\. lépés: útvonalak meghatározása 

1. Az **útvonalak meghatározása** lapon győződjön meg arról, hogy rendelkezik olyan útvonallal (explicit vagy implicit), amely az alábbi példáknak megfelelően továbbítja a **azureiotsecurity** modul üzeneteinek **$upstreamét** , csak ezután kattintson a **tovább**gombra. 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>3\. lépés: az üzembe helyezés áttekintése

- A központi telepítés **áttekintése** lapon tekintse át a központi telepítési adatokat, majd a telepítés befejezéséhez válassza a **Submit (Küldés** ) lehetőséget.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémába ütközik, a tároló naplói a legjobb módszer a IoT Edge biztonsági modul eszközének állapotának megismerésére. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e, és megfelelően működnek-e.

1. Futtassa a következő parancsot a IoT Edge eszközön:
    
    `sudo docker ps`
   
1. Ellenőrizze, hogy a következő tárolók futnak-e:
   
   | Name (Név) | LEMEZKÉP |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.1 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Ha a minimálisan szükséges tárolók nem jelennek meg, ellenőrizze, hogy a IoT Edge központi telepítési jegyzékfájlja az ajánlott beállításokkal van-e igazítva. További információ: [IoT Edge modul üzembe helyezése](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>A modul naplófájljainak vizsgálata hibák esetén
   
1. Futtassa a következő parancsot a IoT Edge eszközön:

   `sudo docker logs azureiotsecurity`
   
1. A részletes naplókhoz adja hozzá a következő környezeti változót a **azureiotsecurity** modul telepítéséhez: `logLevel=Debug`.

## <a name="next-steps"></a>További lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában. 
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
