---
title: Virtuálisgép-ügynök az Azure Security Center engedélyezése |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **Virtuálisgép-ügynök engedélyezése**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7a059cd5c22306c42adc4c7c8fcc4e52c3ebcd00
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313571"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Virtuálisgép-ügynök az Azure Security Center engedélyezése
A virtuális gép ügynököt telepíteni kell a virtuális gépek (VM) annak érdekében, hogy [adatgyűjtés engedélyezése](security-center-enable-data-collection.md).  Azure Security Center segítségével láthatja, melyik virtuális gépeket a Virtuálisgép-ügynök igényelnek, és azt javasolja, hogy engedélyezze a virtuális gépeken a Virtuálisgép-ügynök.

Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) cikkben talál információkat.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok panel**válassza **Virtuálisgép-ügynök engedélyezése**.
   ![Virtuálisgép-ügynök engedélyezése][1]
2. Ekkor megnyílik a panel **VM ügynök hiányzik vagy nem válaszol**. Ezen a panelen a Virtuálisgép-ügynök igénylő virtuális gépek listája. Kövesse az utasításokat a Virtuálisgép-ügynök telepítése a panelre.
   ![A Virtuálisgép-ügynök hiányzik.][2]

## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-azure-policy.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
