---
title: Virtuálisgép-ügynök engedélyezése a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a virtuálisgép- **ügynök engedélyezése**Azure Security Center javaslatot.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60916278"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Virtuálisgép-ügynök engedélyezése Azure Security Center
Az [adatgyűjtés engedélyezéséhez](security-center-enable-data-collection.md)telepíteni kell a virtuálisgép-ügynököt a virtuális gépekre (VM).  Azure Security Center lehetővé teszi, hogy megtekintse, mely virtuális gépek igénylik a virtuálisgép-ügynököt, és javasolja, hogy engedélyezze a virtuálisgép-ügynököt ezeken a virtuális gépeken.

Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) cikkben talál információkat.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok**panelen válassza a virtuálisgép- **ügynök engedélyezése**lehetőséget.
   ![Virtuálisgép-ügynök engedélyezése][1]
2. Ekkor megnyílik a panel **VM-ügynöke, vagy nem válaszol**. Ez a panel felsorolja a virtuálisgép-ügynököt igénylő virtuális gépeket. A virtuálisgép-ügynök telepítéséhez kövesse a panel utasításait.
   ![A virtuálisgép-ügynök hiányzik][2]

## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](tutorial-security-policy.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
