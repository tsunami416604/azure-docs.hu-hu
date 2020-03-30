---
title: Mi történt a felhőalapú szolgáltatásprojektemmel?
description: A felhőszolgáltatások projektjének leírása, miután a Visual Studio csatlakoztatott szolgáltatásaival csatlakozott egy Azure-tárfiókhoz
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298758"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Mi történt felhőszolgáltatás-projektemmel (a Visual Studio Azure Storage-hoz kapcsolódó szolgáltatással)?
## <a name="references-added"></a>Hozzáadott hivatkozások
Az Azure Storage NuGet csomag hozzá lett adva a Visual Studio-projekthez.  
Ez a csomag a következő .NET-hivatkozásokat adja hozzá:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Ügyfél**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage csatlakozási karakterlánca hozzáadva
Az elemek a kijelölt tárfiók kapcsolati karakterláncával és kulcsával jöttek létre. A következő fájlokat módosították:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

