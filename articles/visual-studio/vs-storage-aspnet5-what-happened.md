---
title: Mi történt az ASP.NET 5 project (Visual Studio kapcsolódó szolgáltatások) |} Microsoft Docs
description: Ismerteti, mi történik az services csatlakozik a Visual Studio ASP.NET 5 projektben Visual Studio használatával Azure storage-fiók összekötése után?
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f99ba4b6c954ae9faa87b9604c06e94c56e4f631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Mi történt az ASP.NET 5 project (a Visual Studio Azure Storage szolgáltatások csatlakozik)?
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

Emellett a NuGet-csomag **Microsoft.Framework.Configuration.Json** hozzá lett adva.

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadott Azure Storage kapcsolati karakterlánca
A projekt config.json fájlban a kiválasztott tárfiók kapcsolati karakterláncot és a kulcs elem hozták létre.

További információkért lásd: [ASP.NET 5](http://www.asp.net/vnext).

