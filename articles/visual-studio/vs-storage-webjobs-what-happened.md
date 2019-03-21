---
title: Mi történt a WebJob-projektemmel (a Visual Studio Azure Storage szolgáltatás csatlakozik)? | Microsoft Docs
description: Leírja, mi történt Azure WebJob-projektben, miután csatlakozik a storage-fiók a Visual Studio csatlakoztatott szolgáltatásainak
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: fa152d8b88254a35d00b91537bf1001ea1130e57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884642"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Mi történt a WebJob-projektemmel (a Visual Studio Azure Storage szolgáltatás csatlakozik)?
## <a name="references-added"></a>Hivatkozás
Az Azure Storage NuGet-csomag való hozzáadásakor vagy frissítésekor a Visual Studio-projektben.  
Ez a csomag hozzáadja a következő .NET hivatkozásokat:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadva az Azure Storage kapcsolati karakterlánc
A projekt App.config fájlban a **AzureWebJobsStorage** és **AzureWebJobsDashboard** bejegyzés frissítve a kiválasztott tárfiók kapcsolati karakterláncot, illetve kulcsára.

További információkért lásd: [Azure WebJobs-dokumentáció erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

