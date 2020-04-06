---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421131"
---
Ezek a minták bemutatják, hogyan hozhat létre riasztásokat az Azure Monitor használatával az Azure által delegált erőforrás-kezeléshez előkészített előfizetésekhez.

| **Sablon** | **Leírás** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Lekérdezi a legutóbbi 1 nap tevékenységeit egy kezelést végző bérlőben, és [jelentést készít az esetlegesen hozzáadott vagy eltávolított delegálásokról](../articles/lighthouse/how-to/monitor-delegation-changes.md) (vagy a sikertelen próbálkozásokról).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ez a sablon létrehoz egy Azure-riasztást, és csatlakoztatja azt egy meglévő műveletcsoporthoz.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Több naplóriasztást hoz létre Kusto-lekérdezések alapján.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Üzembe helyez egy riasztást a bérlőben, ha a felhasználó előfizetést delegál a kezelést végző bérlő számára.|
