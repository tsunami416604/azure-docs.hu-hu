---
title: Biztonsági ügynök architektúrája
description: A biztonsági ügynök architektúrájának ismertetése az Azure Defender for IoT szolgáltatásban használt ügynökökkel kapcsolatban.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 64d90b4d9dc3efbe877230bbc20780b1c4f2d213
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939753"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök hivatkozási architektúrája

A IoT készült Azure Defender olyan biztonsági ügynökök számára biztosít hivatkozási architektúrát, amelyek IoT Hubon keresztül naplózzák, feldolgozzák, összesítik és elküldik a biztonsági adatokat.

A biztonsági ügynökök úgy vannak kialakítva, hogy korlátozott IoT-környezetben működjenek, és az általuk használt erőforrásokhoz képest igen testreszabhatók.

A biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Nyers biztonsági események gyűjtése a mögöttes operációs rendszerből (Linux, Windows). További információ a rendelkezésre álló biztonsági adatgyűjtőről: [Defender for IoT Agent Configuration](how-to-agent-configuration.md).

- A nyers biztonsági események összesítése IoT Hubon keresztül küldött üzenetekben.

- Hitelesítés meglévő eszköz-identitással vagy dedikált modul-identitással. További információért lásd: [biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md) .

- Konfigurálja távolról a **azureiotsecurity** modul Twin használatával. További információ: [Defender konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md).

A IoT biztonsági ügynökök védelmezője nyílt forráskódú projektként van kifejlesztve, és elérhető a GitHubról:

- [Defender IoT C-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender IoT C#-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Ügynök által támogatott platformok

A Defender for IoT különböző telepítő ügynököket kínál a 32 bites és 64 bites Windows rendszerekhez, és ugyanezt a 32 bites és 64 bites Linux rendszeren. Győződjön meg arról, hogy rendelkezik a megfelelő ügynök-telepítővel az egyes eszközökhöz az alábbi táblázat szerint:

| Architektúra | Linux | Windows |    Részletek|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| bitesként  | C#  | C#  ||
| 64bit  | C# vagy C           | C#      | Azt javasoljuk, hogy a C ügynököt olyan eszközökön használja, amelyeken korlátozottabb vagy minimális erőforrású eszköz van.|
|

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a IoT biztonsági ügynök architektúrájának és az elérhető telepítőknek a Defender szolgáltatását.

A Defender IoT-telepítéssel való használatának folytatásához használja a következő cikkeket:

- A [biztonsági ügynök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) megismerése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és központi telepítése
- A Defender IoT [szolgáltatás előfeltételeinek](service-prerequisites.md) áttekintése
- Ismerje meg, hogyan [engedélyezheti a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
