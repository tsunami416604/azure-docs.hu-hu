---
title: Azure Security Center kiválasztása és telepítése a IoT-ügynökhöz | Microsoft Docs
description: Ismerje meg, hogyan válassza ki és telepítse Azure Security Center IoT biztonsági ügynökök számára a IoT-eszközökön.
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
ms.openlocfilehash: 18031400155b92f243877ae8c8d7a56e1d5295a0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933647"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Biztonsági ügynök kiválasztása és üzembe helyezése a IoT-eszközön

A IoT Azure Security Center a IoT-eszközökről származó adatokat figyelő és gyűjtő biztonsági ügynökök számára biztosít hivatkozási architektúrákat.
További információt a [biztonsági ügynök hivatkozási architektúrája](security-agent-architecture.md)című témakörben talál.

Az ügynökök nyílt forráskódú projektekként lettek kifejlesztve, és két változatban érhetők el: <br> [C](https://aka.ms/iot-security-github-c)és [C#](https://aka.ms/iot-security-github-cs).

Ebben a cikkben az alábbiakkal ismerkedhet meg: 
> [!div class="checklist"]
> * Biztonsági ügynöki ízek összehasonlítása
> * Támogatott ügynökök platformjának felderítése
> * Válassza ki a megoldás megfelelő ügynökének ízét

## <a name="understand-security-agent-options"></a>A biztonsági ügynök beállításainak ismertetése

A IoT biztonsági ügynök íz minden Azure Security Center ugyanazokat a funkciókat kínálja, és támogatja a hasonló konfigurációs beállításokat. 

A C-alapú biztonsági ügynök alacsonyabb memória-lábnyomot tartalmaz, és ideális választás a kevesebb rendelkezésre álló erőforrásokkal rendelkező eszközök számára. 

|     | C-alapú biztonsági ügynök | C#-alapú biztonsági ügynök |
| --- | ----------- | --------- |
| Nyílt forráskódú | Az [mit licenc](https://en.wikipedia.org/wiki/MIT_License) alatt érhető el a [githubban](https://aka.ms/iot-security-github-cs) | Az [mit licenc](https://en.wikipedia.org/wiki/MIT_License) alatt érhető el a [githubban](https://aka.ms/iot-security-github-c) |
| Fejlesztői nyelv    | C | C# |
| Támogatott Windows-platformok? | Nem | Igen |
| Windows előfeltételek | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Támogatott linuxos platformok? | Igen, x64 és x86 | Igen, csak x64 |
| A Linux előfeltételei | libunwind8, libcurl3, UUID-Runtime, auditált, audispd-plugins | libunwind8, libcurl3, UUID-Runtime, auditált, audispd-plugins, sudo, netstat, iptables |
| Lemez lábnyoma | 10,5 MB | 90 MB |
| Memória lábnyoma (átlagos) | 5,5 MB | 33 MB |
| [Hitelesítés](concept-security-agent-authentication-methods.md) IoT hub | Igen | Igen |
| Biztonsági adatgyűjtés [](how-to-agent-configuration.md#supported-security-events) | Igen | Igen |
| Eseményösszesítés | Igen | Igen |
| Távoli konfiguráció a [biztonsági modul Twin](concept-security-module.md) szolgáltatásán keresztül | Igen | Igen |
|

## <a name="security-agent-installation-guidelines"></a>Biztonsági ügynök telepítési útmutatója

**Windows**esetén: Az install SecurityAgent. ps1 parancsfájlt rendszergazdai PowerShell-ablakból kell végrehajtani. 

**Linux**esetén: A InstallSecurityAgent.sh rendszergazdaként kell futnia. A telepítési parancs "sudo" előtaggal való előjavítását javasoljuk.


## <a name="choose-an-agent-flavor"></a>Ügynök íz kiválasztása 

Válaszoljon a IoT-eszközökkel kapcsolatos alábbi kérdésekre a megfelelő ügynök kiválasztásához:

- A _Windows Servert_ vagy a _Windows IoT Core_-t használja? 

    [Windows rendszerű biztonsági ügynök üzembe helyezése. C#](how-to-deploy-windows-cs.md)

- Linux-disztribúciót használ x86 architektúrával? 

    [A Linux rendszerhez készült C-alapú biztonsági ügynök üzembe helyezése](how-to-deploy-linux-c.md).

- Linux-disztribúciót használ x64 architektúrával?

    Mindkét ügynök-aroma használható. <br>
    [Helyezzen üzembe egy C-alapú biztonsági ügynököt Linuxra](how-to-deploy-linux-c.md) és/vagy [telepítsen egy C#Linux-alapú biztonsági ügynököt](how-to-deploy-linux-cs.md).

Mindkét ügynök-íz ugyanazokat a funkciókat kínálja, és támogatja a hasonló konfigurációs beállításokat.
További információért lásd a [biztonsági ügynök összehasonlítását](how-to-deploy-agent.md#understand-security-agent-options) ismertető témakört.

## <a name="supported-platforms"></a>Támogatott platformok

Az alábbi lista tartalmazza az összes jelenleg támogatott platformot.

|Azure Security Center a IoT-ügynökhöz |Operációs rendszer |Architektúra |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, Build 17763    |x64|
|

## <a name="next-steps"></a>További lépések

A konfigurációs beállításokkal kapcsolatos további tudnivalókért folytassa az ügynök konfigurációjának útmutatója című témakört. 
> [!div class="nextstepaction"]
> [Útmutató az ügynök konfigurálásához](./how-to-agent-configuration.md)
