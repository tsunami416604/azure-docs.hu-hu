---
title: Azure Application Insights intelligens észlelés – az alapértelmezett értesítési címzettek közelgő változása | Microsoft Docs
description: Nyomon követheti az alkalmazások nyomon követését az Azure Application Insights a nyomkövetési telemetria szokatlan mintáit.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1f98b5a9ee592a4c702e87e365eff7941194d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820626"
---
# <a name="smart-detection-e-mail-notification-change"></a>Az e-mail értesítések intelligens észlelésének változása

Az ügyfelek visszajelzései alapján 2019 április 1-jén módosítjuk azokat az alapértelmezett szerepköröket, amelyek e-mail-értesítéseket fogadnak az intelligens észleléstől.

## <a name="what-is-changing"></a>Mi változik?

Jelenleg az intelligens észlelési értesítő e-maileket a rendszer alapértelmezés szerint az _előfizetés tulajdonosa_, az _előfizetés közreműködői_és az _előfizetés-olvasó_ szerepkörei számára küldi el. Ezek a szerepkörök gyakran tartalmaznak olyan felhasználókat, akik nem a monitorozás aktív résztvevői, így sokan közülük fölöslegesen kaphatják meg az értesítéseket. A felhasználói élmény javítása érdekében az e-mail értesítések csak a [figyelési olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és a [közreműködői szerepkörök figyelését](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) teszik elérhetővé.

## <a name="scope-of-this-change"></a>A módosítás hatóköre

Ez a módosítás a következők kivételével az összes intelligens detektálási szabályra érvényes:

* Az előzetes verzióként megjelölt intelligens detektálási szabályok. Ezek az intelligens észlelési szabályok jelenleg nem támogatják az e-mail-értesítéseket.

* A Hibaanomáliák szabály. Ez a szabály elindítja az új alapértelmezett szerepkörök célzását, miután áttelepítette azt egy klasszikus riasztásból az egyesített riasztások platformra (További információ [itt](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)található.)

## <a name="how-to-prepare-for-this-change"></a>Felkészülés ehhez a változáshoz

Annak biztosítása érdekében, hogy az intelligens észlelésről érkező e-mailes értesítések a megfelelő felhasználók számára legyenek elküldve, ezeket a felhasználókat hozzá kell rendelni a [figyelési olvasóhoz](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) vagy az előfizetés [figyelő közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepköreihez.

Ha felhasználókat szeretne hozzárendelni a figyelési olvasóhoz vagy a közreműködő szerepkörökhöz a Azure Portalon keresztül, kövesse a [szerepkör-hozzárendelés hozzáadása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) című cikkben ismertetett lépéseket. Ügyeljen arra, hogy a _figyelési olvasót_ vagy a _figyelő közreműködőt_ adja meg a felhasználókhoz hozzárendelt szerepkörhöz.

> [!NOTE]
> Ez a változás nem érinti az intelligens észlelési értesítések adott címzettjeit, amelyeket a szabály beállításai között a _további e-mail címzettek_ lehetőséggel konfiguráltak. A címzettek továbbra is megkapják az e-mailes értesítéseket.

Ha bármilyen kérdése vagy problémája van a változással kapcsolatban, ne habozzon [kapcsolatba lépni velünk](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Következő lépések

További információ az intelligens észlelésről:

- [Hiba-anomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítmény-anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)