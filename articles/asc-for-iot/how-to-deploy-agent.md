---
title: Válassza ki, és üzembe helyezése az Azure Security Center IoT ügynök előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan készül ki, és üzembe helyezése az Azure Security Center IoT biztonsági ügynökök az IoT-eszközökön.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862420"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Válassza ki, és a egy IoT-eszközök a biztonsági ügynök telepítése

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Security Center (ASC) az IoT biztonsági ügynökök, amelyek figyelése és IoT-eszközök adatainak összegyűjtése referenciaarchitektúrák biztosít.
Lásd: [biztonsági ügynök referenciaarchitektúra](security-agent-architecture.md) további.

Ügynökök nyílt forráskódú projektként lettek kifejlesztve, és két változatban érhetők el: <br> [C](https://aka.ms/iot-security-github-c), és [ C# ](https://aka.ms/iot-security-github-cs).

Ebben a cikkben az alábbiakkal ismerkedhet meg: 
> [!div class="checklist"]
> * Hasonlítsa össze a biztonsági ügynök változatban érhetők el
> * Fedezze fel az támogatott ügynök platformok
> * Válassza ki a megfelelő ügynök íz a megoldáshoz

## <a name="understand-security-agent-options"></a>Biztonsági ügynök lehetőségek ismertetése

Az IoT biztonsági ügynök íz minden ASC ugyanazokat a funkciókat kínál, és támogatja a hasonlóan konfigurációknak. 

A C-alapú biztonsági ügynök tartozik egy alacsonyabb memóriaigényét, és az ideális választás az eszközök kevesebb rendelkezésre álló erőforrások. 

|     | C-alapú biztonsági ügynök | C#-alapú biztonsági ügynök |
| --- | ----------- | --------- |
| Nyílt forráskód | A rendelkezésre álló [MIT licenccel](https://en.wikipedia.org/wiki/MIT_License) a [Github](https://aka.ms/iot-security-github-cs) | A rendelkezésre álló [MIT licenccel](https://en.wikipedia.org/wiki/MIT_License) a [Github](https://aka.ms/iot-security-github-c) |
| Fejlesztői nyelvek    | C | C# |
| A támogatott Windows platformok? | Nem | Igen |
| Windows-Előfeltételek | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Támogatott Linux platformon? | Igen, x64 és x86 | Igen, csak x64 |
| A Linux előfeltételei | libunwind8, libcurl3, uuid-futtatókörnyezet, auditd, audispd – beépülő modulok | libunwind8, libcurl3, uuid-futtatókörnyezet, auditd, audispd – beépülő modulok, sudo, netstat, engedélyezze az iptables |
| Lemez erőforrásigényét | 10,5 MB | 90MB |
| Memóriaigénye (az átlagos) | 5.5-ÖS MB | 33MB |
| [Hitelesítési](concept-security-agent-authentication-methods.md) az IoT hubhoz | Igen | Igen |
| Biztonsági adatok [gyűjtemény](how-to-agent-configuration.md#supported-security-events) | Igen | Igen |
| Események összesítése | Igen | Igen |
| Távoli konfigurációja keresztül [biztonsági ikermodul](concept-security-module.md) | Igen | Igen |


## <a name="choose-an-agent-flavor"></a>Egy ügynök íz kiválasztása 

Válaszoljon az alábbi kérdések az IoT-eszközökről, válassza ki a megfelelő ügynök kapcsolatban:

- Használ _Windows Server_ vagy _Windows IoT Core_? 

    [Üzembe helyezése egy C#-security-ügynök a Windows-alapú](how-to-deploy-windows-cs.md).

- Használ egy Linux-disztribúció x86 az architektúra? 

    [Egy biztonsági C-alapú Linux-ügynök telepítése](how-to-deploy-linux-c.md).

- Használ egy Linux-disztribúció x64 az architektúra?

    Mindkét ügynök íz is használhatja. <br>
    [Egy biztonsági C-alapú Linux-ügynök telepítése](how-to-deploy-linux-c.md) és/vagy [üzembe helyezés egy C#-alapú Linux-ügynök biztonsági](how-to-deploy-linux-cs.md).

Mindkét ügynök változatban érhetők el ugyanazokat a funkciókat kínál, és támogatja a hasonlóan konfigurációknak.
Lásd: [biztonsági ügynök összehasonlító](how-to-deploy-agent.md#understand-security-agent-options) további.

## <a name="supported-platforms"></a>Támogatott platformok

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

## <a name="next-steps"></a>További lépések

További konfigurációs lehetőségekkel kapcsolatos, továbbra is az ügynök konfigurációs útmutatója. 
> [!div class="nextstepaction"]
> [Az ügynök konfigurációs módjáról útmutató](./how-to-agent-configuration.md)
