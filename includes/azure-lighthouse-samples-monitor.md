---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 19c708b2e01f2c35542d5688d579796444814d81
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456802"
---
Ezek a minták bemutatják, hogyan hozhat létre riasztásokat az Azure Monitor használatával az Azure által delegált erőforrás-kezeléshez előkészített előfizetésekhez.

| **Sablon** | **Leírás** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ez a sablon létrehoz egy Azure-riasztást, és csatlakoztatja azt egy meglévő műveletcsoporthoz.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Több naplóriasztást hoz létre Kusto-lekérdezések alapján.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Üzembe helyez egy riasztást a bérlőben, ha a felhasználó előfizetést delegál a kezelést végző bérlő számára.|
