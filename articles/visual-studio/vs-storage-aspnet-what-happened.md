---
title: Mi történt az ASP.NET-projektemmel? | Microsoft Docs
description: Leírja, hogy mi történik az Azure Storage ASP.NET-projekthez való hozzáadása után a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 74db2e1e15c282f4393715c67561b2ed61bbcdfb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512064"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Mi történt a ASP.NET-projekttel (Visual Studio Azure Storage Connected Service)?
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

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt web. config fájljában egy elem lett létrehozva a kiválasztott Storage-fiók kapcsolati karakterlánccal és kulcsával.

További információ: [ASP.net](https://www.asp.net).

