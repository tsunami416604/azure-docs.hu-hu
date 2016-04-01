<properties
 pageTitle="要執行 MPI 應用程式的 Linux RDMA 叢集 | Microsoft Azure"
 description="建立大小為 A8 或 A9 VM 的 Linux 叢集以使用 RDMA 執行 MPI 應用程式。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2015"
 ms.author="danlep"/>

# 設定 Linux RDMA 叢集以執行 MPI 應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文將說明如何設定 Linux RDMA 叢集與 Azure 中 [大小 A8 和 A9 虛擬機器](virtual-machines-a8-a9-a10-a11-specs.md) 執行平行訊息傳遞介面 (MPI) 應用程式。 當您設定 A8 和 A9 大小的 Linux VM 以執行支援的 MPI 實作時，MPI 應用程式可透過低延遲、高輸送量網路，在運用遠端直接記憶體存取 (RDMA) 技術的 Azure 中進行有效率的通訊。

>[AZURE.NOTE] Azure Linux RDMA 目前支援的 Intel MPI Library 第 5 版 SUSE Linux Enterprise Server 12 (SLES 12) 上執行。 這篇文章根據 Intel MPI 5.0.3.048 版。
>
> Azure 也提供 A10 和 A11 大量運算執行個體，包含與 A8 和 A9 執行個體相同的處理能力，但不含 RDMA 後端網路的連接。 若要在 Azure 中執行 MPI 工作負載，使用 A8 和 A9 執行個體通常可獲得最佳效能。


## Linux 叢集部署選項

您可以使用下列方法來建立包含或不含工作排程器的 Linux RDMA 叢集。

* **HPC Pack** -在 Azure 中建立的 Microsoft HPC Pack 叢集，並加入計算節點執行 （啟動 HPC Pack 2012 R2 Update 2 中的 Linux 運算節點支援） 支援的 Linux 散發套件。 某些 Linux 節點可以設定為存取 RDMA 網路。 請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster.md)。

* **Azure CLI 指令碼** -本文使用的其餘部分中的步驟中所示 [Azure 命令列介面](../xplat-cli-install.md) (CLI) 用於 Mac、 Linux 和 Windows 編寫指令碼在部署的虛擬網路和其他必要元件來建立 Linux 叢集。 傳統 (服務管理) 部署模式中的 CLI 將循序建立叢集節點，因此如果您正在部署許多運算節點，則可能需要花費幾分鐘才能完成部署。

* **Azure 資源管理員範本** -藉由建立簡單的 Azure 資源管理員 JSON 範本檔案和執行 Azure CLI 命令，讓資源管理員或使用 Azure 入口網站，部署多個的 A8 和 A9 Linux Vm，以及定義虛擬網路、 靜態 IP 位址、 DNS 設定和其他資源，以建立計算叢集可利用 RDMA 網路，以執行 MPI 工作負載。 您可以 [建立您自己的範本](../resource-group-authoring-templates.md), ，或檢查 [Azure 快速入門範本頁面](https://azure.microsoft.com/documentation/templates/) 部署方案，您想要的 Microsoft 或社群所貢獻的範本。 資源管理員範本通常會提供最快速且最可靠的方式來部署 Linux 叢集。

## Azure 服務管理中使用 Azure CLI 指令碼的部署

下列步驟可協助您使用 Azure CLI 部署 SLES 12 VM、安裝 Intel MPI Library 和其他自訂項目、建立自訂的 VM 映像，然後再編寫 A8 或 A9 VM 叢集部署的指令碼。

### 必要條件

*   **用戶端電腦** -您將需要 Mac、 Linux 或 Windows 用戶端電腦與 Azure 進行通訊。 這些步驟假設您使用 Linux 用戶端。

*   **Azure 訂用帳戶** -如果您沒有帳戶，您可以建立免費試用帳戶只需要幾分鐘的時間。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

*   **核心配額** -您可能需要增加核心配額以部署 A8 或 A9 Vm 的叢集。 例如，如果您想要部署 8 個 A9 VM，將需要至少 128 個核心，如這篇文章中所示。 若要增加配額， [開啟線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。

*   **Azure CLI** - [安裝](../xplat-cli-install.md) Azure CLI 和 [設定 ](../xplat-cli-connect.md) 從用戶端電腦連線到您的 Azure 訂閱。

*   **Intel MPI** -自訂您的叢集的 Linux VM 映像的一部分 （請參閱本文稍後詳細資料），您需要下載並安裝 Intel MPI Library 5 執行階段從 [Intel.com 網站](https://software.intel.com/en-us/intel-mpi-library/) 佈建 Azure Linux VM 上。 若要為此做準備，註冊 Intel 之後，請遵循確認電子郵件中相關網頁的連結，並針對適當版本的 Intel MPI 複製 .tgz 檔案的下載連結。 這篇文章根據 Intel MPI 5.0.3.048 版。

### 佈建 SLES 12 VM

登入 Azure 和 Azure CLI 之後執行 `azure config list` 確認輸出顯示 **asm** 模式、 incldicating CLI 處於 Azure 服務管理模式。 如果不是，請執行此命令來設定模式：

```
azure config mode asm
```

輸入下列命令來列出已獲授權使用的所有訂用帳戶：

```
azure account list
```

目前的使用中訂用帳戶會透過將 `Current` 設為 `true` 來進行識別。 如果這不是您想要用來建立叢集的訂用帳戶，請將適當的訂用帳戶編號設定為使用中的訂用帳戶：

```
azure account set <subscription-number>
```

若要查看公開可用的 SLES 12 HPC 映像，在 Azure 中，執行命令類似於下列各項，假設您的 shell 環境支援 **grep**:

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]SLES 12 HPC 映像所需的 Linux RDMA 驅動程式與預先設定的 Azure。

現在，執行類似下列的命令，使用可用的 SLES 12 HPC 映像來佈建 A9 大小的 VM：

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

其中

* 大小 (在此範例中為 A9) 可以是 A8 或 A9

* 外部 SSH 連接埠號碼 (在此範例中，這是 SSH 的預設值為 22) 是任何有效的連接埠編號;內部的 SSH 連接埠號碼將會設定為 22

* 系統將在位置指定的 Azure 區域中建立新的雲端服務；指定一個位置例如「美國西部」，其中 A8 和 A9 執行個體可提供使用

* 目前的映像名稱可以是 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (免費) 或 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` 以取得 SUSE 優先支援 (會收取費用)

### 自訂 VM

VM 完成佈建之後，使用 VM 的外部 IP 位址 (或 DNS 名稱) 以及您設定和自訂的外部連接埠編號來設定 VM 的 SSH。 連接的詳細資訊，請參閱 [如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。 您應該以您在 VM 上設定的使用者身分來執行命令，除非需要根目錄存取才能完成步驟。

>[AZURE.IMPORTANT]Microsoft Azure Linux vm 沒有根目錄存取。 若要在以使用者身分連接至 VM 時取得系統管理存取權，請使用 `sudo` 執行命令。

*   **更新** -使用安裝更新 **zypper**。 您也可能會想要安裝 NFS 公用程式。  

    >[AZURE.IMPORTANT]目前我們建議您不要套用核心更新，而這可能導致問題 Linux rdma 驅動程式。

*   **Intel MPI** -下載及執行類似下列的命令，從 Intel.com 網站安裝 Intel MPI Library 5 執行階段。

    ```
    sudo wget <download link for your registration>

    sudo tar xvzf <tar-file>

    cd <mpi-directory>

    sudo ./install.sh

    ```

    接受預設設定以在 VM 上安裝 Intel MPI。

*   **鎖定記憶體** -MPI 程式碼的可用記憶體的 RDMA，針對您要新增或變更在 /etc/security/limits.conf 檔案中的下列設定。 (您需要編輯此檔案的根目錄存取權。)

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]基於測試目的，您也可以設定 memlock 設定為無限制。 例如：`<User or group name>    hard    memlock unlimited`。


*   **SSH 金鑰** -產生 SSH 金鑰來執行 MPI 工作時，叢集中建立您的使用者帳戶，在所有計算節點之間的信任。 執行以下命令建立 SSH 金鑰。 只要按下 Enter 鍵即可在預設位置產生金鑰，不需要設定複雜密碼。

    ```
    ssh-keygen
    ```

    將公開金鑰附加至已知公開金鑰的 authorized_keys 檔案。

    ```
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    ```

    在 ~/.ssh 目錄中編輯或建立 config 檔案。 提供您將在 Azure 中使用的私人網路 IP 位址範圍 (在本範例中為 10.32.0.0/16)：

    ```
    host 10.32.0.*
    StrictHostKeyChecking no
    ```

    或者，列出叢集中每個 VM 的私人網路 IP 位址，如下所示：

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]設定 `StrictHostKeyChecking no` 造成潛在的安全性風險，如果特定的 IP 位址或範圍未指定，在下列範例所示。

* **應用程式** -安裝任何應用程式，您需要在此 VM 上，或執行其他自訂項目，才能擷取映像。

### 擷取映像

若要擷取映像，請先在 Linux VM 中執行下列命令。 這樣會取消佈建 VM，但會保留您設定的使用者帳戶與 SSH 金鑰。

```
sudo waagent -deprovision
```

然後，從用戶端電腦，執行下列 Azure CLI 命令來擷取映像。 請參閱 [如何擷取 Linux 虛擬機器作為範本使用](virtual-machines-linux-capture-image.md) 如需詳細資訊。  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

執行這些命令後，系統會擷取 VM 映像供您使用，接著將會刪除 VM。 現在您已準備好使用自訂映像來部署叢集。

### 使用映像部署叢集

針對您的環境，使用適當的值修改下列 Bash 指令碼，並從用戶端電腦執行。 因為服務管理部署方法會循序部署 VM，系統會花費幾分鐘部署這段指令碼中建議的 8 個 A9 VM。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environnment to provision your cluster
```
## 設定和執行 Intel MPI

若要在 Azure Linux RDMA 上執行 MPI 應用程式，您需要設定 Intel MPI 專用的特定環境變數。 以下範例 Bash 指令碼，可供您設定變數並執行應用程式。

```
#!/bin/bash -x

source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLEAND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

主機檔案的格式如下所示。 針對叢集中的每個節點加入一行指令碼。 從之前定義的 VNet 指定私人 IP 位址，而非 DNS 名稱。 例如，對於 IP 位址為 10.32.0.1 和 10.32.0.2 的兩台主機，此檔案包含下列內容：

```
10.32.0.1:16
10.32.0.2:16
```

## Intel MPI 安裝之後驗證兩個基本的節點叢集

如果您尚未這樣做，請先設定 Intel MPI 的環境。

```
source /opt/intel/impi_latest/bin64/mpivars.sh
```

### 執行簡單的 MPI 命令

在其中一個運算節點執行簡單的 MPI 命令，以顯示 MPI 已正確安裝而且可以在至少兩個運算節點之間通訊。 下列 **mpirun** 命令執行 **hostname** 命令在兩個節點的節點上。

```
/opt/intel/impi_latest/bin64/mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
您的輸出應該會列出您傳遞做為 `-hosts` 之輸入的所有節點名稱。 例如， **mpirun** 具有兩個節點的命令會傳回輸出類似如下 ︰

```
cluster11
cluster12
```

### 執行 MPI 基準測試

下列的 Intel MPI 命令會使用 pingpong 基準測試驗證叢集組態和 RDMA 網路連線。

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

您應該會在包含兩個節點的工作中叢集上看到類似下列的輸出。 在 Azure RDMA 網路上，大小為 512 個位元組以下的訊息預期會出現 3 百萬分之一秒或以下的延遲。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```

## 網路拓撲考量

* 在 Linux VM 上，Eth1 會保留給 RDMA 網路流量。 請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。

* 在 Azure 中，不支援透過 Infiniband (IB) 的 IP。 僅支援透過 IB 的 RDMA。

* 在 Linux VM 上，Eth0 會保留給一般 Azure 網路流量。

## 後續步驟

* 嘗試在 Linux 叢集上部署並執行 Linux MPI 應用程式。

* 請參閱 [Intel MPI Library 文件](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) 如需 Intel MPI 的指引。


