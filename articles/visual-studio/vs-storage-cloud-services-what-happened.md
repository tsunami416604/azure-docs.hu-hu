---
title: Mi történt a Cloud Service-projekttel?
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298758"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Mi történt felhőszolgáltatás-projektemmel (a Visual Studio Azure Storage-hoz kapcsolódó szolgáltatással)?
## <a name="references-added"></a>Hivatkozások hozzáadva
Az Azure Storage NuGet csomagja hozzá lett adva a Visual Studio-projekthez.  
Ez a csomag a következő .NET-referenciákat veszi fel:

* **Microsoft. EDM**
* **Microsoft. OData**
* **Microsoft. Services. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System. reaz**
* **System. térbeli**

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
Elemek lettek létrehozva a kiválasztott Storage-fiókhoz tartozó összekapcsolási karakterlánccal és kulccsal. A következő fájlok módosításai történtek:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. local. cscfg**

