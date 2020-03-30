---
title: Mi történt a ASP.NET 5 projekttel (Visual Studio csatlakoztatott szolgáltatások)
description: A cikk azt ismerteti, hogy mi történik, miután egy Visual Studio ASP.NET 5-projektben lévő Azure-tárfiókhoz csatlakozott a Visual Studio csatlakoztatott szolgáltatásainak használatával
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300012"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Mi történt a ASP.NET 5 projekttel (Visual Studio Azure Storage-hoz kapcsolódó szolgáltatások)?
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

A **NuGet csomag, a Microsoft.Framework.Configuration.Json** is hozzá lett adva.

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage csatlakozási karakterlánca hozzáadva
A projekt config.json fájljában egy elem jött létre a kijelölt tárfiók kapcsolati karakterláncával és kulcsával.

További információ: [ASP.NET 5](https://www.asp.net/vnext).

