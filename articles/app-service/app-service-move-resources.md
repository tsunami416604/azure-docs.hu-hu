---
title: "Webes alkalmazás erőforrások áthelyezése egy másik erőforráscsoportban"
description: "Ez a témakör a ahol áthelyezheti webes alkalmazások és az App Service szolgáltatások az egyik erőforráscsoportból a másikba."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="supported-move-configurations"></a>Támogatott áthelyezés konfigurációk
Azure-webalkalmazás-erőforrások áthelyezheti a [Resource Manager áthelyezése erőforrások API](../azure-resource-manager/resource-group-move-resources.md).

Az Azure Web Apps jelenleg a következő move-forgatókönyveket teszi lehetővé:

* A teljes tartalma (a webes alkalmazásokat, a app service-csomagokról és a tanúsítványok) erőforráscsoport áthelyezése egy másik erőforráscsoportban. 
   > [!Note]
   > A célként megadott erőforráscsoport nem ebben a forgatókönyvben Microsoft.Web erőforrásokat tartalmazhat.

* Egy másik erőforráscsoportban található egyes webalkalmazások helyezhetik át, miközben továbbra is az az aktuális app service-csomag (régi az erőforráscsoportban marad az app service-csomag) futtató őket.


