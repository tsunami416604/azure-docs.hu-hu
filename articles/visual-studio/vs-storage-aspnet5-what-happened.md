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
ms.openlocfilehash: 71a95e1974cbcec9afcc3337eb37275532e1b527
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999708"
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

További információkért lásd: [ASP.NET 5](https://www.asp.net/vnext).

