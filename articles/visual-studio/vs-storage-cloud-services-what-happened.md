---
title: "Mi történt a felhőszolgáltatás-projekt? | Microsoft Docs"
description: "Ismerteti, mi történik a felhőalapú szolgáltatások projektben services csatlakozik a Visual Studio használatával Azure storage-fiók összekötése után"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 4c9de56f6daf07097c0f593db37d14dce3bce05f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Mi történt a felhőalapú szolgáltatások projektet (a Visual Studio Azure Storage szolgáltatás csatlakozik)?
## <a name="references-added"></a>Hozzáadott
Az Azure Storage NuGet-csomagot a Visual Studio-projekt lett adva.  
Ez a csomag a következő .NET hivatkozásokat ad:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadott Azure Storage kapcsolati karakterlánca
Elemek a választott tárfiók kapcsolati karakterláncot és a kulcs-ekkel hozta létre. Módosítások történtek a következő fájlokat:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

