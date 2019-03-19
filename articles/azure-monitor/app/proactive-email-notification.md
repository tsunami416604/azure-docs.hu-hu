---
title: Az Azure Application Insights intelligens detektálás – hamarosan várható módosítsa az alapértelmezett értesítési címzett |} A Microsoft Docs
description: Figyelheti az alkalmazások nyomkövetéseit az Azure Application Insights – híváslánc-telemetria a szokatlan mintákat.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889284"
---
# <a name="smart-detection-e-mail-notification-change"></a>Az intelligens észlelési e-mailben értesítést módosítása

2019. április 1., az ügyfelek visszajelzései miatt, alapján azt megváltoztatja az alapértelmezett szerepköröket, aki az e-mail-értesítések fogadása az intelligens detektálás.

## <a name="what-is-changing"></a>Mi változik?

Jelenleg az intelligens detektálás e-mail értesítések küldését, alapértelmezés szerint a _előfizetés tulajdonosa_, _előfizetés Közreműködője_, és _előfizetés: olvasó_ szerepköröket. Ezek a szerepkörök gyakran tartalmaznak olyan felhasználókat, akik nem a monitorozás aktív résztvevői, így sokan közülük fölöslegesen kaphatják meg az értesítéseket. Ez a felület javítása érdekében igyekszünk módosítása, hogy az e-mail-értesítések csak nyissa meg a [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és [közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkörök alapértelmezés szerint.

## <a name="scope-of-this-change"></a>Ez a változás hatóköre

Ez a módosítás a következők kivételével az összes intelligens detektálási szabályra érvényes:

* Az előzetes verzióként megjelölt intelligens detektálási szabályok. E intelligens detektálási szabályok e-mail-értesítések jelenleg nem támogatottak.

* A Hibaanomáliák szabály. Ez a szabály megkezdi az új alapértelmezett szerepköröket célzó, miután azt át lett telepítve egy klasszikus riasztást a riasztások egységes platform (további információk érhetők el [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Hogyan fel a változásra?

Győződjön meg arról, hogy az intelligens észlelésről e-mail-értesítések küldését a megfelelő felhasználók számára, hogy ezek a felhasználók hozzá kell rendelni a [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) vagy [közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkört az előfizetés.

Felhasználók hozzárendelése a Monitoring Reader vagy közreműködő figyelése szerepkörök az Azure Portalon, az ismertetett lépéseket követve a [egy szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) cikk. Ügyeljen arra, hogy válassza ki a _Monitoring Reader_ vagy _közreműködő figyelése_ , amelyhez felhasználók vannak hozzárendelve a szerepkörnek.

> [!NOTE]
> Intelligens detektálás értesítések, konfigurált meghatározott címzettek a _e-mail további címzettjei_ a Szabálybeállítások beállításra kattint, ez a változás nem érinti. Ezek a címzettek az e-mail értesítések fogadásának továbbra is.

Ha bármilyen kérdése vagy aggodalma van, erről a változásról, ne habozzon, [lépjen kapcsolatba velünk](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>További lépések

További információk az intelligens észlelés:

- [Hiba-anomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítmény-anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)