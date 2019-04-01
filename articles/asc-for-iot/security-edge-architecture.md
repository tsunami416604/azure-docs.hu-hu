---
title: Az Azure IoT biztonsági modulhoz az IoT Edge-hez a Security Center megismerése |} A Microsoft Docs
description: Ismerje meg, architektúráját és funkcióit az Azure Security Center IoT biztonsági modulhoz az IoT Edge-hez.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 11eadc70f6da20fdc9b837dfad45705278dd2d27
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756701"
---
# <a name="azure-iot-edge-security-module"></a>Az Azure IoT Edge biztonsági modul

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem ajánlott éles worklo§1ads. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) kezeléséhez, és végezze el a peremhálózaton üzleti munkafolyamatok hatékony funkciókat biztosít.
A kulcsfontosságú jellemzője, mely az IoT Edge IoT-környezetek győződjön meg arról, hogy különösen vonzó rosszindulatú.

Az Azure Security Center (ASC) IoT biztonsági modul egy átfogó biztonsági megoldást kínál az IoT Edge eszközök.
IoT-modul ASC, összesíti az adatokat gyűjt és elemez nyers biztonsági az operációs rendszer és a rendszer a végrehajtható biztonsági ajánlásokat és riasztások.

Hasonlóan az ASC-hez készült IoT-eszközök IoT-biztonsági ügynökök az IoT Edge-modul az ASC nagymértékben testre szabhatók az ikermodul keresztül.
Lásd: [konfigurálása az ügynök](how-to-agent-configuration.md) további.

ASC IoT biztonsági modulhoz az IoT Edge-hez a következő funkciókat kínálja:

- Nyers biztonsági eseményt gyűjti össze az alapul szolgáló operációs rendszer (Linux), és az IoT Edge-tároló rendszerek.
  
  Lásd: [ASC IoT-ügynök konfigurációjának](how-to-agent-configuration.md) elérhető biztonsági adatgyűjtők tájékozódhat.

- IoT Edge üzembe helyezési jegyzékek elemzése.

- Nyers biztonsági események összegzi az keresztül küldött üzenetek [IoT Edge hubot](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Távolítsa el a biztonsági ikermodul használatával konfigurációját.

  Lásd: [az ASC IoT-ügynök konfigurálása](how-to-agent-configuration.md) további.

ASC IoT biztonsági modulhoz az IoT Edge alatt IoT Edge egy védett módban fut.
A modul az operációs rendszer monitorozása és egyéb IoT Edge-modulok engedélyezése védett módban van szükség.

## <a name="agent-supported-platforms"></a>Az ügynök támogatott platformok

ASC IoT biztonsági modulhoz az IoT Edge jelenleg csak Linux rendszeren érhető el.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a architektúráját és funkcióit az ASC IoT biztonsági modulhoz az IoT Edge-hez.

Továbbra is az IoT-környezet ASC használatának első lépései, használja a következő cikkeket:

- Üzembe helyezése [biztonsági modul az IoT Edge-hez](how-to-deploy-edge.md)
- Ismerje meg, hogyan [a biztonsági modul konfigurálása](how-to-agent-configuration.md)
- Tekintse át az ASC IoT [szolgáltatás előfeltételei](service-prerequisites.md)
- Ismerje meg, hogyan [ASC engedélyezése az IoT hub IoT-szolgáltatás](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatás a [ASC IoT – GYIK](resources-frequently-asked-questions.md)