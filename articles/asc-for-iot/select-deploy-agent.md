---
title: Válassza ki, és üzembe helyezése egy IoT-ügynök előzetes ASC |} A Microsoft Docs
description: Ismerje meg, hogyan készül ki, és üzembe helyezése ASC IoT biztonsági ügynökök az IoT-eszközökön.
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
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541962"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Válassza ki, és a egy IoT-eszközök a biztonsági ügynök telepítése

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Az IoT ASC referenciaarchitektúrák adatainak figyelésére és gyűjtésére IoT-eszközökről származó biztonsági ügynökök biztosít.

Biztonsági ügynökök nyílt forráskódú projektként lettek kifejlesztve, és két változatban érhetők el: <br> [C](https://aka.ms/iot-security-github-c), és [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>IoT-ügynökök az ASC által támogatott platformok

Az alábbi lista tartalmazza az összes jelenleg támogatott platformokkal.  

|ASC IoT-ügynök |Operációs rendszer |Architektúra |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Melyik íz használjam?

Vegye figyelembe az alábbi kérdések az IoT-eszközökről vonatkozóan:

- Használ _Windows Server_ vagy _Windows IoT Core_? 

    Használja [IoT telepíteni a Windows ASC a C#-alapú biztonsági ügynök](tutorial-deploy-windows-cs.md).

- Használ egy Linux-disztribúció x86 az architektúra? 

    Használat [IoT telepítés Linux rendszeren C-alapú biztonsági-ügynökkel rendelkező ASC](tutorial-deploy-linux-c.md).
- Használ egy Linux-disztribúció x64 az architektúra?

    Használhatja a két változatban érhetők el. <br>
    [ASC IoT telepítés Linux rendszeren C-alapú biztonsági-ügynökkel rendelkező](tutorial-deploy-linux-c.md) és/vagy [IoT telepítés Linux rendszeren az ASC a C#-alapú biztonsági ügynök](tutorial-deploy-linux-cs.md).

Két változatban érhetők el ugyanazokat a funkciókat rendelkezik, és támogatja a hasonló konfigurációs beállításokat. A C-alapú biztonsági ügynök tartozik egy alacsonyabb memóriaigényét.


## <a name="next-steps"></a>További lépések
- [Áttekintés](overview.md)
- [Biztonsági ügynökök](security-agent-architecture.md)
- [Biztonsági ügynök hitelesítési módszerek](concept-security-agent-authentication-methods.md)
- [ASC IOT – gyakori kérdések](resources-frequently-asked-questions.md)