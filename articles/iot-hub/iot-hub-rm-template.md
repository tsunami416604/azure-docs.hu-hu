<properties
    pageTitle="使用資源管理員範本建立 IoT 中樞 |Microsoft Azure"
    description="您可以依照此教學課程的說明，開始使用資源管理員範本建立 IoT 中樞。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# 教學課程：使用 C# 程式建立 IoT 中樞

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 簡介

您可以使用 Azure 資源管理員，以程式設計方式建立和管理 Azure IoT 中樞。 本教學課程示範如何使用資源管理員範本從 C# 程式建立 IoT 中樞。

若要完成本教學課程，您需要下列項目：

- Microsoft Visual Studio 2015。
- 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用] [任務任務連結-免費試用]。
- [Microsoft Azure PowerShell 1.0][任務任務連結-powershell 安裝的] 或更新版本。

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## 準備 Visual Studio 專案

1. 在 Visual Studio 中建立新的 Visual C# Windows 專案使用 **主控台應用程式** 專案範本。 將專案命名為 **CreateIoTHub**。

2. 在 [方案總管] 中，您的專案上按一下滑鼠右鍵，然後按一下 [ **管理 NuGet 封裝**。

3. 在 [NuGet 封裝管理員] 中，檢查 **包括發行前版本** ，並搜尋 **Microsoft.Azure.Management.Resources**。 選取版本 **2.18.11-preview**。 按一下 [ **安裝**, 中 **檢閱變更** 按一下 **確定**, ，然後按一下 [ **我接受** 以接受授權。

4. 在 [NuGet 封裝管理員] 中，搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory**。 選取版本 **2.19.208020213**。 按一下 [ **安裝**, 中 **檢閱變更** 按一下 **確定**, ，然後按一下 [ **我接受** 接受授權。

5. 在 [NuGet 封裝管理員] 中，搜尋 **Microsoft.Azure.Common**。 選取版本 **2.1.0**。 按一下 [ **安裝**, 中 **檢閱變更** 按一下 **確定**, ，然後按一下 [ **我接受** 以接受授權。

6. 在 Program.cs 中，取代現有 **使用** 取代為下列陳述式:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. 在 Program.cs 中，以下列靜態變數取代預留位置值。 您記下的 **ApplicationId**, ，**SubscriptionId**, ，**TenantId**, ，和 **密碼** 早在本教學課程。 **資源群組名稱** 名稱建立 IoT 中心時，您將使用的資源群組，它可以是預先存在的資源群組或新的。 **IoT 中心名稱** IoT 中心，您將建立，例如名稱 **MyIoTHub**。 **部署名稱** 是部署的名稱，例如 **Deployment_01**。

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## 提交範本，以建立 IoT 中樞

使用 JSON 範本，在資源群組中建立新的 IoT 中樞。 您也可以使用範本變更現有的 IoT 中樞。

1. 在 [方案總管] 中，您的專案上按一下滑鼠右鍵，然後按一下 **新增** 然後 **新項目**。 加入新的 JSON 檔案，稱為 **template.json** 至您的專案。

2. 在 [方案總管] 中，選取 [ **template.json**, ，然後在 **屬性** 設定 **複製到輸出目錄** 至 **永遠複製**。

3. 內容取代 **template.json** 使用下列資源定義加入至新的標準 IoT 中心 **美國東部** 區域:

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. 將下列方法新增至 Program.cs：
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 加入下列程式碼以 **CreateIoTHub** 方法來載入範本檔案，新增的 IoT 中心名稱，然後送出至 Azure 資源管理員範本:

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. 加入下列程式碼以 **CreateIoTHub** 會等到部署順利完成的方法:

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## 完成並執行應用程式

您現在可以完成應用程式藉由呼叫 **CreateIoTHub** 和 **ShowIoTHubKeys** 方法才能建置及執行它。

1. 下列程式碼新增至結尾 **Main** 方法:

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. 按一下 [ **建置** 然後 **建置方案**。 更正所有錯誤。

3. 按一下 [ **偵錯** 然後 **開始偵錯** 執行應用程式。 可能需要數分鐘的時間，部署才會開始執行。

4. 您可以驗證您的應用程式會新增新的 IoT 中心，瀏覽 [網站] [任務任務連結 azure-入口網站]，並檢視清單中的資源，或使用 **Get AzureRmResource** PowerShell cmdlet。

> [AZURE.NOTE] 此範例應用程式加入的計費 S1 標準的 IoT 中心。 您可以刪除 IoT 中心透過 [網站] [任務任務連結 azure-入口網站] 或使用 **移除 AzureRmResource** PowerShell 指令程式完成時。

## 後續步驟

- 瀏覽 [IoT 中心資源提供者 REST API] [任務任務連結-rest api 的] 的功能。
- 閱讀 [Azure 資源管理員概觀] [任務任務連結-azure-rm-概觀] 若要進一步了解 Azure 資源管理員的功能。

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/en-us/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

