---
title: Biztonsági ügynök architektúrája
description: Ismerje meg a biztonsági ügynök architektúráját a IoT szolgáltatás Azure Security Center használt ügynökök számára.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310691"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök hivatkozási architektúrája

A IoT Azure Security Center olyan biztonsági ügynökök számára biztosít hivatkozási architektúrát, amelyek a biztonsági adatokat naplózzák, feldolgozzák, összesítik és elküldik a IoT Hub használatával.

A biztonsági ügynökök úgy vannak kialakítva, hogy korlátozott IoT-környezetben működjenek, és az általuk használt erőforrásokhoz képest igen testreszabhatók.

A biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Nyers biztonsági események gyűjtése a mögöttes operációs rendszerből (Linux, Windows). További információ a rendelkezésre álló biztonsági adatgyűjtőről: [Azure Security Center a IoT-ügynök konfigurációjában](how-to-agent-configuration.md).

- A nyers biztonsági események összesítése IoT Hubon keresztül küldött üzenetekben.

- Hitelesítés meglévő eszköz-identitással vagy dedikált modul-identitással. További információért lásd: [biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md) .

- Konfigurálja távolról a **azureiotsecurity** modul Twin használatával. További információ: [Azure Security Center konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md).

A IoT biztonsági ügynökök Azure Security Center nyílt forráskódú projektként vannak kifejlesztve, és elérhetők a GitHubról:

- [Azure Security Center IoT C-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Azure Security Center IoT C#-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Ügynök által támogatott platformok

A IoT-hez készült Azure Security Center a 32 bites és a 64 bites Windows rendszerhez különböző telepítő ügynököket kínál, és a 32 bites és a 64 bites Linux rendszerhez Győződjön meg arról, hogy rendelkezik a megfelelő ügynök-telepítővel az egyes eszközökhöz az alábbi táblázat szerint:

| Architektúra | Linux | Windows |    Részletek|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bitesként  | C#  | C#  ||
| 64bit  | C# vagy C           | C#      | Azt javasoljuk, hogy a C ügynököt olyan eszközökön használja, amelyeken korlátozottabb vagy minimális erőforrású eszköz van.|
|

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan Azure Security Center a IoT biztonsági ügynök architektúrája és az elérhető telepítők.

A IoT-telepítés Azure Security Centerának folytatásához használja a következő cikkeket:

- A [biztonsági ügynök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) megismerése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és központi telepítése
- A IoT [szolgáltatás előfeltételeinek](service-prerequisites.md) áttekintése Azure Security Center
- Megtudhatja, hogyan [engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)
