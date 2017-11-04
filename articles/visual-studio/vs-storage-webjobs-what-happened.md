---
title: "Mi történt a webjobs-feladat project (a Visual Studio Azure Storage szolgáltatás csatlakozik)? | Microsoft Docs"
description: "Ismerteti, mi történt Azure webjobs-feladat projektben services csatlakozik a Visual Studio használatával storage-fiók összekötése után"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 8891685a99c5ba366b74af0a21396d4a5e499835
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Mi történt a webjobs-feladat project (a Visual Studio Azure Storage szolgáltatás csatlakozik)?
## <a name="references-added"></a>Hozzáadott
Az Azure Storage NuGet-csomag hozzáadni vagy frissíteni a Visual Studio-projekt.  
Ez a csomag a következő .NET hivatkozásokat ad:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Hozzáadott Azure Storage kapcsolati karakterlánca
A projekt App.config fájlban a **AzureWebJobsStorage** és **AzureWebJobsDashboard** bejegyzések frissültek a kiválasztott tárfiók kapcsolati karakterlánc és kulccsal.

További információkért lásd: [Azure WebJobs-dokumentáció erőforrások](http://go.microsoft.com/fwlink/?linkid=390226).

