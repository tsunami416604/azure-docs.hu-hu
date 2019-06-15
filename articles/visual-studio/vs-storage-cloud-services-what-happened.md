---
title: Mi történt a cloud service-projektemmel? | Microsoft Docs
description: Leírja, mi történik a cloud services projektben, miután csatlakozik az Azure storage-fiók használata Visual Studio csatlakoztatott szolgáltatásainak
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b0adceb42109f07658e56cebde3fbc4401478a1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720457"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Mi történt a felhőszolgáltatás (Visual Studio Azure Storage csatlakoztatott szolgáltatás)?
## <a name="references-added"></a>Hivatkozás
Az Azure Storage NuGet-csomagot a Visual Studio-projekt lett hozzáadva.  
Ez a csomag hozzáadja a következő .NET hivatkozásokat:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadva az Azure Storage kapcsolati karakterlánc
Elemek jöttek létre a kiválasztott tárfiók kapcsolati karakterláncot, illetve kulcsára. Módosítások történtek a következő fájlokat:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

