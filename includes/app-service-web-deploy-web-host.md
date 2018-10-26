---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134166"
---
### <a name="app-service-plan"></a>App Service-csomag
Az service-csomagban a webalkalmazás üzemeltetéséhez létrehoz. Azt adja meg a terv keretében nevét a **hostingPlanName** paraméter. A csomag helye ugyanazon a helyen használt erőforráscsoport. Díjszabási szint és a feldolgozó mérete vannak megadva a **termékváltozat** és **workerSize** paraméterek

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

