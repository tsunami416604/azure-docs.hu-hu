---
title: Mi történt a ASP.NET 5 projekttel (Visual Studio csatlakoztatott szolgáltatások)
description: Leírja, hogy mi történik az Azure Storage-fiókhoz való csatlakozás után a Visual Studio ASP.NET 5 projektben a Visual Studio csatlakoztatott szolgáltatásainak használatával
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300012"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Mi történt a ASP.NET 5 projekttel (Visual Studio Azure Storage Connected Services)?
## <a name="references-added"></a>Hivatkozások hozzáadva
Az Azure Storage NuGet csomagja hozzá lett adva a Visual Studio-projekthez.  
Ez a csomag a következő .NET-referenciákat veszi fel:

* **Microsoft. EDM**
* **Microsoft. OData**
* **Microsoft. Services. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. reaz**
* **System. térbeli**

Emellett felvette a **Microsoft. Framework. Configuration. JSON** NuGet-csomagot.

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt config. JSON fájljában egy elem lett létrehozva a kiválasztott Storage-fiókhoz tartozó összekapcsolási karakterlánccal és kulccsal.

További információ: [ASP.net 5](https://www.asp.net/vnext).

