---
title: Mi történt a Webjobs-projekttel (Visual Studio Azure Storage)?
description: Leírja, hogy mi történt egy Azure Webjobs-projektben, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298725"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Mi történt a Webjobs-projekttel (Visual Studio Azure Storage Connected Service)?
## <a name="references-added"></a>Hivatkozások hozzáadva
Az Azure Storage NuGet csomagja hozzá lett adva vagy frissítve lett a Visual Studio-projektben.  
Ez a csomag a következő .NET-referenciákat veszi fel:

* **Microsoft. EDM**
* **Microsoft. OData**
* **Microsoft. Services. Services. Client**
* **Microsoft. WindowsAzure. ConfigurationManager**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. reaz**
* **System. térbeli**

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt app. config fájljában a **AzureWebJobsStorage** és a **AzureWebJobsDashboard** bejegyzések frissültek a kiválasztott Storage-fiókhoz tartozó kapcsolatok karakterláncával és kulcsával.

További információ: [Azure WebJobs dokumentációs erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

