---
title: Első lépések a Azure Security Center használata a IoT-hez | Microsoft Docs
description: Ismerkedjen meg a IoT funkcióinak és szolgáltatásainak Azure Security Center alapvető munkafolyamatának megismerésével.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596421"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Az Azure Security Center for IoT használatának első lépései

Ez a cikk a IoT szolgáltatás Azure Security Center különböző építőelemeit ismerteti, és bemutatja, hogyan kezdheti meg a szolgáltatást két lehetséges üzembe helyezési lehetőség használatával.  

## <a name="deployment-options"></a>Telepítési beállítások

Válassza ki azt a szolgáltatási forgatókönyvet, amely a legjobban megfelel a IoT eszköz és a környezet követelményeinek. 

### <a name="built-in-deployment"></a>Beépített üzembe helyezés
A zökkenőmentes, beépített üzembe helyezési lehetőséggel a IoT Azure Security Center gyorsan integrálható a IoT Hubba, és az IoT hub konfigurációjának, az eszköz identitásának és felügyeletének, valamint a hub-Device kommunikációs mintáknak a biztonsági elemzését is biztosíthatja.

IoT Hub monitorozást és javaslatokat tartalmazó [beépített üzembe helyezés](iot-hub-integration.md) elindítása. 
    <br>

### <a name="enhanced-deployment"></a>Továbbfejlesztett üzembe helyezés
A fokozott biztonsági képességek érdekében Azure Security Center IoT-ügynökök számára történő üzembe helyezése mellett a IoT Hub biztonság lehetővé teszi az ügynök-alapú események gyűjtését, elemzését és veszélyforrások észlelését a IoT-eszközökről, valamint az átfogó a biztonsági testhelyzet kezelési képességei.

Megkezdheti az ügynök-alapú átfogó veszélyforrások elleni védelem és a biztonsági helyzetek kezelésére szolgáló felügyeleti megoldás [kibővített üzembe helyezését](security-agents.md) .
   

## <a name="next-steps"></a>További lépések

- [A IoT Azure Security Center](quickstart-onboard-iot-hub.md) engedélyezése
- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások](quickstart-create-custom-alerts.md) konfigurálása
- [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
