---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67178926"
---
### <a name="app-service-plan"></a>App Service-csomag
Létrehozza a webalkalmazás üzemeltetéséhez szükséges szolgáltatási csomagot. Adja meg a csomag nevét a **hostingPlanName** paraméterrel. A csomag helye megegyezik az erőforráscsoport helyével. Az árképzési szintet és a feldolgozói méretet a **SKU** és a **workerSize** paraméterben határozzák meg

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

