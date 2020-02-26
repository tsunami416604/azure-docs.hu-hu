---
title: Azure Security Center GYIK – a Virtual Machines szolgáltatással kapcsolatos kérdések
description: 'Gyakori kérdések a Azure Security Center-beli virtuális gépekkel kapcsolatban: olyan termék, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599365"
---
# <a name="faq---questions-about-virtual-machines"></a>GYAKORI kérdések – a Virtual Machines szolgáltatással kapcsolatos kérdések


## <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?

A [klasszikus és a Resource Manager-alapú üzemi modellekkel](../azure-classic-rm.md)létrehozott virtuális gépek (VM-EK) figyelése és javaslatai elérhetők.

A támogatott platformok listáját a [Azure Security Center támogatott platformok](security-center-os-coverage.md) részben tekintheti meg.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem az Azure Security Center ismeri fel a kártevőirtó megoldást, az Azure virtuális gépen?

Az Azure Security Center az Azure-bővítmények segítségével telepítve kártevőirtó láthassa. Például nem sikerül, amely előre telepítve van a megadott képet, vagy ha a virtuális gépeken a saját folyamatok (például konfigurációs rendszerek) segítségével telepítve kártevőirtó kártevőirtó észleli a Security Center.


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért jelenik meg az üzenet "Hiányzó vizsgálati adatok" virtuális gép?

Ez az üzenet jelenik meg, ha egy virtuális gép vizsgálati adatai hiányoznak. Miután az Azure Security Centerben engedélyezte az adatgyűjtést, némi időt igénybe vehet (de kevesebb mint egy órát), amíg az adatok érkezni kezdenek. A vizsgálati adatok kezdeti feltöltését követően akkor jelenhet meg ez az üzenetet, ha egyáltalán nincsenek vizsgálati adatok, vagy ha az utóbbi időben nem érkeztek. A leállított állapotú virtuális gépekről nem érkeznek vizsgálati adatok. Ez az üzenet volt is megjelenhet, ha vizsgálati adatok nem nemrég (a megtartási házirend a Windows-ügynök, amely rendelkezik az alapértelmezett érték 30 nap) megfelelően van feltöltve.


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>A Security Center milyen gyakran nem vizsgálata az operációs rendszer biztonsági réseivel, a rendszerfrissítésekkel és a végpontvédelem?

Az alábbiakban láthatók a biztonsági rések, frissítések és problémák Security Center vizsgálatainak késési ideje:

- Operációs rendszer biztonsági konfigurációk – adatok 48 órán belül frissült
- Rendszerfrissítések – adatok 24 órán belül frissült
- Az Endpoint Protection kapcsolatos problémák – 8 órán belül adatainak frissítése

A Security Center általában óránként keres új adatokat, és ennek megfelelően frissíti a javaslatokat. 

> [!NOTE]
> Security Center a Microsoft monitoring Agent használatával gyűjti és tárolja az adatokat. További információ: [Azure Security Center platform migrálása](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg az üzenet "Virtuálisgép-ügynök hiányzik?"

Virtuális gépek az adatgyűjtés engedélyezése a Virtuálisgép-ügynököt kell telepíteni. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök más virtuális gépekre való telepítésével kapcsolatos információkért tekintse meg a [VM-ügynök és-bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)című blogbejegyzést.