---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038162"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) osztály elemzi a konfigurációs beállításokat. Elemzi az asztalon, mobileszközön, Egy Azure virtuális gépen vagy egy Azure-felhőszolgáltatásban futó ügyfélalkalmazások beállításait.

A `CloudConfigurationManager` csomagra való hivatkozáshoz egészítse ki a következő `using` irányelveket:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy konfigurációs fájlból:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Az Azure Configuration Manager használata nem kötelező. Api-kat is használhat, például a [ConfigurationManager Class](/dotnet/api/system.configuration.configurationmanager).
