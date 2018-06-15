---
title: Mi történt a webjobs-feladat project (a Visual Studio Azure Storage szolgáltatás csatlakozik)? | Microsoft Docs
description: Ismerteti, mi történt Azure webjobs-feladat projektben services csatlakozik a Visual Studio használatával storage-fiók összekötése után
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 056ccd572a04a436ff53dda6ae967711c9f9903d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790797"
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

