---
title: Mi történt a Cloud Service-projekttel? | Microsoft Docs
description: Leírja, hogy mi történik a Cloud Services-projektekben, miután kapcsolódott egy Azure Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: d96e523b6e4e060eaef33de0f961e7020e5dfb57
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510636"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Mi történt a Cloud Services-projekttel (Visual Studio Azure Storage Connected Service)?
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
Elemek lettek létrehozva a kiválasztott Storage-fiókhoz tartozó összekapcsolási karakterlánccal és kulccsal. A következő fájlok módosításai történtek:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

