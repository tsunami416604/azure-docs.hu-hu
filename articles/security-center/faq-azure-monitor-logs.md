---
title: Azure Security Center GYIK – a meglévő MMA kapcsolatos kérdések
description: Ez a gyakori kérdések a Microsoft monitoring agentet használó ügyfelek kérdéseire válaszolnak, és Azure Security Center, a fenyegetések megelőzését, észlelését és elhárítását megkönnyítő terméket fontolgatják.
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661889"
---
# Gyakori kérdések a Azure Monitor naplókat használó ügyfelek számára<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center nem bírálja felül bármely meglévő kapcsolatokat a virtuális gépek és a munkaterületek között?

Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve van az Azure kiterjesztése, a Security Center nem bírálja felül a meglévő munkaterület-kapcsolatot. Ehelyett a Security Center a meglévő munkaterületet használja. A virtuális gép védett lesz, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van azon a munkaterületen, amelyre a jelentést küldi. 

Egy Security Center megoldás van telepítve az adatgyűjtési képernyőn kiválasztott munkaterületre, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre lesz alkalmazva. A megoldás hozzáadásakor az automatikusan telepített összes Windows és Linux-ügynökök az a Log Analytics-munkaterülethez kapcsolódó alapértelmezés szerint a. A [megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md) használatával hatókört alkalmazhat a megoldásokra.

> [!TIP]
> Ha a Microsoft monitoring Agent közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center nem telepíti a Microsoft monitoring agentet, és a biztonság figyelése korlátozott.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A Security Center telepítési megoldások a saját meglévő Log Analytics-munkaterület? Mik azok a számlázását?
A Security Center azonosítja, hogy egy virtuális Gépet már csatlakoztatva van egy munkaterületet hozott létre, ha a Security Center lehetővé teszi ezen a munkaterületen, a tarifacsomag szerint megoldásokat. A megoldásokat csak a megfelelő Azure-beli virtuális gépekre alkalmazza a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül, így a számlázás változatlan marad.

- **Ingyenes szintű** – Security Center a "SecurityCenterFree" megoldást telepíti a munkaterületre. Az ingyenes szintet nem számoljuk fel.
- **Standard szint** – Security Center a "biztonsági" megoldást telepíti a munkaterületre.

   ![Alapértelmezett munkaterület-megoldások](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már rendelkezem munkaterületek saját környezetben, használható őket használatával gyűjt biztonsági adatokat?
Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve van az Azure kiterjesztése, a Security Center a meglévő csatlakoztatott munkaterület használ. A munkaterületre egy Security Center megoldás van telepítve, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre vonatkozik, a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül.

A Security Center telepíti a Microsoft Monitoring Agentet a virtuális gépekre, használja a Security Center által létrehozott alapértelmezett munkaterületeire.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldást a saját munkaterület. Mik azok a számlázását?
A biztonsági & auditálási megoldással engedélyezhető az Azure-beli virtuális gépek Security Center standard szintű szolgáltatásainak használata. Ha a biztonsági és auditálási megoldás már telepítve van a munkaterülethez, a Security Center a meglévő megoldást használ. Nem történik változás a számlázás.