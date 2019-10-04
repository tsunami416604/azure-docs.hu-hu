---
title: Mi történt a ASP.NET 5 projekttel (Visual Studio csatlakoztatott szolgáltatások) | Microsoft Docs
description: Leírja, hogy mi történik az Azure Storage-fiókhoz való csatlakozás után a Visual Studio ASP.NET 5 projektben a Visual Studio csatlakoztatott szolgáltatásainak használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e29c0302ecd703cb02199df95892e24917baf8e8
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510760"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Mi történt a ASP.NET 5 projekttel (Visual Studio Azure Storage Connected Services)?
## <a name="references-added"></a>Hivatkozások hozzáadva
Az Azure Storage NuGet csomagja hozzá lett adva a Visual Studio-projekthez.  
Ez a csomag a következő .NET-referenciákat veszi fel:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Emellett felvette a **Microsoft. Framework. Configuration. JSON** NuGet-csomagot.

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt config. JSON fájljában egy elem lett létrehozva a kiválasztott Storage-fiókhoz tartozó összekapcsolási karakterlánccal és kulccsal.

További információ: [ASP.net 5](https://www.asp.net/vnext).

