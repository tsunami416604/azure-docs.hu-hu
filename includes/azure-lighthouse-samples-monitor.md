---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356267"
---
Ezek a minták bemutatják, hogyan hozhat létre riasztásokat az Azure Monitor használatával az Azure által delegált erőforrás-kezeléshez előkészített előfizetésekhez.

| **Sablon** | **Leírás** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Lekérdezi a legutóbbi 1 nap tevékenységeit egy kezelést végző bérlőben, és [jelentést készít az esetlegesen hozzáadott vagy eltávolított delegálásokról](../articles/lighthouse/how-to/monitor-delegation-changes.md) (vagy a sikertelen próbálkozásokról).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Ez a sablon létrehoz egy Azure-riasztást, és csatlakoztatja azt egy meglévő műveletcsoporthoz.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Több naplóriasztást hoz létre Kusto-lekérdezések alapján.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Üzembe helyez egy riasztást a bérlőben, ha a felhasználó előfizetést delegál a kezelést végző bérlő számára.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Megjeleníti a különböző előfizetések Azure-tevékenységnaplóit a naplók tartománynév alapján történő szűrését lehetővé tevő lehetőséggel. |
