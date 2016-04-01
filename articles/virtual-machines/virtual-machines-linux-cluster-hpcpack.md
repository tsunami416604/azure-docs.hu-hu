<properties
 pageTitle="HPC Pack 叢集中的 Linux 運算 VM | Microsoft Azure"
 description="了解如何撰寫指令碼，以在包含一個執行 Windows Server 的前端節點與多個 Linux 運算節點的 Azure 中部署 HPC Pack 叢集。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# 開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點

本文將說明如何使用 Azure PowerShell 指令碼在 Azure 中設定 Microsoft HPC Pack 叢集，其中包含執行 Windows Server 的前端節點和執行 Linux 散發的數個計算節點。 我們來說明了數種可將資料檔案移至 Linux 運算節點的方法。 您可以使用這個叢集在 Azure 中執行 Linux HPC 工作負載。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


在較高層級上，下圖顯示您將建立的 HPC Pack 叢集。

![具有 Linux 節點的 HPC 叢集][scenario]

## 部署具有 Linux 運算節點的 HPC Pack 叢集

您將使用 Microsoft HPC Pack IaaS 部署指令碼 (**New-hpciaascluster.ps1**) 若要在 Azure 基礎結構服務 (IaaS) 中的叢集部署作業自動化。 此 Azure PowerShell 指令碼使用 Azure Marketplace 中的 HPC Pack VM 映像進行快速部署，並提供一組完整的組態參數，讓部署變得簡單且有彈性。 指令碼部署的 Azure 虛擬網路、 儲存體帳戶、 雲端服務、 網域控制站、 不同的選用 SQL Server 資料庫伺服器、 叢集前端節點、 運算節點、 代理程式節點、 Azure PaaS （「 高載 」） 節點和 Linux 運算節點 (中引入的 Linux 支援 [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx))。

如需 HPC Pack 叢集部署選項的概觀，請參閱 [HPC Pack 2012 R2 和 HPC Pack 2012 開始使用指南](https://technet.microsoft.com/library/jj884144.aspx) 和 [選項來建立和管理高觸及運算 (HPC) 叢集在 Azure 使用 Microsoft HPC Pack](virtual-machines-hpcpack-cluster-options.md)。

### 必要條件

* **用戶端電腦** -您將需要有 Windows 用戶端電腦，才能執行叢集部署指令碼。

* **Azure PowerShell** - [安裝和設定 Azure PowerShell](../powershell-install-configure.md) (0.8.10 或更新版本) 用戶端電腦上。

* **HPC Pack IaaS 部署指令碼** -下載並解壓縮最新版的指令碼從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)。 執行 `New-HPCIaaSCluster.ps1 –Version` 即可檢查指令碼的版本。 這篇文章根據 4.4.0 版或更新版本的指令碼。

* **Azure 訂用帳戶** -您可以使用 Azure 全域或 Azure China 服務中的訂閱。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* **核心配額** -您可能需要增加核心配額，特別是如果您選擇部署多核心 VM 大小的數個叢集節點。 對於本文中的範例，您需要至少 12 個可用核心。 若要增加配額， [開啟線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。

### 建立組態檔

HPC Pack IaaS 部署指令碼會使用 XML 組態檔做為輸入，可描述 HPC 叢集的基礎結構。 若要部署由一個前端節點和 2 個 Linux 運算節點所組成的小型叢集，請將環境的值取代為下列範例組態檔。 如需組態檔的詳細資訊，請參閱指令碼資料夾中的 Manual.rtf 檔案和 [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

以下是組態檔中元素的簡短描述。

* **IaaSClusterConfig** -組態檔的根項目。

* **訂閱** -用來部署 HPC Pack 叢集的 Azure 訂用帳戶。 使用以下命令來確定已在您的用戶端電腦中設定唯一的 Azure 訂用帳戶名稱。 在此範例中，我們會使用 Azure 訂用帳戶的 “Subscription-1”。

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]或者，若要指定您想要使用的訂閱使用訂用帳戶 ID。 請參閱指令碼資料夾中的 Manual.rtf 檔案。

* **StorageAccount** -的 HPC Pack 叢集的所有持續性資料會儲存至指定的儲存體帳戶 (在本例中為 allvhdsje)。 如果儲存體帳戶不存在，指令碼會在指定的區域中建立 **位置**。

* **位置** -Azure 區域，您將部署 HPC Pack 叢集 （在本例中為日本東部）。

* **VNet** -設定虛擬網路和子網路建立 HPC 叢集的位置。 您可以在執行此指令碼前自行建立虛擬網路和子網路，或指令碼會建立位址空間為 192.168.0.0/20 的虛擬網路與位址空間為 192.168.0.0/23 的子網路。 在此範例中，指令碼會建立虛擬網路 centos7rdmavnetje 和子網路 CentOS7RDMACluster。

* **網域** -HPC Pack 叢集的 Active Directory 網域設定。 指令碼建立的所有 Windows VM 都會加入網域。 指令碼目前支援三個網域選項：ExistingDC、NewDC 和 HeadNodeAsDC。 此範例會將前端節點設定為網域控制站，其完整網域名稱為 hpc.local。

* **資料庫** -資料庫 HPC Pack 叢集的設定。 指令碼目前支援三個資料庫選項：ExistingDB、NewRemoteDB 和 LocalDB。 此範例會在前端節點上建立本機資料庫。

* **前端節點** -HPC Pack 前端節點的設定。 此範例會在雲端服務 centos7rdma-je 中建立名為 CentOS7RDMA-HN 的大小 A7 前端節點。 為了支援從遠端 (非網域聯結) 的用戶端電腦提交 HPC 作業，此指令碼會啟用 HPC 作業排程器 REST API 和 HPC Web 入口網站。

* **LinuxComputeNodes** -設定 HPC Pack linux 運算節點。 此範例會在雲端服務 centos7rdma-je 中建立兩個大小 A7 CentOS 7 Linux 計算節點 (CentOS7RDMA-LN1 和 CentOS7RDMA-LN2)。

### Linux 運算節點的其他考量

* HPC Pack 目前針對運算節點支援下列 Linux 散發套件：Ubuntu Server 14.10、CentOS 6.6、CentOS 7.0 和 SUSE Linux Enterprise Server 12。

* 本文中的範例使用 Azure Marketplace 中可用的特定 CentOS 版本來建立叢集。 如果您想要使用其他可用的映像，使用 **get azurevmimage** Azure PowerShell cmdlet 來尋找您的需要。 例如，若要列出所有 CentOS 7.0 映像，請執行下列命令：

    ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    尋找您需要的並取代 **ImageName** 組態檔中的值。

* 針對大小 A8 和 A9 VM 支援 RDMA 連線能力的 Linux 映像。 如果您指定已安裝並啟用 Linux RDMA 驅動程式的映像，HPC Pack IaaS 部署指令碼將會部署這些驅動程式。 例如，在 Marketplace 中為目前的 SUSE Linux Enterprise Server 12 (針對高效能計算最佳化) 映像指定映像名稱 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708`。

* 若要在從支援的映像建立的 Linux VM 上啟用 Linux RDMA 以執行 MPI 工作，請在叢集部署之後，根據您的應用程式需求在 Linux 節點上安裝並設定特定 MPI 程式庫。 如需範例，請參閱 [叢集在 Azure 中的使用 Microsoft HPC Pack Linux RDMA 上執行的 OpenFOAM](virtual-machines-linux-cluster-hpcpack-openfoam.md)。

* 請確定您部署一個服務內的所有 Linux RDMA 節點，以便 RDMA 網路連線在節點之間運作。


## 執行 HPC Pack IaaS 部署指令碼

1. 在用戶端電腦上以系統管理員身分開啟 PowerShell 主控台。

2. 將目錄變更為指令碼資料夾 (在本範例中為 E:\IaaSClusterScript)。

    ```
    cd E:\IaaSClusterScript
    ```

3. 執行下列命令來部署 HPC Pack 叢集。 這個範例假設組態檔位於 E:\HPCDemoConfig.xml。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    指令碼自動產生的記錄檔 **-記錄檔** 未指定參數。 記錄並不會即時寫入，但會在驗證和部署結束時收集，所以如果 PowerShell 處理程序在指令碼正在執行時停止，則會遺失某些記錄。

    a. 因為 **AdminPassword** 中未指定上述命令中，系統會提示使用者輸入的密碼 *MyAdminName*。

    b. 指令碼會接著開始驗證組態檔。 根據網路連線而定，這需要數十秒以至幾分鐘的時間。

    ![驗證][validate]

    c. 通過驗證之後，指令碼會列出將要針對 HPC 叢集建立的資源。 輸入 *Y* 以繼續。

    ![資源][resources]

    d. 此指令碼會開始部署 HPC Pack 叢集並完成設定，而不需要進一步的手動步驟。 這可能需要數分鐘的時間。

    ![部署][deploy]

4. 在設定順利完成之後，將遠端桌面連至前端節點並開啟 HPC 叢集管理員，以檢查 HPC Pack 叢集的狀態。 您可以使用您處理 Windows 運算節點的相同方式，管理和監視 Linux 運算節點。 例如，您會看到節點管理中所列的 Linux 節點。

    ![節點管理][management]

    您也會看到熱圖檢視中的 Linux 節點。

    ![熱圖][heatmap]

## 如何在具有 Linux 節點的叢集中移動資料

您有數個選擇可在 Linux 節點與叢集的 Windows 前端節點間移動資料。 以下是三種常見的方法。

* **Azure 檔案** -公開檔案共用，以在 Azure 儲存體中儲存資料檔案。 Windows 節點和 Linux 節點均可同時裝載 Azure 檔案共用做為磁碟機或資料夾，即使它們部署在不同的虛擬網路中。

* **前端節點 SMB 共用** -在 Linux 節點上裝載前端節點的共用的資料夾。

* **前端節點 NFS 伺服器**  -為混合式的 Windows 和 Linux 環境提供檔案共用解決方案。

### Azure 檔案儲存體

 [Azure 檔案](https://azure.microsoft.com/services/storage/files/) 服務會公開使用標準 SMB 2.1 通訊協定的檔案共用。 Azure VM 和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案儲存體 API，存取共用中的檔案資料。 如需詳細資訊，請參閱 [如何搭配 PowerShell 與.NET 使用 Azure 檔案儲存體](../storage/storage-dotnet-how-to-use-files.md)。

若要建立 Azure 檔案共用，請參閱中的詳細的步驟 [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)。 若要設定保留與連線，請參閱 [Persisting 連接 Microsoft Azure 檔案](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)。

在此範例中，我們會在儲存體帳戶 allvhdsje 上建立名為 rdma 的 Azure 檔案共用。 若要在前端節點上裝載共用，請開啟 [命令] 視窗並輸入下列命令：

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

在此範例中，allvhdsje 是儲存體帳戶名稱、storageaccountkey 是儲存體帳戶金鑰，以及 rdma 是 Azure 檔案共用名稱。 Azure 檔案共用將會裝載到前端節點的 Z: 上。

若要在 Linux 節點上裝載 Azure 檔案共用，請執行 **clusrun** 前端節點上的命令。 **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** 是實用的 HPC Pack 工具，可執行多個節點上的系統管理工作。 (請參閱 [適用於 Linux 節點的 CLusrun](#CLusrun-for-Linux-nodes) 在這篇文章。)

開啟 Windows PowerShell 視窗並輸入下列命令。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /rdma 的資料夾。 第二個命令會將 Azure 檔案共用 allvhdsjw.file.core.windows.net/rdma 裝載至 /rdma 資料夾，其 dir 和檔案模式位元設為 777。 在第二個命令中，allvhdsje 是儲存體帳戶名稱，而 storageaccountkey 是儲存體帳戶金鑰。

>[AZURE.NOTE]「 \ 」 「 第二個命令中的符號是 powershell 的逸出符號。 「 \'，"表示"，"（逗號） 是命令的一部分。

### 前端節點共用

或者，在 Linux 節點上裝載前端節點的共用資料夾。 這是共用檔案的最簡單方式，但前端節點和所有 Linux 節點都必須部署在相同的虛擬網路中。 步驟如下：

1. 在前端節點上建立資料夾，並將它分享給具有讀取/寫入權限的每個人。 為 \\CentOS7RDMA-HN\OpenFOAM，比方說，在前端節點上分享 D:\OpenFOAM。 此處的 CentOS7RDMA-HN 是前端節點的主機名稱。

    ![檔案共用權限][fileshareperms]

    ![檔案共用][filesharing]

2. 開啟 Windows PowerShell 視窗並執行下列命令來裝載共用資料夾。

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /openfoam 的資料夾。 第二個命令會將共用資料夾 //CentOS7RDMA-HN/OpenFOAM 裝載至此資料夾，其 dir 和檔案模式位元設為 777。 命令中的使用者名稱和密碼應該是前端節點上叢集使用者的使用者名稱和密碼。

>[AZURE.NOTE]「 \ 」 「 第二個命令中的符號是 powershell 的逸出符號。 「 \'，"表示"，"（逗號） 是命令的一部分。


### NFS 伺服器

NFS 服務可讓使用者使用 SMB 通訊協定在執行 Windows Server 2012 作業系統的電腦間共用和移轉檔案，以及使用 NFS 通訊協定在 Linux 架構電腦間共用和移轉檔案。 NFS 伺服器和其他所有節點都必須部署在相同的虛擬網路中。 這可提供更好的 Linux 節點相容性 (相較於 SMB 共用)；例如，支援檔案連結。

1. 若要安裝並設定 NFS 伺服器，請依照下列中的步驟 [伺服器的網路檔案系統的第一個共用端對端](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)。

    例如，使用下列屬性建立名為 nfs 的 NFS 共用。

    ![NFS 授權][nfsauth]

    ![NFS 共用權限][nfsshare]

    ![NFS NTFS 權限][nfsperm]

    ![NFS 管理屬性][nfsmanage]

2. 開啟 Windows PowerShell 視窗並執行下列命令來裝載 NFS 共用。

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  第一個命令會在 LinuxNodes 群組中的所有節點上建立名為 /nfsshared 的資料夾。 第二個命令會將 NFS 共用 CentOS7RDMA-HN:/nfs 裝載到此資料夾。 此處的 CentOS7RDMA-HN:/nfs 是 NFS 共用的遠端路徑。

## 如何提交工作
有數種方式可以將工作提交到 HPC Pack 叢集：

* HPC 叢集管理員或 HPC 工作管理員 GUI

* HPC Web 入口網站

* REST API

透過 HPC Pack GUI 工具和 Web 入口網站將工作提交至 Azure 中的叢集，與提交至 Windows 運算節點相同。 請參閱 [HPC Pack 工作管理員](https://technet.microsoft.com/library/ff919691.aspx) 和 [如何提交工作，並且在內部用戶端](virtual-machines-hpcpack-cluster-submit-jobs.md)。

若要透過 REST API 提交工作，請參閱 [建立和使用 Microsoft HPC Pack 中的 REST API 提交工作](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)。 也請參閱中的 Python 範例 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756) 從 Linux 用戶端提交工作。

## 適用於 Linux 節點的 Clusrun

HPC Pack **clusrun** 工具可用來透過命令提示字元或 HPC 叢集管理員中的 Linux 節點上執行命令。 以下有一些基本範例。

* 顯示叢集中所有節點的目前使用者名稱。

    ```
    > clusrun whoami
    ```

* 安裝 **gdb** 偵錯工具與 **yum** 所有節點在 linuxnodes 群組，並接著在 10 分鐘後重新啟動節點。

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* 建立可在叢集中的每個 Linux 節點上一秒顯示數字 1 到 10 的殼層指令碼、加以執行，並立即顯示節點的輸出。

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] 您可能需要使用中的某些逸出字元 **clusrun** 命令。 如此範例所示，在命令視窗中使用 ^ 來逸出 ">" 符號。

## 後續步驟

* 嘗試擴大叢集中的節點數量，或嘗試在叢集上執行 Linux 工作負載。 如需範例，請參閱 [執行 NAMD 使用 Microsoft HPC Pack linux 運算節點，在 Azure 中的](virtual-machines-linux-cluster-hpcpack-namd.md)。

* 嘗試的叢集大小 [A8 或 A9](virtual-machines-a8-a9-a10-a11-specs.md) 運算節點以執行 MPI 工作負載。 如需範例，請參閱 [叢集在 Azure 中的使用 Microsoft HPC Pack Linux RDMA 上執行的 OpenFOAM](virtual-machines-linux-cluster-hpcpack-openfoam.md)。

* 請嘗試 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) 搭配 Azure 資源管理員來加速部署 HPC Pack Linux 較大數目的運算節點。

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png


