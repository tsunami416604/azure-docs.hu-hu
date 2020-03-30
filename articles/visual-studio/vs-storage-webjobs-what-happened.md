---
title: Mi történt a WebJob-projekttel (Visual Studio Azure Storage)?
description: A Visual Studio csatlakoztatott szolgáltatásaival egy tárfiókhoz való csatlakozás után az Azure WebJob-projektben történt műveletek ismertetése
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298725"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Mi történt a WebJob-projekttel (Visual Studio Azure Storage-hoz csatlakoztatott szolgáltatás)?
## <a name="references-added"></a>Hozzáadott hivatkozások
Az Azure Storage NuGet csomag hozzá lett adva vagy frissítve a Visual Studio-projektben.  
Ez a csomag a következő .NET-hivatkozásokat adja hozzá:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Ügyfél**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage csatlakozási karakterlánca hozzáadva
A projekt App.config fájljában az **AzureWebJobsStorage** és az **AzureWebJobsDashboard** bejegyzések frissültek a kiválasztott tárfiók kapcsolati karakterláncával és kulcsával.

További információt az [Azure WebJobs dokumentációs erőforrásai című témakörben talál.](https://go.microsoft.com/fwlink/?linkid=390226)

