---
title: Mi történt az ASP.NET 5-projektemmel (a Visual Studio csatlakoztatott szolgáltatásainak) |} A Microsoft Docs
description: Leírja, mi történik, miután csatlakozik az Azure storage-fiók, a Visual Studio ASP.NET 5-projektben a Visual Studio használatával csatlakoztatott szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 0e38e570b5ecb2a19af943e0dde96e2ed66f3507
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054519"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Mi történt az ASP.NET 5-projektemmel (a Visual Studio Azure Storage szolgáltatások csatlakozik)?
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

Emellett a NuGet-csomag **Microsoft.Framework.Configuration.Json** hozzá lett adva.

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadva az Azure Storage kapcsolati karakterlánc
A projekt config.json fájl a kiválasztott tárfiók kapcsolati karakterláncot, és a kulcs egy elemet hozott létre.

További információkért lásd: [ASP.NET 5](http://www.asp.net/vnext).

