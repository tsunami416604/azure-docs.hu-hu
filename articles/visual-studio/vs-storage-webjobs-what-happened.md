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
ms.openlocfilehash: 673143c2bfb23bda45c4e435c3048ae61fa16902
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256901"
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

