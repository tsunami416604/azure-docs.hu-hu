---
title: Az IoT biztonsági ügynök architektúra előzetes ASC ismertetése |} A Microsoft Docs
description: Ismerje meg, az ügynökök az IoT-szolgáltatást az ASC használt biztonsági ügynök architektúra.
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
ms.openlocfilehash: 7de4307f7eef95a637dc828ac1738bd0ed39c7c7
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578458"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök a referencia-architektúra

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC IOT referenciaarchitektúra naplózni, feldolgozásához, összesített és küldése az IoT hubon keresztül a biztonsági adatok biztonsági ügynökök biztosít.

Biztonsági ügynökök tervezték, hogy korlátozott IoT-környezetben működik, és nagymértékben testre szabhatók, adja meg a erőforrásokért képest értékek szempontjából.

Biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Nyers biztonsági események gyűjtésére a mögöttes operációs rendszer (Linux, Windows). Elérhető biztonsági adatgyűjtők kapcsolatos további információkért lásd: [IoT-ügynök konfigurációjának ASC](how-to-agent-configuration.md).

- Nyers biztonsági események összesítése az IoT hubon keresztül küldött üzeneteket.

- Meglévő eszközidentitás, vagy egy dedikált modul identitás hitelesíteni. Lásd: [biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md) további.

- Az használatával távolról konfigurálja a **azureiotsecurity** ikermodul. További tudnivalókért lásd: [az ASC IoT-ügynök konfigurálása](how-to-agent-configuration.md).

IoT-biztonság ügynökök ASC lettek kifejlesztve, nyílt forráskódú projektként, és a Githubról elérhető: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>Az ügynök támogatott platformok

A 32 bites és 64 bites Windows installer különböző ügynökök és 32 bites és 64 bites Linux esetében azonos ASC az IoT érhetők el. Győződjön meg arról, hogy a megfelelő ügynököt telepítő minden eszközt, az alábbi táblázat szerint:

| 32 vagy 64 bites | Linux | Windows |    Részletek|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bites  | C  | C#  ||
| 64 bites  | C#vagy C           | C#      | Használja a C-ügynököt tartalmazó minimális erőforrás-eszközök|

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az ASC IoT biztonsági házirendügynök-architektúra és a rendelkezésre álló telepítők.

Továbbra is az IoT-környezet ASC használatának első lépései, használja a következő cikkeket:

- Megismerheti [biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md)
- Válassza ki és helyezhet üzembe egy [security-ügynök](how-to-deploy-agent.md)
- Tekintse át az ASC IoT [szolgáltatás előfeltételei](service-prerequisites.md)
- Ismerje meg, hogyan [ASC engedélyezése az IoT hub IoT-szolgáltatás](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatás a [ASC IoT – GYIK](resources-frequently-asked-questions.md)