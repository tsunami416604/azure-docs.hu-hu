---
title: Understanding Azure Security Center az IoT biztonsági ügynök architektúra előzetes verzió |} A Microsoft Docs
description: Ismerje meg, az ügynökök az Azure Security Center az IoT-szolgáltatáshoz használt biztonsági házirendügynök-architektúra.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e10cd3f60c3b12c6d5115ff34f4cbde2ef19d9fd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862811"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök a referencia-architektúra

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Az Azure Security Center (ASC) az IoT referenciaarchitektúra biztosít a naplózása, feldolgozása, összesített és küldése az IoT hubon keresztül a biztonsági adatok biztonsági ügynökök.

Biztonsági ügynökök tervezték, hogy korlátozott IoT-környezetben működik, és nagymértékben testre szabhatók, adja meg a erőforrásokért képest értékek szempontjából.

Biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Nyers biztonsági események gyűjtésére a mögöttes operációs rendszer (Linux, Windows). Elérhető biztonsági adatgyűjtők kapcsolatos további információkért lásd: [IoT-ügynök konfigurációjának ASC](how-to-agent-configuration.md).

- Nyers biztonsági események összesítése az IoT hubon keresztül küldött üzeneteket.

- Meglévő eszközidentitás, vagy egy dedikált modul identitás hitelesíteni. Lásd: [biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md) további.

- Az használatával távolról konfigurálja a **azureiotsecurity** ikermodul. További tudnivalókért lásd: [az ASC IoT-ügynök konfigurálása](how-to-agent-configuration.md).

IoT-biztonság ügynökök ASC lettek kifejlesztve, nyílt forráskódú projektként, és a Githubról elérhető: 

- [Az ASC a IoT C-alapú ügynök](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Az IoT ASC C#-alapú ügynök](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

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
