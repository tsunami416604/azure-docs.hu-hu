---
title: Mi történt az ASP.NET-projektemmel?
description: A cikk azt ismerteti, hogy mi történik, miután hozzáadott egy ASP.NET projekthez a Visual Studio csatlakoztatott szolgáltatásainak használatával
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300035"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Mi történt a ASP.NET projekttel (Visual Studio Azure Storage-hoz csatlakoztatott szolgáltatás)?
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
A projekt web.config fájljában egy elem jött létre a kijelölt tárfiók kapcsolati karakterláncával és kulcsával.

További információ: [ASP.NET](https://www.asp.net).

