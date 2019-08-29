---
title: Virtuális gép üzembe helyezése Resource Manager C# -sablonnal | Microsoft Docs
description: Útmutató az Azure-beli C# virtuális gépek üzembe helyezéséhez és Resource Manager-sablonok használatához.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 65ce7711786e15a5455d91ce829a3bc0bdf4317d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103228"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Azure C# -beli virtuális gép üzembe helyezése Resource Manager-sablonnal

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Resource Manager- C#sablont a használatával. A létrehozott sablon egy Windows Server rendszert futtató virtuális gépet telepít egyetlen alhálózattal rendelkező új virtuális hálózatban.

A virtuális gép erőforrásának részletes ismertetését lásd: [virtuális gépek egy Azure Resource Manager sablonban](template-description.md). További információ a sablon összes erőforrásáról: [Azure Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Ezek a lépések körülbelül 10 percet vesznek igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ebben a lépésben meg kell győződnie arról, hogy a Visual Studio telepítve van, és létrehoz egy, a sablon üzembe helyezéséhez használt konzol alkalmazást.

1. Ha még nem tette meg, telepítse a [Visual studiót](https://docs.microsoft.com/visualstudio/install/install-visual-studio). A munkaterhelések lapon válassza ki a **.net Desktop Development** elemet, majd kattintson a **telepítés**gombra. Az összegzésben láthatja, hogy a **.NET-keretrendszer 4 – 4,6 fejlesztői eszközei** automatikusan ki vannak választva. Ha már telepítette a Visual studiót, a .NET-munkaterhelést a Visual Studio Launcher használatával adhatja hozzá.
2. A Visual Studióban kattintson a **File (Fájl)**  > **New (Új)**  > **Project (Projekt)** parancsra.
3. A **sablonok** > **vizualizáció C#** területén válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, írja be a *myDotnetProject* nevet a projekt neveként, válassza ki a projekt helyét, majd kattintson **az OK**gombra.

## <a name="install-the-packages"></a>A csomagok telepítése

A NuGet csomagok a legegyszerűbben a lépések végrehajtásához szükséges kódtárak telepítésére szolgálnak. A Visual Studióban szükséges kódtárak beszerzéséhez hajtsa végre a következő lépéseket:

1. Kattintson az **eszközök** > **Nuget csomagkezelő**elemre, majd a **Package Manager konzol**elemre.
2. Írja be ezeket a parancsokat a konzolba:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben létrehoz egy sablonfájlt, amely központilag telepíti az erőforrásokat és a paramétereket tartalmazó fájlt, amely a sablonhoz paramétereket szolgáltat. Emellett Azure Resource Manager műveletek végrehajtásához használt engedélyezési fájlt is létrehoz.

### <a name="create-the-template-file"></a>Sablonfájl létrehozása

1. A Megoldáskezelőban kattintson a jobb gombbal a *myDotnetProject* > **új elem** **hozzáadása** > lehetőségre, majd válassza a **szövegfájl** *elemet a vizuális C# elemekben*. Nevezze el az *CreateVMTemplate. JSON*fájlt, majd kattintson a **Hozzáadás**gombra.
2. Adja hozzá ezt a JSON-kódot a létrehozott fájlhoz:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Mentse a CreateVMTemplate. JSON fájlt.

### <a name="create-the-parameters-file"></a>A Parameters fájl létrehozása

A sablonban lévő erőforrás-paraméterek értékének megadásához létre kell hoznia egy paramétereket tartalmazó fájlt, amely tartalmazza az értékeket.

1. A Megoldáskezelőban kattintson a jobb gombbal a *myDotnetProject* > **új elem** **hozzáadása** > lehetőségre, majd válassza a **szövegfájl** *elemet a vizuális C# elemekben*. Nevezze el a file *Parameters. JSON*fájlt, majd kattintson a **Hozzáadás**gombra.
2. Adja hozzá ezt a JSON-kódot a létrehozott fájlhoz:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Mentse a Parameters. JSON fájlt.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

A sablon üzembe helyezése előtt győződjön meg arról, hogy van hozzáférése egy [Active Directory egyszerű szolgáltatáshoz](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Az egyszerű szolgáltatástól kapott tokent a kérelmek Azure Resource Manager való hitelesítéséhez. Jegyezze fel az alkalmazás AZONOSÍTÓját, a hitelesítési kulcsot és a bérlő AZONOSÍTÓját is, amelyet az engedélyezési fájlban kell megadnia.

1. A Megoldáskezelőban kattintson a jobb gombbal a *myDotnetProject* > **új elem** **hozzáadása** > lehetőségre, majd válassza a **szövegfájl** *elemet a vizuális C# elemekben*. Nevezze el a *azureauth. properties*fájlt, majd kattintson a **Hozzáadás**gombra.
2. Adja hozzá az alábbi engedélyezési tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Cserélje le  **&lt;az előfizetés&gt; -azonosítót** az előfizetési azonosítóra,  **&lt;az Application-ID&gt; -** t a Active Directory alkalmazás-azonosítóra, **&lt;a hitelesítési kulcsra az&gt;** alkalmazás kulcsa **és&lt;abérlő&gt;** azonosítója a bérlő azonosítójával.

3. Mentse a azureauth. properties fájlt.
4. Állítson be egy környezeti változót a Windows nevű AZURE_AUTH_LOCATION a létrehozott hitelesítési fájl teljes elérési útjával, például használhatja a következő PowerShell-parancsot:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekthez tartozó Program.cs-fájlt. Ezután adja hozzá a következő utasításokat a fájl elejéhez a meglévő utasításokhoz:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a Main metódushoz:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az alkalmazás értékeinek megadásához adja hozzá a kódot a Main metódushoz:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A sablon és a paraméterek üzembe helyezése egy Azure-beli Storage-fiókból történik. Ebben a lépésben létrehozza a fiókot, és feltölti a fájlokat. 

A fiók létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Telepítse a sablont és a paramétereket a létrehozott Storage-fiókból. 

A sablon üzembe helyezéséhez adja hozzá ezt a kódot a Main metódushoz:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Az erőforrások törlése

Mivel az Azure-ban használt erőforrásokért kell fizetnie, mindig érdemes törölni a már nem szükséges erőforrásokat. Nem kell minden erőforrást külön törölni az erőforráscsoporthoz. Törölje az erőforráscsoportot, és minden erőforrását automatikusan törli a rendszer. 

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Main metódushoz:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe ahhoz, hogy a konzol alkalmazás teljes körűen fusson az elejétől a végéig. 

1. A konzol alkalmazás futtatásához kattintson a **Start**gombra.

2. Mielőtt megnyomja az **ENTER** billentyűt az erőforrások törlésének megkezdéséhez, eltarthat néhány percig, hogy ellenőrizze az erőforrások létrehozását a Azure Portalban. A központi telepítésre vonatkozó információk megtekintéséhez kattintson a központi telepítés állapotára.

## <a name="next-steps"></a>További lépések

* Ha probléma merült fel az üzembe helyezés során, a következő lépés az [Azure telepítési hibáinak elhárítása a Azure Resource Manager](../../resource-manager-common-deployment-errors.md)használatával.
* Megtudhatja, hogyan helyezhet üzembe egy virtuális gépet és annak támogató erőforrásait az Azure-beli [virtuális gépek C#a használatával történő üzembe helyezésének ](csharp.md)áttekintésével.