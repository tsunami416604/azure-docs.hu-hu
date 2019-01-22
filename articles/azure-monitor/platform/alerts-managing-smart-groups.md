---
title: Intelligens csoportok kezelése
description: A riasztási példányok létre intelligens csoportok kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: a9434bebe82687b14f45b3c375572717d71d0e55
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430619"
---
# <a name="manage-smart-groups"></a>Intelligens csoportok kezelése
[Csoportok intelligens](https://aka.ms/smart-groups) csoport együtt riasztások alapján közös előfordulás vagy a hasonló, gépi tanulási algoritmusok használata, hogy a felhasználó is intelligens csoportokat helyett minden riasztás kezelése külön-külön kellene kezelni. Ez a cikk végigvezeti elérése és intelligens csoportok használata az Azure monitorban.
1.  Az intelligens csoportokat létrehozni a riasztási példányok megtekintéséhez lehetőségek közül választhat
     1. Kattintson a **intelligens csoportok** származó a **riasztások szerint** lap    
    ![Monitorozás](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
     2. Kattintson a riasztások intelligens csoportok szerint az összes riasztás oldalról   
     ![Figyelés](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)
2.  Ekkor megjelenik a lista nézet a riasztás példányok létre intelligens csoportokhoz. Több riasztás átnéznie, helyett mostantól kezelhető, az intelligens csoportok helyette.   
![Figyelés](./media/alerts-managing-smart-groups/sg-list.jpg)
3.  Intelligens egyetlen csoportnak sem a hivatkozásra kattintva megnyílik a Részletek lap, ahol megtekintheti a csoportosítási OK, tag riasztások együtt fel. Ez az összesítés lehetővé teszi egyetlen intelligens csoport helyett több riasztás átnéznie foglalkozik.   
![Figyelés](./media/alerts-managing-smart-groups/sg-details.jpg)


