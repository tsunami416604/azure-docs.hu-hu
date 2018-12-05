---
title: Intelligens csoportok kezelése
description: A riasztási példányok létre intelligens csoportok kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 80d5d667b4edee579afed4a9eaa88996663497f4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879775"
---
# <a name="manage-smart-groups"></a>Intelligens csoportok kezelése
[Csoportok intelligens](https://aka.ms/smart-groups) csoport együtt riasztások alapján közös előfordulás vagy a hasonló, gépi tanulási algoritmusok használata, hogy a felhasználó is intelligens csoportokat helyett minden riasztás kezelése külön-külön kellene kezelni. Ez a cikk végigvezeti elérése és intelligens csoportok használata az Azure monitorban.
1.  Az intelligens csoportokat létrehozni a riasztási példányok megtekintéséhez lehetőségek közül választhat
     1. Kattintson a **intelligens csoportok** származó a **riasztások szerint** lap    
    ![Monitorozás](./media/monitoring-alerts-managing-smart-groups/sg-alerts-summary.jpg)
     2. Kattintson a riasztások intelligens csoportok szerint az összes riasztás oldalról   
     ![Figyelés](./media/monitoring-alerts-managing-smart-groups/sg-all-alerts.jpg)
2.  Ekkor megjelenik a lista nézet a riasztás példányok létre intelligens csoportokhoz. Több riasztás átnéznie, helyett mostantól kezelhető, az intelligens csoportok helyette.   
![Figyelés](./media/monitoring-alerts-managing-smart-groups/sg-list.jpg)
3.  Intelligens egyetlen csoportnak sem a hivatkozásra kattintva megnyílik a Részletek lap, ahol megtekintheti a csoportosítási OK, tag riasztások együtt fel. Ez az összesítés lehetővé teszi egyetlen intelligens csoport helyett több riasztás átnéznie foglalkozik.   
![Figyelés](./media/monitoring-alerts-managing-smart-groups/sg-details.jpg)

