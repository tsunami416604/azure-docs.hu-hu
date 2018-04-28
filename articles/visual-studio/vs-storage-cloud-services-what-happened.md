---
title: Mi történt a felhőszolgáltatás-projekt? | Microsoft Docs
description: Ismerteti, mi történik a felhőalapú szolgáltatások projektben services csatlakozik a Visual Studio használatával Azure storage-fiók összekötése után
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e88e41edbd062f89e24915889f5b74660ec45513
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
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

