---
title: Mi történt az ASP.NET-projekt? | Microsoft Docs
description: Ismerteti, mi történik egy ASP.NET-projekt Visual Studio használatával ad hozzá Azure Storage szolgáltatások csatlakoztatása után
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 84b608d335dc26198c3af6f89407758fd1d020dc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791773"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Mi történt az ASP.NET-projekt (a Visual Studio Azure Storage szolgáltatás csatlakozik)?
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
A web.config fájlban a projekt a kiválasztott tárfiók kapcsolati karakterláncot és a kulcs elem hozták létre.

További információkért lásd: [ASP.NET](http://www.asp.net).

