<properties
   pageTitle="用來部署 HPC Pack 叢集的 PowerShell 指令碼 |Microsoft Azure"
   description="執行 Windows PowerShell 指令碼，以在 Azure 基礎結構服務中部署完整的 HPC Pack 叢集。"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# 使用 HPC Pack IaaS 部署指令碼在 Azure VM 中建立高效能運算 (HPC) 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。



用戶端上執行 HPC Pack IaaS 部署 PowerShell 指令碼
若要完成 HPC Pack 叢集部署在 Azure 基礎結構的電腦
服務 (IaaS)。 此指令碼提供數個部署選項，並可以加入叢集計算節點執行支援的 Linux
散發套件或 Windows Server 作業系統。

根據您的環境和選擇，指令碼可建立所有的叢集基礎結構，包括 Azure 虛擬網路、儲存體帳戶、雲端服務、網域控制站、遠端或本機 SQL Database、前端節點、訊息代理程式節點、運算節點與 Azure 雲端服務 (「高載」或 PaaS) 節點。 或者，指令碼可使用既有的 Azure 基礎結構，然後建立 HPC 叢集前端節點、訊息代理程式節點、運算節點和 Azure 高載節點。


如需規劃 HPC Pack 叢集的背景資訊，請參閱 [產品評估與規劃](https://technet.microsoft.com/library/jj899596.aspx) 和 [開始](https://technet.microsoft.com/library/jj899590.aspx) HPC Pack TechNet 文件庫中的內容。

>[AZURE.NOTE]您也可以使用 Azure 資源管理員範本來部署 HPC Pack 叢集。 如需範例，請參閱 [建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), ，[使用自訂的運算節點映像建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/), ，或 [建立 HPC 叢集使用 Linux 運算節點](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)。

## 必要條件

* **Azure 訂用帳戶** -您可以使用 Azure 全域或 Azure China 服務中的訂閱。 您的訂用帳戶限制將會影響到您可以部署的叢集節點類型與數量。 如需資訊，請參閱 [Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md)。


* **Windows 用戶端電腦使用 Azure PowerShell 0.8.7 版或更新版本安裝並設定** -請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。 指令碼會在 Azure 服務管理中執行。


* **HPC Pack IaaS 部署指令碼** -下載並解壓縮最新版的指令碼從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)。 執行 `New-HPCIaaSCluster.ps1 –Version` 即可檢查指令碼的版本。 這篇文章是根據 4.4.0 版的指令碼撰寫的。

* **指令碼組態檔** -您需要建立指令碼來設定 HPC 叢集所使用的 XML 檔案。 如需相關資訊和範例，請參閱本文稍後的章節。


## 語法

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]您必須以系統管理員身分執行指令碼。

### 參數

* **ConfigFile** -指定描述 HPC 叢集的組態檔的檔案路徑。 如需詳細資訊，請參閱 [組態檔](#Configuration-file) 在本主題中或 Manual.rtf 檔案，該資料夾包含指令碼。

* **AdminUserName** -指定的使用者名稱。 如果網域樹系是由指令碼所建立，這將會成為所有 VM 的本機系統管理員使用者名稱和網域系統管理員名稱。 如果網域樹系已存在，則會將網域使用者指定為安裝 HPC Pack 的本機系統管理員使用者名稱。

* **AdminPassword** -指定系統管理員的密碼。 如果未在命令列中指定，指令碼會提示您輸入密碼。

* **HPCImageName** (選用)-指定 HPC Pack VM 映像名稱，用來部署 HPC 叢集。 它必須是 Microsoft 在 Azure Marketplace 中提供的 HPC Pack 映像。 如果未指定 (在大部分的情況下建議使用)，指令碼會選擇最新發佈的 HPC Pack 映像。

    >[AZURE.NOTE] 如果您未指定有效的 HPC Pack 映像，部署將會失敗。

* **記錄檔** (選用)-指定部署記錄檔路徑。 若未指定，指令碼會在執行指令碼之電腦的暫存目錄中建立記錄檔。

* **強制** (選用)-隱藏所有確認提示。

* **NoCleanOnFailure** (選用)-指定將不會移除未成功部署在 Azure Vm。 您必須先手動移除這些 VM 才能重新執行指令碼以繼續部署，否則部署可能會失敗。

* **PSSessionSkipCACheck** (選用)-使用此指令碼部署的每個雲端服務，自我簽署的憑證自動由 Azure 產生，並在雲端服務中的所有 Vm 會都使用此憑證做為預設的 Windows 遠端管理 (WinRM) 憑證。 若要部署 HPC 功能，這些 Azure Vm 中，預設的指令碼暫時安裝這些憑證以隱藏 「 不受信任的 CA 」 安全性錯誤指令碼執行期間，用戶端電腦的本機 Computer\\Trusted 根憑證授權單位存放區中指令碼完成時移除憑證。 如果指定此參數，則不會在用戶端電腦上安裝憑證，並且會抑制安全性警告。

    >[AZURE.IMPORTANT] 這個參數不建議用於實際部署。

### 範例

下列範例會建立新的 HPC Pack 叢集使用
組態檔 MyConfigFile.xml，並指定系統管理
安裝叢集的認證。

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### 其他考量

* 指令碼會使用 Azure Marketplace 中的 HPC Pack VM 映像來建立叢集前端節點。 目前的映像是基於已安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。

* 指令碼可選擇性地讓工作透過 HPC Pack Web 入口網站或 HPC Pack REST API 來提交。


* 如果您想要安裝其他軟體或進行其他設定，指令碼可以選擇性地在前端節點上執行自訂的前置和後置組態指令碼。


## 組態檔

部署指令碼的組態檔是 XML
檔案。 結構描述檔案 HPCIaaSClusterConfig.xsd 位於 HPC Pack IaaS
部署指令碼] 資料夾。 **IaaSClusterConfig** 是根項目
中所述的組態檔，其中包含子元素
在部署指令碼資料夾中的 Manual.rtf 檔案的詳細資料。 例如不同的情況下，檔案，請參閱
[範例組態檔](#Example-configuration-files) 在這篇文章。

## 範例組態檔

### 範例 1

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。 叢集中有 1 個具有本機資料庫的前端節點，和 12 個套用了 BGInfo VM 延伸模組的運算節點。
已停用中的所有 Vm 的自動安裝 Windows 更新
網域樹系中。 建立直接在所有雲端服務
「 東亞 」 位置。 3 個雲端服務中建立計算節點
和 3 個儲存體帳戶 (亦即，Myhpccnservice01-myhpccn-0001 至 Myhpccn-0005-0005 中
MyHPCCNService01 和 mycnstorage01;Myhpccnservice01-0006 至 MyHPCCN0010 中
MyHPCCNService02 和 mycnstorage02;以及 Myhpccnservice03-0011 為 Myhpccnservice01-0012 中
MyHPCCNService03 和 mycnstorage03)。 從建立計算節點
現有私人映像擷取自運算節點。 自動成長
壓縮和預設值啟用服務放大和縮小的間隔。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### 範例 2

下列組態檔會部署 HPC Pack 叢集
在現有的網域樹系。 叢集包含 1 個前端節點，1
資料庫伺服器與 500 GB 資料磁碟，而執行 Windows 的 2 個 broker 節點
Server 2012 R2 作業系統，以及執行 Windows 的 5 個運算節點
Server 2012 R2 作業系統。 雲端服務 MyHPCCNService 會
同質群組 myibaffinitygroup 中和其他雲端中建立
服務會建立在同質群組 myaffinitygroup。 HPC 工作
排程器 REST API 和 HPC web 入口網站會在前端節點上啟用。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### 範例 3

下列組態檔會建立新的網域樹系
與部署 HPC Pack 叢集有 1 個前端節點本機
資料庫和 20 Linux 運算節點。 建立所有雲端服務
直接在 「 東亞 」 位置。 建立 Linux 運算節點
在 4 個雲端服務和 4 個儲存體帳戶 (也就是 MyLnxCN-0001 到
在 MyLnxCNService01 和 mylnxstorage01，MyLnxCN-0006 至 Myhpccn-0005-0005
MyLnxCN-0010 MyLnxCNService02 和 mylnxstorage02，MyLnxCN-0011 至
MyLnxCN-0015 MyLnxCNService03 和 mylnxstorage03，和 MyLnxCN-0016 至
MyLnxCN-0020 MyLnxCNService04 和 mylnxstorage04 中)。 運算節點
OpenLogic CentOS 7.0 版的 Linux 映像建立。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### 範例 4

下列組態檔會部署 HPC Pack 叢集
其具有與本機資料庫的前端節點和 5 個運算節點執行
Windows Server 2008 R2 作業系統中。 所有雲端服務
直接在 「 東亞 」 位置中建立。 前端節點做為網域
控制站的網域樹系。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### 範例 5

下列組態檔會部署 HPC Pack 叢集
在現有的網域樹系。 叢集有 1 個前端節點本機
資料庫，就會建立兩個 Azure 節點範本，而且 3 個中型大小的 Azure
為 Azure 節點範本 AzureTemplate1 建立節點。 指令碼檔案
設定前端節點後，會執行在前端節點上。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## 已知問題


* **「 VNet 不存在 」 錯誤** -如果您執行的 HPC Pack IaaS 部署指令碼來部署多個
在 Azure 中下一個訂用帳戶一或多個並行的叢集
部署可能會失敗，發生錯誤 「 VNet *VNet\_Name* 不存在 」。
如果發生此錯誤，請對失敗的部署重新執行指令碼。

* **從 Azure 虛擬網路存取網際網路的問題** -如果您建立新的網域控制站的 HPC Pack 叢集使用
部署指令碼，或您手動將升級網域的 VM
控制站，您可能會遇到問題，在 Azure 中的 Vm 連線
虛擬網路到網際網路。 這可能會發生 DNS 轉寄站
伺服器會自動設定的網域控制站，而這在
轉寄站 DNS 伺服器未正確解析。

    若要解決此問題，請登入網域控制站，而且不是
    移除轉寄站組態設定或設定有效
    轉寄站 DNS 伺服器。 若要這樣做，請在 [伺服器管理員中，按一下 **工具** >
    **DNS** 來開啟 [DNS 管理員]，然後按兩下 [ **轉寄站**。

* **存取 RDMA 網路從大小為 A8 或 A9 Vm 問題** -如果您新增 Windows Server 的運算節點或代理程式節點 Vm 的大小為 A8 或
A9 使用部署指令碼，您可能會遇到的問題
這些 Vm 連線到 rdma 應用程式。 其中一個原因這
可能是如果 HpcVmDrivers 延伸模組未正確安裝
當大小 A8 或 A9 Vm 加入至叢集。 例如，
延伸模組可能會卡在安裝中的狀態。

    若要解決此問題，請先檢查中的延伸模組的狀態
    Vm。 如果未正確安裝延伸模組，請嘗試移除
    從 HPC 節點叢集，然後再次加入節點。 例如，
    您可以新增計算節點 Vm 的前端節點上執行 Add-hpciaasnode.ps1 指令碼。


## 後續步驟

* 嘗試在叢集上執行測試工作負載。 如需範例，請參閱 HPC Pack [入門指南](https://technet.microsoft.com/library/jj884144)。

* 使用指令碼來建立叢集，並執行 HPC 工作負載的教學課程，請參閱 [開始使用 Azure 來執行 Excel 和 SOA 工作負載中的 HPC Pack 叢集](virtual-machines-excel-cluster-hpcpac), ，[執行 NAMD 使用 Microsoft HPC Pack linux 運算節點，在 Azure 中的](virtual-machines-linux-cluster-hpcpack-namd.md), ，或 [執行 OpenFOAM 使用 Microsoft HPC Pack linux 運算節點，在 Azure 中的](virtual-machines-linux-cluster-hpcpack-openfoam.md)。

* 嘗試以 HPC Pack 的工具啟動、停止、新增和移除您所建立之叢集中的運算節點。 請參閱 [管理計算節點的 HPC Pack 叢集在 Azure 中](virtual-machines-hpcpack-cluster-node-manage.md)

