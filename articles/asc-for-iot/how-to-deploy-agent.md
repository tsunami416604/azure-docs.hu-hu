---
title: Biztonsági ügynökök kijelölése és telepítése
description: Ismerje meg, hogyan választhatja ki és telepítheti az Azure Security Center t IoT-biztonsági ügynökökhöz IoT-eszközökön.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311217"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Biztonsági ügynök kiválasztása és üzembe helyezése az IoT-eszközön

Az Azure Security Center for IoT referenciaarchitektúrákat biztosít az IoT-eszközökről adatokat figyelő és adatokat gyűjtő biztonsági ügynökök számára.
További információ: [Security agent reference architecture](security-agent-architecture.md).

Ügynökök fejlesztik a nyílt forráskódú projektek, és rendelkezésre állnak két ízben: <br> [C](https://aka.ms/iot-security-github-c)és [C#](https://aka.ms/iot-security-github-cs).

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Biztonsági ügynökök ízének összehasonlítása
> * Támogatott ügynökplatformok felfedezése
> * Válassza ki a megfelelő ügynök ízt a megoldáshoz

## <a name="understand-security-agent-options"></a>A biztonsági ügynökök beállításainak ismertetése

Minden Azure Security Center for IoT biztonsági ügynök íz ugyanazokat a funkciókat kínálja, és támogatja a hasonló konfigurációs lehetőségeket.

A C-alapú biztonsági ügynök alacsonyabb memóriaigényű, és az ideális választás a kevesebb rendelkezésre álló erőforrásokkal rendelkező eszközök számára.

|     | C-alapú biztonsági ügynök | C#-alapú biztonsági ügynök |
| --- | ----------- | --------- |
| Nyílt forráskódú | Mit [licenc](https://en.wikipedia.org/wiki/MIT_License) alatt érhető el a [GitHubon](https://aka.ms/iot-security-github-cs) | Mit [licenc](https://en.wikipedia.org/wiki/MIT_License) alatt érhető el a [GitHubon](https://aka.ms/iot-security-github-c) |
| Fejlesztési nyelv    | C# | C# |
| Támogatott Windows-platformok? | Nem | Igen |
| A Windows előfeltételei | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Támogatott Linux platformok? | Igen, x64 és x86 | Igen, csak x64 |
| A Linux előfeltételei | libunwind8, libcurl3, uuid-runtime, auditált, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditált, audispd-plugins, sudo, netstat, iptables |
| Lemez alapigénye | 10,5 MB | 90 MB |
| Memórialábnyom (átlagosan) | 5,5 MB | 33 MB |
| [Hitelesítés](concept-security-agent-authentication-methods.md) az IoT Hubnak | Igen | Igen |
| Biztonsági [adatgyűjtés](how-to-agent-configuration.md#supported-security-events) | Igen | Igen |
| Eseményösszesítés | Igen | Igen |
| Távoli konfiguráció a [biztonsági modul ikermodulján keresztül](concept-security-module.md) | Igen | Igen |
|

## <a name="security-agent-installation-guidelines"></a>A biztonsági ügynökök telepítésére vonatkozó irányelvek

**Windows**esetén: A SecurityAgent.ps1 telepítése parancsfájlt rendszergazdai PowerShell-ablakból kell végrehajtani.

**Linux esetén:** A InstallSecurityAgent.sh rendszergazdaként kell futtatni. Javasoljuk, hogy a telepítési parancsot a "sudo" segítségével rögzítse.

## <a name="choose-an-agent-flavor"></a>Válasszon egy ügynök íz

Válaszoljon a következő kérdésekre az IoT-eszközökkel kapcsolatban a megfelelő ügynök kiválasztásához:

- _Windows Server_ vagy _Windows IoT Core rendszert_használ?

    [C#-alapú biztonsági ügynök telepítése Windows rendszerhez](how-to-deploy-windows-cs.md).

- X86 architektúrával rendelkező Linux disztribúciót használ?

    [Telepítsen egy C-alapú biztonsági ügynököt Linuxra.](how-to-deploy-linux-c.md)

- X64 architektúrával rendelkező Linux disztribúciót használ?

    Mindkét ügynök ízek lehet használni. <br>
    [Telepítsen egy C-alapú biztonsági ügynököt Linuxra](how-to-deploy-linux-c.md) és/vagy [telepítsen egy C#-alapú biztonsági ügynököt Linuxra.](how-to-deploy-linux-cs.md)

Mindkét ügynök ízek kínálnak ugyanazokat a funkciókat, és támogatja a hasonló konfigurációs lehetőségeket.
További információ: [Biztonsági ügynök összehasonlítása.](how-to-deploy-agent.md#understand-security-agent-options)

## <a name="supported-platforms"></a>Támogatott platformok

Az alábbi lista tartalmazza az összes jelenleg támogatott platformot.

|Azure Security Center ioT-ügynökhöz |Operációs rendszer |Architektúra |
|--------------|------------|--------------|
|C#|Ubuntu 16.04 |    x64|
|C#|Ubuntu 18.04 |    x64, ARMv7|
|C#|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a konfigurációs beállításokról, folytassa az útmutatóval az ügynök konfigurációjához.
> [!div class="nextstepaction"]
> [Az ügynök konfigurációja útmutató](./how-to-agent-configuration.md)
