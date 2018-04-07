---
title: A virtuális gép C# és a Resource Manager-sablon üzembe helyezése |} Microsoft Docs
description: Ismerje meg, hogy C# és a Resource Manager-sablon használata az Azure virtuális gép.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: davidmu
ms.openlocfilehash: f2c488a891d1037535f1782da54544ac6af9dd41
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Egy Azure virtuális gépen a C# és a Resource Manager-sablon telepítése
Ez a cikk bemutatja, hogyan telepítheti egy Azure Resource Manager-sablon használatával C#. A sablon az Ön által létrehozott telepít egy új virtuális hálózatot egyetlen alhálózattal Windows Server rendszerű egyetlen virtuális gépet.

A virtuálisgép-erőforrás részletes ismertetését lásd: [Azure Resource Manager-sablonokban a virtuális gépek](template-description.md). A sablon összes erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

A lépések elvégzéséhez körülbelül 10 percet vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ebben a lépésben biztosíthatja, hogy telepítve van a Visual Studio és a sablon telepítéséhez használt Konzolalkalmazás létrehozása.

1. Ha még nem tette meg, telepítse [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **.NET asztali fejlesztési** a munkaterhelések lapot, és kattintson a **telepítése**. Az összegzés láthatja, hogy **.NET-keretrendszer 4-4.6 Fejlesztőeszközök** automatikusan ki van jelölve meg. Ha már telepítette a Visual Studio, a .NET alkalmazások és szolgáltatások segítségével a Visual Studio indítója adhat meg.
2. A Visual Studióban kattintson **fájl** > **új** > **projekt**.
3. A **sablonok** > **Visual C#**, jelölje be **Konzolalkalmazás (.NET-keretrendszer)**, adja meg *myDotnetProject* a projekt nevét, válassza ki a projekt helyét, és kattintson **OK**.

## <a name="install-the-packages"></a>A csomagok telepítése

NuGet-csomagok használata a lépések végrehajtása szükséges kódtárak telepítése a legegyszerűbb. Ahhoz, hogy a Visual Studio szükséges könyvtárak, hajtsa végre ezeket a lépéseket:

1. Kattintson a **eszközök** > **Nuget-Csomagkezelő**, és kattintson a **Csomagkezelő konzol**.
2. Ezek a parancsok a konzol írja be:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben létrehoz egy sablonfájlban, amely telepíti az erőforrások és paraméterfájl, amely megadja az paraméterértékeket a sablonhoz. Is létrehozhat egy engedélyezési fájlt, amely az Azure Resource Manager műveletek végrehajtásához használatos.

### <a name="create-the-template-file"></a>A sablonfájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C# elemek*. A fájl neve *CreateVMTemplate.json*, és kattintson a **Hozzáadás**.
2. A JSON-kódot a létrehozott fájl hozzáadása:

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

3. Mentse a CreateVMTemplate.json fájlt.

### <a name="create-the-parameters-file"></a>A paraméterek fájl létrehozása

Adja meg a sablonban definiált erőforrás-paraméterek értékei, hozzon létre paraméterfájl, amely értékeket tartalmazza.

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C# elemek*. A fájl neve *Parameters.json*, és kattintson a **Hozzáadás**.
2. A JSON-kódot a létrehozott fájl hozzáadása:

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

4. A Parameters.json fájl mentéséhez.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

A sablon telepítése előtt győződjön meg arról, hogy rendelkezik-e a hozzáférést egy [Active Directory szolgáltatás egyszerű](../../resource-group-authenticate-service-principal.md). A szolgáltatás egyszerű szerez be egy tokent, az Azure Resource Manager kérelmek hitelesítéséhez. Az Alkalmazásazonosító, a hitelesítési kulcs és a hitelesítési fájlhoz szükséges bérlői azonosító is kell rögzítenie.

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C# elemek*. A fájl neve *azureauth.properties*, és kattintson a **Hozzáadás**.
2. Adja hozzá a engedélyezési tulajdonságok:

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

    Cserélje le **&lt;előfizetés-azonosító&gt;** az előfizetés-azonosítóval rendelkező **&lt;alkalmazásazonosító&gt;** való a Active Directory-azonosítót, **&lt;hitelesítési kulcs&gt;** az alkalmazás kulccsal és **&lt;bérlőazonosító&gt;** a bérlő azonosítója.

3. Mentse a azureauth.properties fájlt.
4. A fájl teljes elérési útja engedélyezési létrehozott, például a következő parancs használható PowerShell AZURE_AUTH_LOCATION nevű Windows környezeti változó értéke:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a Program.cs fájlra a projekthez létrehozott, és adja hozzá ezeket a fájl felső utasítást, hogy a meglévő utasítás használatával:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Hozzon létre a felügyeleti ügyfél, vegye fel ezt a kódot a fő metódus:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az alkalmazás értékek megadásához adja hozzá a fő metódus kódot:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Create a storage account

A sablon és a paraméterek az Azure-ban tárfiókból vannak telepítve. Ebben a lépésben a fiók létrehozásához, és töltse fel a fájlokat. 

Hozzon létre a fiókot, vegye fel ezt a kódot a fő metódus:

```
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

Telepítse a sablon és a paraméterek létrehozott tárfiókból. 

A sablon telepítéséhez, adja hozzá ezt a kódot a fő metódus:

```
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

Mivel az Azure-ban használt erőforrásokhoz van szó, ajánlott mindig törli az erőforrást, amely már nem szükséges. Nem kell törölnie az egyes erőforrások külön-külön egy erőforráscsoportból. Törölje a csoportot, és a hozzá tartozó összes erőforrásnak automatikusan törlődnek. 

Törölje a csoportot, vegye fel ezt a kódot a fő metódus:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Öt perc a konzol alkalmazás teljesen futtatásához indítás kell vennie a befejezéshez. 

1. Futtassa a konzolalkalmazást, kattintson a **Start**.

2. Ahhoz, hogy nyomja le az ENTER **Enter** erőforrások törlése elindításához eltarthat néhány percig az Azure-portálon az erőforrások létrehozásának ellenőrzése. Kattintson a telepítés állapota a telepítéssel kapcsolatos információk megjelenítéséhez.

## <a name="next-steps"></a>További lépések
* Ha problémák merültek fel a központi telepítést, a következő lépésben lesz lássunk [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Megtudhatja, hogyan telepítheti a virtuális gép és annak támogató forrásokat megtekintésével [központi telepítése egy Azure virtuális gép használata a C#](csharp.md).
