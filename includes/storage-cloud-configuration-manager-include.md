A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati karakterláncok elemzéséhez. A [CloudConfigurationManager osztály](https://msdn.microsoft.com/library/azure/mt634650.aspx) konfigurációs beállításokat elemez, függetlenül attól, hogy az ügyfélalkalmazás az asztalon, egy mobileszközön, egy Azure virtuális gépen vagy egy Azure-felhőszolgáltatásban fut-e.

Adja a következő `using` utasítást az osztályhoz, ha a CloudConfigurationManager csomagra kíván hivatkozni:

    using Microsoft.Azure;  //Namespace for CloudConfigurationManager

A következő példa bemutatja, hogyan kérhető le egy kapcsolati karakterlánc egy konfigurációs fájlból:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Az Azure Configuration Manager használata nem kötelező. Használhat egy API-t is, például a .NET-keretrendszer [ConfigurationManager osztályát](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx).



<!--HONumber=Jun16_HO2-->


