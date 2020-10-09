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
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986628"
---
Ezek a minták bemutatják, hogyan hozhat létre riasztásokat az Azure Monitor használatával az Azure által delegált erőforrás-kezeléshez előkészített előfizetésekhez.

| **Sablon** | **Leírás** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Lekérdezi a legutóbbi 1 nap tevékenységeit egy kezelést végző bérlőben, és [jelentést készít az esetlegesen hozzáadott vagy eltávolított delegálásokról](../articles/lighthouse/how-to/monitor-delegation-changes.md) (vagy a sikertelen próbálkozásokról).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Ez a sablon létrehoz egy Azure-riasztást, és csatlakoztatja azt egy meglévő műveletcsoporthoz.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Több naplóriasztást hoz létre Kusto-lekérdezések alapján.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Üzembe helyez egy riasztást a bérlőben, ha a felhasználó előfizetést delegál a kezelést végző bérlő számára.|
