---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251959"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) osztály konfigurációs beállításokat elemez, függetlenül attól, hogy az ügyfélalkalmazás az asztali számítógépen, egy mobileszközön, egy Azure virtuális gépen vagy egy Azure-felhőszolgáltatásban fut-e.

Adja hozzá a következő `using` utasítást, ha a CloudConfigurationManager csomagra kíván hivatkozni:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy konfigurációs fájlból:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Az Azure Configuration Manager használata nem kötelező. Használhat egy API-t is, például a .NET-keretrendszer [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) osztályát.

