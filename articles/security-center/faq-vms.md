---
title: Az Azure Security Center – gyakori kérdések – kérdések a virtuális gépekkel kapcsolatban
description: Gyakori kérdések az Azure Security Center ben található virtuális gépekről, amely a fenyegetések megelőzésében, észlelésében és az azokra való reagálásban segít
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599365"
---
# <a name="faq---questions-about-virtual-machines"></a>GYIK - Kérdések a virtuális gépekkel kapcsolatban


## <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?

Figyelés és javaslatok érhetők el a klasszikus és az [Erőforrás-kezelő központi telepítési modelljeivel](../azure-classic-rm.md)létrehozott virtuális gépekhez .Monitoring and recommendations are available for virtual machines (VMs) created using both the classic and Resource Manager deployment models.

A támogatott platformok listájáért tekintse meg [az Azure Security Center támogatott platformjait.](security-center-os-coverage.md)


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem ismeri fel az Azure Security Center az Azure-beli virtuális gépen futó kártevőirtó megoldást?

Az Azure Security Center az Azure-bővítményeken keresztül telepített kártevőirtók raista. A Security Center például nem képes észlelni a megadott lemezképre előre telepített kártevőirtókat, vagy ha saját folyamatait (például konfigurációkezelő rendszereket) használva telepítette a kártevőirtót a virtuális gépekre.


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért jelenik meg a "Hiányzó bekési adatok" üzenet a virtuális gépemhez?

Ez az üzenet jelenik meg, ha egy virtuális gép vizsgálati adatai hiányoznak. Miután az Azure Security Centerben engedélyezte az adatgyűjtést, némi időt igénybe vehet (de kevesebb mint egy órát), amíg az adatok érkezni kezdenek. A vizsgálati adatok kezdeti feltöltését követően akkor jelenhet meg ez az üzenetet, ha egyáltalán nincsenek vizsgálati adatok, vagy ha az utóbbi időben nem érkeztek. A leállított állapotú virtuális gépekről nem érkeznek vizsgálati adatok. Ez az üzenet akkor is megjelenhet, ha a bekéselt adatok nem töltődtek fel a közelmúltban (a Windows-ügynök adatmegőrzési házirendjének megfelelően, amelynek alapértelmezett értéke 30 nap).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Milyen gyakran keres a Security Center az operációs rendszer biztonsági réseit, a rendszerfrissítéseket és a végpontvédelmi problémákat?

Az alábbiakban a Biztonsági központ biztonsági réseinek, frissítéseinek és problémáinak késési idejét olvashatja:

- Operációs rendszer biztonsági konfigurációi – az adatok 48 órán belül frissülnek
- Rendszerfrissítések – az adatok 24 órán belül frissülnek
- Végpontvédelmi problémák – az adatok frissítése 8 órán belül

A Security Center általában óránként ellenőrzi az új adatokat, és ennek megfelelően frissíti a javaslatokat. 

> [!NOTE]
> A Security Center a Microsoft Monitoring Agent segítségével gyűjti és tárolja az adatokat. További információ: [Azure Security Center Platform Migration](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg a "Virtuálisgép-ügynök hiányzik?" üzenet?

Az adatgyűjtés engedélyezéséhez a virtuális gép ügynökét telepíteni kell a virtuális gépekre. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök más virtuális gépekre történő telepítéséről a [virtuális gépügynök és -bővítmények blogbejegyzésben](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)talál további információt.