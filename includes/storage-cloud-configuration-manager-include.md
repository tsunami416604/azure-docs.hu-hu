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

