---
title: ASC megismerheti az IoT biztonsági ikermodulokkal előzetes verzió |} A Microsoft Docs
description: Ismerje meg a biztonsági ikermodulokkal és azok hogyan használhatók az ASC IOT fogalmát.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541842"
---
# <a name="security-module"></a>Biztonsági modul

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan használja az IOT ASC ikereszközök és a modulok. 

## <a name="device-twins"></a>Ikereszközök

IoT-megoldások az Azure-ban fejlesztett ikereszközök Eszközkezelés és a folyamatok automatizálásával kulcsfontosságú szerepet játszanak.  

Az IoT ASC kínál szolgáltatás teljesen integrálható a meglévő IoT device-kezelési platform, az, hogy az eszköz biztonsági állapotát kezelése lehetővé teszi az eszközök vezérlési meglévő képességeit használja gyártmány és. Integráció érhető el az IoT Hub végrehajtását ikereszköz mechanizmust.  

További információ fogalma [eszköz](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) ikerállapotának az Azure IoT hubon. 

## <a name="security-module-twins"></a>Biztonsági ikermodulokkal

ASC IOT egy biztonsági ikermodul a szolgáltatás minden egyes eszközhöz fenntart. Biztonsági ikermodulja tárolja a megoldás megfelelő az eszköz biztonsági egyes adott eszközhöz tartozó összes információt. A kijelölt biztonsági ikermodul biztonságosabb kommunikációhoz és a frissítések és karbantartás kevesebb erőforrást igénylő engedélyezése biztonsági eszköztulajdonságok karbantartása.  

Lásd: [létrehozás biztonsági ikermodul](quickstart-create-security-twin.md) és [biztonsági ügynökök konfigurálása](concept-agent-configuration.md) megtudhatja, hogyan hozhat létre, testre szabhatja, és állítsa be az ikereszköz. Lásd: [ismertetése ikermodulokkal](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) további információ az IoT Hub ikermodulokkal fogalma. 
 

## <a name="see-also"></a>Lásd még
- [ASC for IoT-előzetes verzió](overview.md)
- [Biztonsági ügynökök telepítése](select-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md)