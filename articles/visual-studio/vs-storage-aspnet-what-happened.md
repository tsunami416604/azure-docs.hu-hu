---
title: Mi történt az ASP.NET-projektemmel? | Microsoft Docs
description: Leírja, mi történik a szolgáltatások hozzáadása egy ASP.NET-projekt a Visual Studio használatával Azure Storage csatlakoztatása után
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a6e05d706d54d63695861b03cd9de0e65ebdd8bb
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061388"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Mi történt az ASP.NET-projektemmel (a Visual Studio Azure Storage szolgáltatás csatlakozik)?
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
A web.config fájlban a projekt egy elem létrejött, a kiválasztott tárfiók kapcsolati karakterláncot, illetve kulcsára.

További információkért lásd: [ASP.NET](http://www.asp.net).

