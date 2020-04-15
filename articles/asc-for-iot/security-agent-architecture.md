---
title: Biztonsági ügynök architektúrája
description: Ismerje meg az Azure Security Center for IoT szolgáltatásban használt ügynökök biztonsági ügynökarchitektúráját.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310691"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök referenciaarchitektúrája

Az Azure Security Center for IoT referenciaarchitektúrát biztosít az IoT Hubon keresztül naplózó, feldolgozó, összesítő és küldő biztonsági ügynökök számára.

A biztonsági ügynökök úgy vannak kialakítva, hogy korlátozott IoT-környezetben működjenek, és nagymértékben testreszabhatók az általuk biztosított értékek tekintetében az általuk felhasznált erőforrásokhoz képest.

A biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Gyűjtse össze a nyers biztonsági eseményeket az alapul szolgáló operációs rendszerből (Linux, Windows). Ha többet szeretne megtudni az elérhető biztonsági adatgyűjtőkről, olvassa el [az Azure Security Center for IoT ügynök konfigurációját.](how-to-agent-configuration.md)

- Nyers biztonsági események összesítése az IoT Hubon keresztül küldött üzenetekben.

- Hitelesítse a hitelesítést meglévő eszközidentitással vagy dedikált modulidentitással. További információ: [Biztonsági ügynök hitelesítési módszerei.](concept-security-agent-authentication-methods.md)

- Konfigurálja távolról az **azureiotsecurity** modul iker használatával. További információ: [Azure Security Center for IoT agent](how-to-agent-configuration.md).

Az Azure Security Center for IoT Security agents nyílt forráskódú projektekként lett kifejlesztve, és a GitHubról érhetők el:

- [Azure Security Center IoT C-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Azure Security Center iot C#-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Ügynök által támogatott platformok

Az Azure Security Center for IoT különböző telepítőügynököket kínál 32 bites és 64 bites Windows hoz, és ugyanezt a 32 bites és 64 bites Linux hoz. Győződjön meg arról, hogy az egyes eszközökhöz a megfelelő ügynöktelepítővel rendelkezik az alábbi táblázat szerint:

| Architektúra | Linux | Windows |    Részletek|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bites  | C#  | C#  ||
| 64 bites  | C# vagy C           | C#      | Azt javasoljuk, hogy a C ügynök eszközök több korlátozott vagy minimális eszköz erőforrásokat.|
|

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Security Center for IoT biztonsági ügynök architektúrájáról és a rendelkezésre álló telepítőkről szerzett tudomást.

Az Azure Security Center iot-telepítéshez való használatának megkezdéséhez használja az alábbi cikkeket:

- A [biztonsági ügynökök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) ismertetése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és üzembe helyezése
- Tekintse át az Azure Security Center t [ioT-szolgáltatás előfeltételeihez](service-prerequisites.md)
- Megtudhatja, hogyan engedélyezheti az [Azure Security Center for IoT-szolgáltatást az IoT Hubban](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról az [Azure Security Center for IoT –GYIK-ből](resources-frequently-asked-questions.md)
