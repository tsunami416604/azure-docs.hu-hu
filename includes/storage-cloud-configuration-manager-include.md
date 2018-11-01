---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166497"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) osztály konfigurációs beállításokat elemez, függetlenül attól, hogy az ügyfélalkalmazás az asztali számítógépen, egy mobileszközön, egy Azure virtuális gépen vagy egy Azure-felhőszolgáltatásban fut-e.

Adja hozzá a következő `using` utasítást, ha a CloudConfigurationManager csomagra kíván hivatkozni:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy konfigurációs fájlból:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Az Azure Configuration Manager használata nem kötelező. Használhat egy API-t is, például a .NET-keretrendszer [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) osztályát.

