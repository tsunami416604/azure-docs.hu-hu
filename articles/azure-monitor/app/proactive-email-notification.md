---
title: Intelligens észlelési értesítés módosítása – Azure Application Insights
description: Váltás az alapértelmezett értesítési címzettekre az Intelligens észlelés szolgáltatásból. Az intelligens észlelés lehetővé teszi az alkalmazások nyomkövetéseifigyelést az Azure Application Insights segítségével a nyomkövetési telemetriai minták szokatlan minták.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671783"
---
# <a name="smart-detection-e-mail-notification-change"></a>Intelligens észlelési e-mail értesítés módosítása

Az ügyfelek visszajelzései alapján 2019. április 1-jén megváltoztatjuk az alapértelmezett szerepköröket, amelyek e-mailes értesítéseket kapnak az Intelligens észleléstől.

## <a name="what-is-changing"></a>Mi változik?

Jelenleg az intelligens észlelési e-mail értesítések alapértelmezés szerint az _Előfizetés tulajdonosa_, _az Előfizetés-közreműködő_és az _Előfizetés-olvasó_ szerepköröknek kerülnek elküldésre. Ezek a szerepkörök gyakran olyan felhasználókat foglalnak magukban, akik nem vesznek részt aktívan a figyelésben, ami miatt sok ilyen felhasználó szükségtelenül kap értesítéseket. Ennek a felhasználói élménynek a javítása érdekében módosítjuk, hogy az e-mail értesítések alapértelmezés szerint csak a [Figyelési olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és a [Közreműködőfigyelés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkörhöz kerülhessenek.

## <a name="scope-of-this-change"></a>A módosítás hatálya

Ez a módosítás minden intelligens észlelési szabályt érint, kivéve a következőket:

* Az intelligens észlelési szabályok előnézetként vannak megjelölve. Ezek az intelligens észlelési szabályok ma nem támogatják az e-mailes értesítéseket.

* A hibaanomáliák szabálya. Ez a szabály az új alapértelmezett szerepkörök célzását kezdi meg, amint áttér egy klasszikus riasztásról az egyesített riasztási platformra (további információ [itt](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)érhető el.)

## <a name="how-to-prepare-for-this-change"></a>Hogyan készüljünk fel erre a változásra?

Annak érdekében, hogy az intelligens észlelési értesítések et a megfelelő felhasználóknak küldje el a rendszer, ezeket a felhasználókat hozzá kell rendelni az előfizetés [figyelési olvasójához](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) vagy [a közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkörökhöz.

Ha felhasználókat szeretne hozzárendelni a figyelési olvasóhoz vagy a közreműködői szerepkörök figyelése szerepkörökhöz az Azure Portalon keresztül, kövesse a [szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) című cikkben ismertetett lépéseket. Győződjön meg arról, hogy a _figyelési olvasó_ vagy _a figyelési közreműködő,_ mint a szerepkör, amelyhez a felhasználók vannak rendelve.

> [!NOTE]
> Ez a módosítás nem érinti az intelligens észlelési értesítések adott címzettjeit, akik a szabálybeállításokban a _További e-mail címzettek_ beállítással vannak konfigurálva. Ezek a címzettek továbbra is megkapják az e-mail értesítéseket.

Ha bármilyen kérdése vagy aggálya van ezzel a változással kapcsolatban, ne habozzon [kapcsolatba lépni velünk.](mailto:smart-alert-feedback@microsoft.com)

## <a name="next-steps"></a>További lépések

További információ az intelligens észlelésről:

- [Hibaanomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítményanomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)
