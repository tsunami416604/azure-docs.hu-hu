---
title: Az Azure Security Center – kérdések – kérdések a meglévő Log Analytics-ügynökökkel kapcsolatban
description: Ez a gyIK megválaszolja a Log Analytics-ügynököt már használó ügyfelek számára feltett kérdéseket, és figyelembe veszi az Azure Security Centert, amely egy olyan termék, amely segít a fenyegetések megelőzésében, észlelésben és a fenyegetések megválaszolásában.
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
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436167"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Gyakran feltett kérdések az Azure Monitor naplóit már használó ügyfelek számára<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center felülbírálja a virtuális gépek és a munkaterületek közötti meglévő kapcsolatokat?

Ha egy virtuális gép már rendelkezik a Log Analytics-ügynök telepítve van az Azure-bővítményként, a Security Center nem bírálja felül a meglévő munkaterületi kapcsolatot. Ehelyett a Security Center a meglévő munkaterületet használja. A virtuális gép védelemben részesül, feltéve, hogy a "Security" vagy a "SecurityCenterFree" megoldás telepítve van azon a munkaterületen, amelyről jelentést tesz. 

A Security Center megoldás telepítve van a munkaterület kiválasztott adatgyűjtés i. Amikor hozzáad egy megoldást, az alapértelmezés szerint automatikusan települ a Log Analytics-munkaterülethez csatlakoztatott összes Windows- és Linux-ügynökre. [A megoldáscélzás](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy hatókört alkalmazzon a megoldásokra.

> [!TIP]
> Ha a Log Analytics-ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), a Security Center nem telepíti a Log Analytics-ügynököt, és a biztonsági figyelés korlátozott.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Telepíti a Security Center a megoldásokat a meglévő Log Analytics-munkaterületeken? Milyen következményekkel jár a számlázási vonzata?
Amikor a Security Center megállapítja, hogy egy virtuális gép már csatlakozik egy létrehozott munkaterülethez, a Security Center a tarifacsomagnak megfelelően engedélyezi a megoldásokat ezen a munkaterületen. A megoldások csak a megfelelő Azure-beli virtuális gépekre vonatkoznak a [megoldáscélzáson](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül, így a számlázás ugyanaz marad.

- **Ingyenes szint** – A Security Center telepíti a "SecurityCenterFree" megoldást a munkaterületre. Az ingyenes szintért nem kell fizetnie.
- **Standard szint** – A Security Center telepíti a "Biztonság" megoldást a munkaterületre.

   ![Megoldások az alapértelmezett munkaterületen](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már vannak munkaterületeim a környezetemben, használhatom őket biztonsági adatok gyűjtésére?
Ha egy virtuális gép már rendelkezik a Log Analytics-ügynök telepítve van az Azure-bővítményként, a Security Center a meglévő csatlakoztatott munkaterületet használja. A Security Center-megoldás telepítve van a munkaterületen, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre vonatkozik [a megoldás célzása](../operations-management-suite/operations-management-suite-solution-targeting.md)révén.

Amikor a Security Center telepíti a Log Analytics-ügynököt a virtuális gépeken, a Security Center által létrehozott alapértelmezett munkaterület(eke)t használja.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldás a munkaterületeimen. Milyen következményekkel jár a számlázási vonzata?
A Security & Audit megoldás az Azure-beli virtuális gépek biztonsági & szabványos rétegfunkcióinak engedélyezésére szolgál. Ha a Biztonsági & naplózási megoldás már telepítve van egy munkaterületen, a Security Center a meglévő megoldást használja. A számlázásban nincs változás.