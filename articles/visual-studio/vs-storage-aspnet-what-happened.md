---
title: Mi történt az ASP.NET-projektemmel?
description: Leírja, hogy mi történik az Azure Storage ASP.NET-projekthez való hozzáadása után a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300035"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Mi történt a ASP.NET-projekttel (Visual Studio Azure Storage Connected Service)?
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

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt web. config fájljában egy elem lett létrehozva a kiválasztott Storage-fiók kapcsolati karakterlánccal és kulcsával.

További információ: [ASP.net](https://www.asp.net).

