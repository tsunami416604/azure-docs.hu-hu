---
title: Ismerkedés az Azure Security Center for IoT- val| Microsoft dokumentumok
description: Ismerkedés az Azure Security Center for IoT-funkciók és -szolgáltatások alapvető munkafolyamatának megismerésével.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596421"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Az Azure Security Center for IoT használatának első lépései

Ez a cikk ismerteti az Azure Security Center for IoT szolgáltatás különböző építőelemeit, és ismerteti, hogyan kezdheti el a szolgáltatást két lehetséges telepítési lehetőség használatával.  

## <a name="deployment-options"></a>Üzembe helyezési beállítások

Válassza ki azt a szolgáltatási forgatókönyvet, amely a legjobban megfelel az IoT-eszköz és a környezet követelményeinek. 

### <a name="built-in-deployment"></a>Beépített telepítés
A zökkenőmentes, beépített üzembe helyezési lehetőség használatával az Azure Security Center for IoT gyorsan integrálható az IoT Hubba, és biztonsági elemzést biztosít hat az IoT hub konfigurációjának, az eszközidentitásnak és -kezelésnek, valamint a hub-eszköz kommunikációs mintáinak.

Az [IoT](iot-hub-integration.md) Hub figyelésével és javaslataival rendelkező beépített üzembe helyezést indíthat el. 
    <br>

### <a name="enhanced-deployment"></a>Továbbfejlesztett telepítés
A továbbfejlesztett biztonsági képességek érdekében az Azure Security Center ioT-ügynökökhöz való üzembe helyezése az IoT Hub biztonságának engedélyezése mellett ügynökalapú eseménygyűjtést, elemzést és fenyegetésészlelést biztosít az IoT-eszközökről származó kulcsfontosságú biztonsági adatokról, valamint átfogó biztonsági testtartás-kezelési képességek.

Továbbfejlesztett [üzembe helyezést](security-agents.md) indíthat el egy ügynökalapú átfogó fenyegetésvédelmi és biztonsági testtartás-kezelési megoldással.
   

## <a name="next-steps"></a>További lépések

- Az [Azure Security Center engedélyezése az IoT-hez](quickstart-onboard-iot-hub.md)
- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
- [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
