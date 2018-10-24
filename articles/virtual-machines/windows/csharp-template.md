---
title: Virtuális gép üzembe helyezése a C# és a egy Resource Manager-sablon használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használható a C# és a egy Resource Manager-sablon üzembe helyezéséhez Azure virtuális gép.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
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
ms.author: zarhoads
ms.openlocfilehash: f3d83fa8ba5a78c7aacc7014e9ec50fb447bb1c6
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953688"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# és a egy Resource Manager-sablon használatával, egy Azure virtuális gép üzembe helyezése
Ez a cikk bemutatja, hogyan helyezhet üzembe Azure Resource Manager-sablon, C# használatával. A sablon által létrehozott telepít egy új virtuális hálózatot egyetlen alhálózattal rendelkező Windows Server rendszerű egyetlen virtuális gépet.

A virtuális gép típusú erőforrást részletes ismertetését lásd: [egy Azure Resource Manager-sablonban virtuális gépek](template-description.md). A sablon összes erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Nagyjából 10 percet elvégezheti ezeket a lépéseket vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ebben a lépésben, győződjön meg arról, hogy telepítve van a Visual Studio és a sablon üzembe helyezéséhez használt Konzolalkalmazás létrehozása.

1. Ha még nem tette, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **.NET asztali fejlesztés** a számítási feladatok lapján, és kattintson a **telepítése**. A fentieket összegezve láthatja, hogy **.NET-keretrendszer 4-4.6 Fejlesztőeszközök** automatikusan ki van jelölve az Ön számára. Ha már telepítette a Visual Studio, a .NET-munkaterhelés használatával a Visual Studio indítója is hozzáadhat.
2. A Visual Studióban kattintson a **File (Fájl)** > **New (Új)** > **Project (Projekt)** parancsra.
3. A **sablonok** > **Visual C#** válassza **Console App (.NET Framework)**, adja meg *myDotnetProject* neve a projektre, válassza ki a projekt helyét, majd kattintson **OK**.

## <a name="install-the-packages"></a>A csomagok telepítése

NuGet-csomagok a legegyszerűbben úgy, hogy először fejezze be a következő lépéseket a kódtárak telepítése. A Visual Studio szükséges kódtárakat, tegye ezeket a lépéseket:

1. Kattintson a **eszközök** > **Nuget-Csomagkezelő**, és kattintson a **Package Manager Console**.
2. Írja be ezeket a parancsokat a konzolon:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben hozzon létre egy sablon fájlt, amely üzembe helyezi az erőforrásokat és a egy paramétereket tartalmazó fájlt, amely a sablonhoz paraméter értékeket. Emellett létrehozhat egy Azure Resource Manager-műveletek végrehajtásához használt engedélyezési fájl.

### <a name="create-the-template-file"></a>A sablonfájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C#-elemek*. A fájl neve *CreateVMTemplate.json*, és kattintson a **Hozzáadás**.
2. A JSON-kód hozzáadása a létrehozott fájlt:

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

### <a name="create-the-parameters-file"></a>A paraméterfájl létrehozása

Adja meg a sablonban definiált erőforrás-paraméterek értékeit, hozzon létre egy paramétereket tartalmazó fájlt, az értékek.

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C#-elemek*. A fájl neve *Parameters.json*, és kattintson a **Hozzáadás**.
2. A JSON-kód hozzáadása a létrehozott fájlt:

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

4. Mentse a Parameters.json fájlban.

### <a name="create-the-authorization-file"></a>Az engedélyezési-fájl létrehozása

Sablon központi telepítése, győződjön meg arról, hogy hozzáféréssel rendelkezik-e egy [Active Directory egyszerű szolgáltatás](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). A szolgáltatásnévből szerez be a jogkivonatot az Azure Resource Manager-kérelmek hitelesítéséhez. Az Alkalmazásazonosítót, a hitelesítési kulcsot és a bérlő Azonosítóját, a hitelesítési fájlhoz szükséges is kell rögzíteni.

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C#-elemek*. A fájl neve *azureauth.properties*, és kattintson a **Hozzáadás**.
2. Adja hozzá ezeket a hitelesítési tulajdonságokat:

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

    Cserélje le **&lt;előfizetés-azonosító&gt;** az előfizetés-azonosítójú **&lt;alkalmazásazonosító&gt;** az Active Directory-alkalmazással azonosító, **&lt;hitelesítési kulcs&gt;** az alkalmazás kulccsal és **&lt;bérlőazonosító&gt;** a bérlői azonosító.

3. Mentse a azureauth.properties fájlt.
4. A fájl teljes elérési útja engedélyezési létrehozott, például a következő parancs használható PowerShell AZURE_AUTH_LOCATION nevű Windows környezeti változóban állíthatja be:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekt a Program.cs fájlt, és adja hozzá ezek using utasításokat a meglévő utasításokat a fájl felül:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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

Az alkalmazás értékek megadásához adja hozzá a kódot a Main metódushoz:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A sablon és paraméterek vannak telepítve az Azure storage-fiókból. Ebben a lépésben hozza létre a fiókot, és töltse fel a fájlokat. 

A fiók létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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

Telepítse a sablon és paraméterek létrehozott storage-fiókból. 

A sablon üzembe helyezéséhez, adja hozzá ezt a kódot a Main metódushoz:

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

Mivel az Azure-ban használt erőforrások díjkötelesek, mindig érdemes mindig törölheti az erőforrást, amely már nincs rá szükség. Nem kell minden egyes erőforrás külön-külön töröl egy erőforráscsoportot. Törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást a rendszer automatikusan töröl. 

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Main metódushoz:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nagyjából öt perc alatt az a Konzolalkalmazás futtatása teljesen le kell vennie a befejezéshez. 

1. Futtassa a konzolalkalmazást, kattintson a **Start**.

2. Mielőtt lenyomja **Enter** indítása erőforrás törlése, eltarthat néhány percig, ellenőrizze az az Azure Portalon az erőforrások létrehozását. Kattintson a telepítés állapota, az üzembe helyezéssel kapcsolatos információk megtekintéséhez.

## <a name="next-steps"></a>További lépések
* Ha a központi telepítési problémái vannak, és tekintse meg a következő lépésben lesz [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Ismerje meg, hogyan helyezhet üzembe egy virtuális gép és a támogató erőforrások áttekintésével [üzembe helyezése egy Azure virtuális gép használata a C#](csharp.md).
