---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 11626dd95064cf972a845e5c37f930b9e49c57e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077041"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) osztály konfigurációs beállításokat elemez, függetlenül attól, hogy az ügyfélalkalmazás az asztali számítógépen, egy mobileszközön, egy Azure virtuális gépen vagy egy Azure-felhőszolgáltatásban fut-e.

Adja hozzá a következő `using` utasítást, ha a CloudConfigurationManager csomagra kíván hivatkozni:

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

Az Azure Configuration Manager használata nem kötelező. Használhat egy API-t is, például a .NET-keretrendszer [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) osztályát.
