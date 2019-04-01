---
title: IoT Edge-modul üzembe helyezése az Azure Security Center |} A Microsoft Docs
description: További tudnivalók az Azure Security Center az IoT Edge-ben az IoT biztonsági ügynök telepítése.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 2a201fe649d52ad9604c7ac6675b26d60e7f2dd1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754762"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Az IoT Edge-eszközön a biztonsági modul üzembe helyezése

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Security Center (ASC) az IoT **azureiotsecurity** modul átfogó biztonsági megoldást kínál az IoT Edge-eszköz.
Biztonsági modult, összesíti az adatokat gyűjt és elemez nyers biztonsági az operációs rendszer és a tároló rendszerből végrehajtható biztonsági ajánlásokat és riasztások.
További tudnivalókért lásd: [az IoT Edge biztonsági modul](security-edge-architecture.md).

Ebben az útmutatóban megismerheti, hogyan helyezhet üzembe egy biztonsági modul az IoT Edge-eszközön.

## <a name="deploy-security-module"></a>Biztonsági modul üzembe helyezése

Használja az alábbi lépéseket egy ASC IoT biztonsági modul telepítéséhez az IoT Edge-hez.

### <a name="prerequisites"></a>Előfeltételek

1. Győződjön meg arról, hogy az eszköze [IoT Edge-eszköz regisztrálva](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. Az IoT Edge-modul az ASC igényel a [AuditD keretrendszer](https://linux.die.net/man/8/auditd) az Edge-eszközön telepíteni.

   Telepítse a keretrendszert az Edge-eszközön a következő parancs futtatásával:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Üzembe helyezés az Azure portal használatával

1. Nyissa meg **Marketplace** az Azure Portalon.

1. Keresse meg **az azure iot-biztonság** , majd kattintson a **Azure IoT-biztonság**.

   ![](media/howto/edge_onboarding_7.png)

1. Kattintson a **Create** (Létrehozás) gombra.

1. Válassza ki a **előfizetés**, **az IoT Hub** és **IoT Edge-eszköz neve**, majd kattintson a **létrehozás**.

1. Kattintson a **tovább** kétszer a **áttekintése telepítési**.

1. Győződjön meg arról, hogy **edgeHub.settings.createOptions** konfigurációja a következő:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Kattintson a **küldés** a telepítés befejezéséhez.


## <a name="next-steps"></a>További lépések

További konfigurációs lehetőségekkel kapcsolatos, továbbra is a modulkonfiguráció útmutatója. 
> [!div class="nextstepaction"]
> [A modul konfigurációs módjáról útmutató](./how-to-agent-configuration.md)
