---
title: Azure Security Center GYIK – a meglévő Log Analytics ügynökökkel kapcsolatos kérdések
description: Ez a gyakori kérdések a Log Analytics-ügynököt használó ügyfelek kérdéseire válaszolnak, és Azure Security Center, amely a fenyegetések megelőzését, észlelését és reagálását megkönnyítő termékre vonatkozik.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436167"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Gyakori kérdések a Azure Monitor naplókat használó ügyfelek számára<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center felülbírálja a virtuális gépek és a munkaterületek közötti meglévő kapcsolatokat?

Ha egy virtuális gépen már telepítve van a Log Analytics ügynök Azure-bővítményként, Security Center nem bírálja felül a meglévő munkaterület-kapcsolatokat. Ehelyett Security Center a meglévő munkaterületet használja. A virtuális gép védett lesz, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van azon a munkaterületen, amelyre a jelentést küldi. 

Egy Security Center megoldás van telepítve az adatgyűjtési képernyőn kiválasztott munkaterületre, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre lesz alkalmazva. Megoldás hozzáadásakor a rendszer alapértelmezés szerint automatikusan telepíti a Log Analytics munkaterülethez csatlakoztatott összes Windows-és Linux-ügynököt. A [megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md) használatával hatókört alkalmazhat a megoldásokra.

> [!TIP]
> Ha a Log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center nem telepíti a Log Analytics-ügynököt, és a biztonsági figyelés korlátozott.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center telepítési megoldásokat a meglévő Log Analytics-munkaterületekre? Mik a számlázási következményei?
Ha Security Center azt észleli, hogy egy virtuális gép már csatlakoztatva van egy létrehozott munkaterülethez, Security Center engedélyezi a megoldásokat a munkaterületen az Ön díjszabási szintjétől függően. A megoldásokat csak a megfelelő Azure-beli virtuális gépekre alkalmazza a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül, így a számlázás változatlan marad.

- **Ingyenes szintű** – Security Center a "SecurityCenterFree" megoldást telepíti a munkaterületre. Az ingyenes szintet nem számoljuk fel.
- **Standard szint** – Security Center a "biztonsági" megoldást telepíti a munkaterületre.

   ![Megoldások az alapértelmezett munkaterületen](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már van munkaterületem a saját környezetben, használhatom a biztonsági adatok gyűjtésére?
Ha egy virtuális gépen már telepítve van a Log Analytics ügynök Azure-bővítményként, Security Center a meglévő csatlakoztatott munkaterületet használja. A munkaterületre egy Security Center megoldás van telepítve, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre vonatkozik, a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül.

Amikor Security Center telepíti a Log Analytics-ügynököt a virtuális gépekre, a Security Center által létrehozott alapértelmezett munkaterületet (ka) t használja.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldásom a saját munkaterületeken. Mik a számlázási következményei?
A biztonsági & auditálási megoldással engedélyezhető az Azure-beli virtuális gépek Security Center standard szintű szolgáltatásainak használata. Ha a biztonsági & auditálási megoldás már telepítve van egy munkaterületen, Security Center a meglévő megoldást használja. A számlázás nem változik.