---
title: Virtuális gép központi telepítése C# és Erőforrás-kezelő sablon használatával
description: Ismerje meg, hogyan használhatja a C# és a Resource Manager-sablon azure-beli virtuális gép üzembe helyezéséhez.
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
ms.openlocfilehash: 6d99c5ae91b80b9b6b9af08001b3a7c57bc7ca8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944531"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Azure virtuális gép üzembe helyezése C# és Erőforrás-kezelő sablon használatával

Ez a cikk bemutatja, hogyan telepíthet egy Azure Resource Manager-sablont a C#használatával. A létrehozott sablon egyetlen, Windows Server rendszert futtató virtuális gépet telepít egyetlen alhálózattal rendelkező új virtuális hálózatban.

A virtuálisgép-erőforrás részletes leírását az [Azure Resource Manager-sablonban található virtuális gépek című témakörben olvashatja.](template-description.md) A sablonösszes erőforrásáról az Azure [Resource Manager sablonforgatókönyv-forgatókönyve című témakörben olvashat bővebben.](../../azure-resource-manager/resource-manager-template-walkthrough.md)

Ezeket a lépéseket körülbelül 10 percalatt elkell végezni.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ebben a lépésben győződjön meg arról, hogy a Visual Studio telepítve van, és hozzon létre egy konzolalkalmazást a sablon központi telepítéséhez.

1. Ha még nem tette meg, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)alkalmazást. A Számítási feladatok lapon válassza a **.NET asztal fejlesztését,** majd kattintson a **Telepítés gombra.** Az összegzésben láthatja, hogy a **.NET Framework 4 - 4.6 fejlesztői eszközök** automatikusan kiválasztásra kerülnek. Ha már telepítette a Visual Studio alkalmazást, a .NET-számítási feladatot hozzáadhatja a Visual Studio launcher használatával.
2. A Visual Studio programban kattintson az**Új** > **projekt** **fájlja** > gombra.
3. A Templates Visual C# **(Sablonok vizuális** >  **Console App (.NET Framework)****csz.** *myDotnetProject* **OK**

## <a name="install-the-packages"></a>A csomagok telepítése

A NuGet csomagok a legegyszerűbb módja a lépések végrehajtásához szükséges könyvtárak telepítésének. A Visual Studio alkalmazásban szükséges könyvtárak leeléréséhez tegye a következő lépéseket:

1. Kattintson **az Eszközök** > **Nuget csomagkezelő ikonra,** majd a **Csomagkezelő konzol parancsra.**
2. Írja be ezeket a parancsokat a konzolba:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben létrehoz egy sablonfájlt, amely telepíti az erőforrásokat és egy paraméterfájlt, amely paraméterértékeket ad a sablonnak. Az Azure Resource Manager-műveletek végrehajtásához használt engedélyezési fájlt is létrehozhat.

### <a name="create-the-template-file"></a>A sablonfájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *a myDotnetProject* > **Új elem****hozzáadása** > elemre, majd válassza a **Szövegfájl parancsot** a *Visual C# elemekben.* Nevezze el a *CreateVMTemplate.json*fájlt, majd kattintson a **Hozzáadás**gombra.
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

3. Mentse a CreateVMTemplate.json fájlt.

### <a name="create-the-parameters-file"></a>A paraméterfájl létrehozása

A sablonban az erőforrás-paraméterek értékeinek megadásához hozzon létre egy paraméterfájlt, amely tartalmazza az értékeket.

1. A Megoldáskezelőben kattintson a jobb gombbal *a myDotnetProject* > **Új elem****hozzáadása** > elemre, majd válassza a **Szövegfájl parancsot** a *Visual C# elemekben.* Nevezze el a *fájlt Parameters.json fájlnak,* majd kattintson a **Hozzáadás gombra.**
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

4. Mentse a Parameters.json fájlt.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

Mielőtt telepítene egy sablont, győződjön meg arról, hogy rendelkezik hozzáféréssel egy [Active Directory egyszerű szolgáltatáshoz.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) Az egyszerű szolgáltatásból beszerezhet egy jogkivonatot az Azure Resource Manager kérelmek hitelesítéséhez. Az engedélyezési fájlban rögzítenie kell az alkalmazásazonosítót, a hitelesítési kulcsot és a szükséges bérlői azonosítót is.

1. A Megoldáskezelőben kattintson a jobb gombbal *a myDotnetProject* > **Új elem****hozzáadása** > elemre, majd válassza a **Szövegfájl parancsot** a *Visual C# elemekben.* Nevezze el a fájlt *azureauth.properties*, majd kattintson **a Hozzáadás gombra.**
2. Adja hozzá ezeket az engedélyezési tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Cserélje le ** &lt;az&gt; előfizetés-id-t** az előfizetés-azonosítóra, ** &lt;az alkalmazásazonosítót&gt; ** az Active Directory alkalmazásazonosítóra, ** &lt;&gt; ** a hitelesítési kulcsot az alkalmazáskulcsra és ** &lt;a bérlőazonosítót&gt; ** a bérlői azonosítóra.

3. Mentse az azureauth.properties fájlt.
4. Állítson be egy AZURE_AUTH_LOCATION nevű környezeti változót a Windows rendszerben a létrehozott engedélyezési fájl teljes elérési útjával, például a következő PowerShell-paranccsal:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekt Program.cs fájlját. Ezután adja hozzá ezeket a fájl tetején lévő utasításokhoz a következő utasítások használatával:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a fő metódushoz:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az alkalmazás értékeinek megadásához adjon kódot a Fő metódushoz:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Create a storage account

A sablon és a paraméterek az Azure-beli tárfiókból vannak telepítve. Ebben a lépésben hozza létre a fiókot, és töltse fel a fájlokat. 

A fiók létrehozásához adja hozzá ezt a kódot a Fő metódushoz:

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

Telepítse a sablont és a paramétereket a létrehozott tárfiókból. 

A sablon központi telepítéséhez adja hozzá ezt a kódot a fő metódushoz:

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

Mivel az Azure-ban használt erőforrásokért díjat számítunk fel, mindig ajánlott törölni azokat az erőforrásokat, amelyekre már nincs szükség. Nem kell minden erőforrást az erőforráscsoporttól elkülönítve törölnie. Törölje az erőforráscsoportot, és az összes erőforrása automatikusan törlődik. 

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Fő metódushoz:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe, amíg ez a konzolalkalmazás teljesen elindul az elejétől a végéig. 

1. A konzolalkalmazás futtatásához kattintson a **Start**gombra.

2. Mielőtt az **Enter** billentyű lenyomásával megszeretné kezdeni az erőforrások törlését, eltarthat néhány percet az erőforrások létrehozásának ellenőrzése az Azure Portalon. Kattintson a központi telepítés állapotára a központi telepítéssel kapcsolatos információk megtekintéséhez.

## <a name="next-steps"></a>További lépések

* Ha problémák merültek fel a központi telepítéssel kapcsolatban, a következő lépés az [Azure Resource Manager gyakori Azure-telepítési hibák elhárítása című](../../resource-manager-common-deployment-errors.md)témakörben található.
* Ismerje meg, hogyan helyezhet üzembe egy virtuális gépet és a támogató erőforrásokat [az Azure virtuális gép c# használatával történő üzembe helyezése áttekintésével.](csharp.md)