<properties
 pageTitle="Excel 和 SOA 適用的 HPC Pack 叢集 | Microsoft Azure"
 description="使用資源管理員部署模型，開始使用 HPC Pack 叢集以執行 Excel 和 SOA 工作負載。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="11/11/2015"
 ms.author="danlep"/>


# 開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載

本文將說明如何在 Azure 基礎結構服務 (IaaS) 上使用 Azure 快速入門範本或 Azure PowerShell 部署指令碼部署 HPC Pack 叢集。 您將使用 Azure Marketplace VM 映像，其設計目的為使用 HPC Pack 執行 Microsoft Excel 或服務導向架構 (SOA) 工作負載。 您可以從內部部署用戶端電腦使用叢集來執行簡單的 Excel HPC 和 SOA 服務。 Excel HPC 服務包括 Excel 活頁簿卸載和 Excel 使用者定義函數或 UDF。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


在較高層級上，下圖顯示您將建立的 HPC Pack 叢集。

![HPC 叢集與執行 Excel 工作負載的節點][scenario]

## 必要條件

* **用戶端電腦** - 您需要 Windows 用戶端電腦才能執行 Azure PowerShell 叢集部署指令碼 (如果您選擇該部署方法) 以及提交範例 Excel 和 SOA 工作至叢集。

* **Azure 訂用帳戶** - 如果您沒有帳戶，僅需幾分鐘就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* **核心配額** - 您可能需要增加核心的配額，特別是如果您選擇部署多核心 VM 大小的數個叢集節點。 如果您使用 Azure 快速入門範本，請注意資源管理員中的核心配額為每個 Azure 區域，您可能需要增加特定區域中的配額。 請參閱 [Azure 訂用帳戶限制、 配額和條件約束](../azure-subscription-service-limits.md)。 若要增加配額，您可以 [開啟線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。


## 步驟 1.在 Azure 中設定 HPC Pack 叢集

我們將說明設定叢集的兩種方式：第一，使用 Azure 快速入門範本和 Azure 入口網站；第二，使用 Azure PowerShell 部署指令碼。


### 使用快速入門範本

使用 Azure 快速入門範本在 Azure 入口網站中快速、輕鬆地部署 HPC Pack 叢集。 在 Preview 入口網站中開啟範本時，您會看到一個可供您輸入叢集設定的簡單 UI。 步驟如下：

1. 請瀏覽 [GitHub 上的建立 HPC 叢集範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)。 您可根據意願檢視範本和原始碼的相關資訊。

2. 按一下 [部署至 Azure]****，以在 Azure 入口網站中利用範本開始部署。

    ![將範本部署到 Azure][github]

3. 在 Preview 入口網站中，依照下列步驟輸入 HPC 叢集範本的參數。

    a. 在 [**編輯範本**] 頁面上，按一下 [**儲存**]。

    ![儲存範本][template]

    b. 在 [參數]**** 頁面上，輸入範本參數的值。 (按一下說明資訊的每個設定旁邊的圖示。) 下列畫面顯示範例值。 此範例會在 *hpc.local* 網域中建立名為 *hpc01* 的新 HPC Pack 叢集，由一個前端節點和 2 個運算節點組成。 運算節點將會從 HPC Pack VM 映像建立，包括 Microsoft Excel。

    ![輸入參數][parameters]
    >[AZURE.NOTE]前端節點 VM 就會自動建立從 [最新 Marketplace 映像](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) 的 Windows Server 2012 R2 上的 HPC Pack 2012 R2。 目前此映像以 HPC Pack 2012 R2 Update 3 為基礎。
    >
    >運算節點 VM 會從選取之運算節點系列的最新映像建立。 選取 **ComputeNode** 選項做為一般用途的最新 HPC Pack 2012 R2 Update 3 計算映像。 選取 **ComputeNodeWithExcel** 選項做為最新的 HPC Pack 運算節點映像，包含評估版 Microsoft Excel Professional Plus 2013。 如果您想要部署一般 SOA 工作階段或 Excel UDF 卸載的叢集，請選擇 **ComputeNode** 選項 (不需安裝 Excel)。
    >
    >當使用  **ComputeNodeWithExcel** 生產工作負載，您必須提供有效的 Excel 授權運算節點上啟動 Excel。 否則，Excel 評估版會在 30 天內到期，且執行 Excel 活頁簿會不斷失敗並出現 COMExeption (0x800AC472)。 如果發生這種情況，您可登入前端節點，透過 [HPC 叢集管理員] 主控台在所有 Excel 運算節點上 clusrun “%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe”，進而重設 Excel 的授權狀態以獲得另外 30 天的評估時間。 寬限期的重設授權狀態時間上限為 2，之後您可能需要提供有效的 Excel 授權。

    c. 選擇訂用帳戶。

    d. 建立叢集的新資源群組，例如 *hpc01RG*。

    e. 選擇資源群組的位置，例如美國東部。

    f. 在 [**法律條款**] 頁面上檢閱條款。 如果您同意，請按一下 [**購買**]。

    g. 當您完成設定範本的值，請按一下 [**建立**]。

    ![建立叢集][create]

3.  當部署完成時 (通常需要約 30 分鐘)，從叢集前端節點匯出叢集憑證檔。 在稍後的步驟中，此公開憑證將在用戶端電腦上匯入以提供安全 HTTP 繫結的伺服器端驗證。

    a. 從 Azure 入口網站透過「遠端桌面」連線到前端節點。

     ![連接至前端節點][connect]

    b. 使用標準程序來使用憑證管理員匯出前端節點憑證 (位於 Cert: \LocalMachine\My 之下) 而不需私密金鑰。 在此範例中，匯出 *CN = hpc01.eastus.cloudapp.azure.com*。

    ![匯出憑證][cert]

### 使用 HPC Pack IaaS 部署指令碼

HPC Pack IaaS 部署指令碼提供靈活的另一種方式部署 HPC Pack 叢集。 它會以 Azure 服務管理 (ASM) 模式執行，而範本會以 Azure 資源管理員 (ARM) 模式執行。 指令碼也和 Azure 全域或 Azure China 服務中的訂用帳戶相容。

**其他必要條件**

* **PowerShell** - [安裝和設定 Azure PowerShell](../powershell-install-configure.md) (0.8.10 或更新版本) 用戶端電腦上。

* **HPC Pack IaaS 部署指令碼** -下載並解壓縮最新版的指令碼的 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)。 執行檢查的指令碼版本 `New-hpciaascluster.ps1 – 版本`。 這篇文章根據 4.5.0 版或更新版本的指令碼。

**建立組態檔**

 HPC Pack IaaS 部署指令碼會使用 XML 組態檔做為輸入，可描述 HPC 叢集的基礎結構。 若要部署由一個前端節點和從包含 Microsoft Excel 之運算節點映像所建立的 18 個運算節點組成的叢集，請將環境的值取代為下列範例組態檔。 如需組態檔的詳細資訊，請參閱指令碼資料夾中的 Manual.rtf 檔案和 [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**組態檔的相關注意事項**

* 前端節點的 **VMName** **必須**和 **ServiceName** 完全相同，否則 SOA 工作會無法執行。

* 請確定您會指定 **EnableWebPortal**，所以已經產生並匯出前端節點憑證。

* 後組態 PowerShell 指令碼 PostConfig.ps1 會在前端節點上進行某些設定，例如設定 Azure 儲存體連接字串、從前端節點移除運算節點角色，並在部署所有節點時使其上線。 隨後會出現範例指令碼。

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**執行指令碼**

1. 在用戶端電腦上以系統管理員身分開啟 PowerShell 主控台。

2. 將目錄變更為指令碼資料夾 (在本範例中為 E:\IaaSClusterScript)。

    ```
    E:\IaaSClusterScript cd
```

4. Run the command below to deploy the HPC Pack cluster. This example assumes that the configuration file is located in E:\HPCDemoConfig.xml.
```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName

```

The HPC Pack deployment script will run for some time. One thing the script wil do is to export and download the cluster certificate and save it in the current user’s Documents folder on the client computer. The script will generate a message similar to the following. In a following step you'll import the certificate in the appropriate certificate store.
```
您已啟用 REST API 或 web 入口網站，HPC Pack 前端節點上。 請匯入要提交工作或存取 HPC web 入口網站的電腦上的受信任的根憑證授權單位 」 憑證存放區中的下列憑證:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Step 2. Offload Excel workbooks and run UDFs from an on-premises client

### Offload Excel workbooks

Follow these steps to offload an Excel workbook to run on the HPC Pack cluster in Azure. To do this, you must have Excel 2010 or 2013 already installed on the client computer.

1. Use one of the methods in Step 1 to deploy an HPC Pack cluster with the Excel compute node image. Obtain the cluster certificate file (.cer) and cluster username and password.

2. On the client computer, import the cluster certificate under Cert:\CurrentUser\Root.

3. Make sure Excel is installed. Create an Excel.exe.config file with the following contents in the same folder with Excel.exe on the client computer. This ensures that the HPC Pack 2012 R2 Excel COM add-in will be loaded successfully.
```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
</configuration>
```
4.  Download the full [HPC Pack 2012 R2 Update 3 installation](http://www.microsoft.com/download/details.aspx?id=49922) and install the HPC Pack client,
or download and install the [HPC Pack 2012 R2 Update 3 client utilities](https://www.microsoft.com/download/details.aspx?id=49923) and the appropriate Visual C++ 2010 redistributable for your computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  In this example, we use a sample Excel workbook named ConvertiblePricing_Complete.xlsb, available for download [here](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copy the Excel workbook to a working folder such as D:\Excel\Run.

7.  Open the Excel workbook. On the **Develop** ribbon, click **COM Add-Ins** and confirm that the HPC Pack Excel COM add-in is loaded successfully as shown in the following screen.

    ![Excel add-in for HPC Pack][addin]

8.  Edit the VBA macro HPCControlMacros in Excel by changing the commented lines as shown in the following script. Substitute appropriate values for your environment.

    ![Excel macro for HPC Pack][macro]
```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
    
    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
    
    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
    
    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
    
    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"

```

9.  Copy the Excel work book to an upload directory such as D:\Excel\Upload, as specified in the HPC_DependsFiles constant in the VBA macro.

10. Click the **Cluster** button on the worksheet to run the workbook on the Azure IaaS cluster.

### Run Excel UDFs

To run Excel UDFs, follow the preceding steps 1 – 3 to set up the client computer. For Excel UDFs, you don't need to have the Excel application installed on compute nodes, so you could choose a normal compute node image in Step 1 instead of the compute node image with Excel.

>[AZURE.NOTE] There is a 34 character limit in the Excel 2010 and 2013 cluster connector dialog box. If the full cluster name is longer, e.g. hpcexcelhn01.southeastasia.cloudapp.azure.com, it won't fit in the dialog box. The workaround is to set a machine wide variable e.g. *CCP_IAASHN* with the value of the long cluster name and input *%CCP_IAASHN%* in the dialog box as the cluster head node name. Note for Update 2 clusters, it requires the Update 2 QFE KB3085833 (download [here](http://www.microsoft.com/en-us/download/details.aspx?id=48725)) for SOA Session API on the client machine to support this workaround.

After the cluster is successfully deployed, continue with the following steps to run a sample built-in Excel UDF. For customized Excel UDFs, see these [resources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) to build the XLLs and deploy them on the IaaS cluster.

1.  Open a new Excel workbook. On the **Develop** ribbon, click **Add-Ins**. Then, in the dialog box, click **Browse**, navigate to the %CCP_HOME%Bin\XLL32 folder, and select the sample ClusterUDF32.xll. If the ClusterUDF32 doesn't exist on the client machine, you can copy it from the %CCP_HOME%Bin\XLL32 folder on the head node.

    ![Select the UDF][udf]

2.  Click **File** > **Options** > **Advanced**. Under **Formulas** check **Allow user-defined XLL functions to run a compute cluster**. Then click **Options** and enter the full cluster name in **Cluster head node name**. (As noted previously this input box is limited to 34 characters, so a long cluster name may not fit. You may use machine wide variables here for long cluster names.)

    ![Configure the UDF][options]

3.  Click the cell with value =XllGetComputerNameC() and press Enter to run the UDF calculation on the IaaS cluster. The function will simply retrieve the name of the compute node on which the UDF runs. For the first run, a credentials dialog box prompts for the username and password to connect to the IaaS cluster.

    ![Run UDF][run]

    When there are a lot of cells to calculate, press Alt-Shift-Ctrl + F9 to run the calculation on all cells.

## Step 3. Run a SOA workload from an on-premises client

To run general SOA applications on the HPC Pack IaaS cluster, first use one of the methods in Step 1 to deploy the IaaS cluster, using a generic compute node image (because you will not need Excel on the compute nodes). Then follow these steps.

1. After retrieving the cluster certificate, import it on the client computer under Cert:\CurrentUser\Root.

2. Install the [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) and [HPC Pack 2012 R2 Update 3 client utilities](https://www.microsoft.com/download/details.aspx?id=49923) so you can develop and run SOA client applications.

3. Download the HellowWorldR2 [sample code](https://www.microsoft.com/download/details.aspx?id=41633). Open the HelloWorldR2.sln in Visual Studio 2010 or 2012.

4. Build the EchoService project first and deploy the service to the IaaS cluster in the same way you deploy to an on-premises cluster. For detailed steps, see the Readme.doc in HelloWordR2. Modify and build the HellowWorldR2 and other projects as described below to generate the SOA client applications running on an Azure IaaS cluster from an on-premises client computer.

### Use Http binding with Azure storage queue

To use Http binding with an Azure storage queue, make a few changes to the sample code.

* Update the cluster name.
```
之前
const headnode 字串 ="[前端節點]";
之後如
const headnode 字串 ="hpc01.eastus.cloudapp.azure.com 」。
或
const headnode 字串 ="hpc01.cloudapp.net";
```

* Optionally, use default TransportScheme in SessionStartInfo or explicitly set it to Http.
```
    info.TransportScheme = TransportScheme.Http;

```

* Use default binding for the BrokerClient.
```
之前
使用 (BrokerClient<IService1> 用戶端 = 新 BrokerClient<IService1>(工作階段，繫結))
之後
使用 (BrokerClient<IService1> 用戶端 = 新 BrokerClient<IService1>(工作階段))
```

    Or set explicitly using the basicHttpBinding.
```
BasicHttpBinding 繫結 = 新 BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
繫結。Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;   繫結。Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Optionally, set the UseAzureQueue flag to true in SessionStartInfo. If not set, it will be set to true by default when the cluster name has Azure domain suffixes and the TransportScheme is Http.
```
    info.UseAzureQueue = true;

```

###Use Http binding without Azure storage queue

To do this, explicitly set UseAzureQueue flag to false in the SessionStartInfo.
```
    info.UseAzureQueue = false;

```

### 使用 NetTcp 繫結

若要使用 NetTcp 繫結，組態就像是連接至內部部署叢集。 您必須開啟幾個前端節點 VM 上的端點。 在 Azure 傳統入口網站中執行下列動作。


1. 停止 VM。

2. 新增 TCP 連接埠 9090、9087、9091、9094 分別做為工作階段、代理人、 代理背景工作和資料服務

    ![設定端點][endpoint]

3. 啟動 VM。

SOA 用戶端應用程式不需要變更，除了將標頭名稱改變為 IaaS 叢集的完整名稱。

## 後續步驟

* 請參閱 [這些資源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 如需有關使用 HPC Pack 執行 Excel 工作負載。

* 請參閱 [Microsoft HPC Pack 中管理 SOA 服務](https://technet.microsoft.com/library/ff919412.aspx) 如需有關部署和管理 HPC pack SOA 服務。



[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png 
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png 
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png 
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png 
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png 
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png 
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png 
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png 
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png 
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png 
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png 
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png 
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png 

