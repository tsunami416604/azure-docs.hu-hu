---
title: Azure Security Center üzembe helyezése IoT Edge modulhoz (előzetes verzió) | Microsoft Docs
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4e568d2322088d9f6f6b4f9ad6e4b3cd98f25a47
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376063"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Biztonsági modul üzembe helyezése a IoT Edge eszközön

> [!IMPORTANT]
> A IoT IoT Edge-eszközök támogatásának Azure Security Center jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A **IoT modul Azure Security Center** átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A biztonsági modul az operációs rendszer és a tárolórendszer nyers biztonsági adatainak gyűjtését, összesítését és elemzését végezheti el a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.
További információ: [IoT Edge biztonsági modulja](security-edge-architecture.md).

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy biztonsági modult a IoT Edge eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul telepítése

A következő lépésekkel telepítheti a IoT Edge IoT biztonsági moduljának Azure Security Center.

### <a name="prerequisites"></a>Előfeltételek

- A IoT Hub ellenőrizze, hogy az eszköz IoT Edge- [eszközként](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)van-e regisztrálva.

- A IoT Edge modulhoz Azure Security Center a [naplózási keretrendszert](https://linux.die.net/man/8/auditd) telepíteni kell a IoT Edge eszközön.

    - A keretrendszer telepítéséhez futtassa a következő parancsot a IoT Edge eszközön:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - A naplózott ellenőrzés aktív a következő parancs futtatásával:
   
      `sudo systemctl status auditd`
      
        A várt válasz `active (running)`:. 

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés Azure Portal használatával

1. A Azure Portal nyissa meg a **Marketplace**-t.

1. Válassza a **eszközök internetes hálózata**lehetőséget, majd keresse meg a **IoT Azure Security Center** , és válassza ki.

   ![IoT Azure Security Center kiválasztása](media/howto/edge-onboarding-8.png)

1. A telepítés konfigurálásához kattintson a **Létrehozás** gombra. 

1. Válassza ki a IoT Hub Azure- **előfizetését** , majd válassza ki a **IoT hub**.<br>Válassza a **telepítés egy eszközre** lehetőséget egyetlen eszköz kiválasztásához, vagy válassza a nagy **méretű üzembe helyezés** lehetőséget a több eszköz megcélzásához, majd kattintson a **Létrehozás**gombra. További információ a nagy léptékű üzembe helyezésről: a [központi](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)telepítés. 

    >[!Note] 
    >Ha a **központi telepítés méretezése**lehetőséget választotta, adja hozzá az eszköz nevét és részleteit, mielőtt továbblép a **modulok hozzáadása** lapra az alábbi utasításokban.     

A IoT számára három lépésben hozhat létre IoT Edge központi Azure Security Center telepítést. A következő szakaszok egyenként végig. 

#### <a name="step-1-add-modules"></a>1\. lépés: Modulok hozzáadása

1. A **modulok hozzáadása** lap **üzembe helyezési modulok** területén kattintson a **AzureSecurityCenterforIoT**elemre. 
   
1. Módosítsa a **nevet** a **azureiotsecurity**értékre.
1. Módosítsa a **rendszerkép URI-ját** a **MCR.microsoft.com/ascforiot/azureiotsecurity:0.0.3**értékre.
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
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Kattintson a **Save** (Mentés) gombra.
1. Görgessen a lap aljára, majd válassza a **speciális Edge-futtatókörnyezet beállításainak konfigurálása**lehetőséget.
   
   
1. Módosítsa a **képet** az **Edge Hub** alatt a **MCR.microsoft.com/ascforiot/edgehub:1.0.9-Preview**értékre.

   >[!Note]
   > A IoT modulhoz Azure Security Center az SDK 1,20-es verzióján alapuló IoT Edge hub villás verzióját kell megadnia.
   > IoT Edge hub-lemezkép módosításával arra utasítja a IoT Edge eszközt, hogy cserélje le a legújabb stabil kiadást az IoT Edge hub elágazó verziójára, amelyet a IoT Edge szolgáltatás nem támogat hivatalosan.

1. A **létrehozási beállítások** ellenőrzése a következőre van beállítva: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Kattintson a **Save** (Mentés) gombra.
   
1. Kattintson a **Tovább** gombra.

#### <a name="step-2-specify-routes"></a>2\. lépés: Útvonalak megadása 

1. Az **útvonalak meghatározása** lapon győződjön meg arról, hogy van olyan útvonala (explicit vagy implicit), amely továbbítja az üzeneteket a **azureiotsecurity** modulból a **$upstreamra**. 
1. Kattintson a **Tovább** gombra.

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

#### <a name="step-3-review-deployment"></a>3\. lépés: Üzembe helyezés áttekintése

- A központi telepítés **áttekintése** lapon tekintse át a központi telepítési adatokat, majd a telepítés befejezéséhez válassza a **Submit (Küldés** ) lehetőséget.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémába ütközik, a tároló naplói a legjobb módszer a IoT Edge biztonsági modul eszközének állapotának megismerésére. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve vannak-e, és megfelelően működnek-e.

1. Futtassa a következő parancsot a IoT Edge eszközön:
    
     `sudo docker ps`
   
1. Ellenőrizze, hogy a következő tárolók futnak-e:
   
   | Name (Név) | LEMEZKÉP |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Ha a minimálisan szükséges tárolók nem jelennek meg, ellenőrizze, hogy a IoT Edge központi telepítési jegyzékfájlja az ajánlott beállításokkal van-e igazítva. További információ: [IoT Edge modul üzembe helyezése](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>A modul naplófájljainak vizsgálata hibák esetén
   
1. Futtassa a következő parancsot a IoT Edge eszközön:

   `sudo docker logs azureiotsecurity`
   
1. A részletes naplókhoz adja hozzá a következő környezeti változót a **azureiotsecurity** modul telepítéséhez: `logLevel=Debug`.

## <a name="next-steps"></a>További lépések

A konfigurációs beállításokkal kapcsolatos további információkért folytassa a modul konfigurálásának útmutatójában. 
> [!div class="nextstepaction"]
> [Útmutató a modul konfigurálásához](./how-to-agent-configuration.md)
