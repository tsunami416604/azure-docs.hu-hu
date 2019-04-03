---
title: IoT Edge-modul üzembe helyezése az Azure Security Center |} A Microsoft Docs
description: Ismerje meg az Azure Security Center az IoT biztonsági IoT Edge-ügynök telepítése.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 40f771e97b61c28229b0eff29191247ef2fef695
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862845"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Az IoT Edge-eszközön a biztonsági modul üzembe helyezése

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Az Azure Security Center (ASC) az IoT** modul átfogó biztonsági megoldást kínál az IoT Edge-eszköz.
Biztonsági modult, összesíti, adatokat gyűjt és elemez nyers biztonsági az operációs rendszer és a tároló rendszerből végrehajtható biztonsági ajánlásokat és riasztások.
További tudnivalókért lásd: [az IoT Edge biztonsági modul](security-edge-architecture.md).

Ebben az útmutatóban megismerheti, hogyan helyezhet üzembe egy biztonsági modul az IoT Edge-eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul üzembe helyezése

Használja az alábbi lépéseket egy ASC IoT biztonsági modul telepítéséhez az IoT Edge-hez.

### <a name="prerequisites"></a>Előfeltételek

- Az IoT hub, ellenőrizze, hogy az eszköz [IoT Edge-eszköz regisztrálva](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Az IoT Edge-modul ASC igényel [AuditD keretrendszer](https://linux.die.net/man/8/auditd) az IoT Edge-eszközön telepítve van.

    - Telepítse a keretrendszert az IoT Edge-eszközön a következő parancs futtatásával:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Győződjön meg róla AuditD aktív a következő parancs futtatásával:
   
      `sudo systemctl status auditd`
      
        A várt válasz `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés az Azure portal használatával

1. Az Azure Portalon nyissa meg a **Marketplace**.

1. Válassza ki **IOT-**, majd keresse meg az **Azure Security Center az IoT** , és jelölje ki.

   ![Válassza ki az Azure Security Center az IoT](media/howto/edge-onboarding-8.png)

1. Kattintson a **létrehozás** a központi telepítésnek a konfigurálásához. 

1. Válassza ki az Azure **előfizetés** az IoT hub, majd válassza ki a **az IoT Hub**.<br>Válassza ki **üzembe helyezés az eszköz** célként egyetlen eszközt, vagy válasszon **üzembe helyezése ipari méretekben** célozhat meg több eszközön, és kattintson **létrehozás**. Nagy mennyiségű központi telepítésével kapcsolatos további információkért lásd: [üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Ha a kiválasztott **üzembe helyezése ipari méretekben**, adja meg az eszköz nevét és részleteit, mielőtt a **modulok hozzáadása** lapján az alábbi utasításokat.     

Hozzon létre egy IoT Edge üzembe helyezése az Azure Security Center az IoT három lépésből áll. A következő szakaszok egyenként végig. 

#### <a name="step-1-add-modules"></a>1. lépés: Modulok hozzáadása

1. Az a **modulok hozzáadása** lapon **üzembe helyezési modulok** területen kattintson a **AzureSecurityCenterforIoT**. 
   
1. Módosítsa a **neve** való **azureiotsecurity**.
1. Módosítsa a nevet **rendszerkép URI** való **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.1**
      
1. Ellenőrizze, hogy **Set ikermodul kívánt tulajdonságai** van kiválasztva, és módosítsa a konfigurációs objektum:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Kattintson a **Save** (Mentés) gombra.
1. Alsó részén a lapra, és görgessen **speciális Edge-futtatókörnyezet-beállítások konfigurálása**.
   
  >[!Note]
  > Tegye **nem** IoT Edge hub az AMQP-kommunikáció letiltásához.
  > IoT-modul az Azure Security Center és az IoT Edge Hub az AMQP kommunikációs igényel.
   
1. Módosítsa a **kép** alatt **Hub él** való **mcr.microsoft.com/ascforiot/edgehub:1.05-preview**.
      
1. Győződjön meg arról **beállítások létrehozása** értékre van állítva: 
         
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
   
1. Kattintson a **tovább**.

#### <a name="step-2-specify-routes"></a>2. lépés: Útvonalak megadása 

1. Az a **útvonalak megadása** lapra, és állítsa a **ASCForIoTToIoTHub** átirányítása **"származó/üzenetek/modulok/azureiotsecurity/\* be felső $"**, kattintson **Tovább**.

   ![Útvonalak megadása](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>3. lépés: Üzembe helyezés áttekintése

1. Az a **tekintse át a központi telepítési** fülre, tekintse át a telepítési adatokat, majd válassza ki **küldés** a telepítés befejezéséhez.

## <a name="diagnostic-steps"></a>Diagnosztikai lépések

Ha problémát tapasztal, a tároló naplóit a legjobb módszer az IoT Edge module-eszköz állapotával kapcsolatos további. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ellenőrizze, hogy a szükséges tárolók telepítve és az elvárt módon működik

1. Futtassa a következő parancsot az IoT Edge-eszközön:
    
     `sudo docker ps`
   
1. Győződjön meg arról, hogy futnak-e a következő tárolókban:
   
   | Name (Név) | IMAGE |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.1 |
   | edgeHub | asotcontainerregistry.azurecr.io/edgehub:1.04-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Ha a szükséges minimális tárolók nem találhatók, ellenőrizze, ha az IoT Edge manifest nasazení igazítva van-e az ajánlott beállításokkal. További információkért lásd: [üzembe helyezése IoT Edge-modul](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>A modul naplók hibák vizsgálata
   
1. Futtassa a következő parancsot az IoT Edge-eszközön:

   `sudo docker logs azureiotsecurity`
   
1. További részletes naplók, adja hozzá a következő környezeti változó **azureiotsecurity** üzembe helyezett házirendmodul: `logLevel=Debug`.

## <a name="next-steps"></a>További lépések

További konfigurációs lehetőségekkel kapcsolatos, továbbra is a modulkonfiguráció útmutatója. 
> [!div class="nextstepaction"]
> [A modul konfigurációs útmutató](./how-to-agent-configuration.md)
