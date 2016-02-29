<properties
 pageTitle="Linux VM 上的 NAMD 與 Microsoft HPC Pack | Microsoft Azure"
 description="在 Azure 上部署 Microsoft HPC Pack 叢集並且執行在多個 Linux 運算節點上具有 charmrun 的 NAMD 模擬。"
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
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# 在 Azure 中的 Linux 運算節點以 Microsoft HPC Pack 執行 NAMD

本文將說明如何部署在 Azure 上的 Microsoft HPC Pack 叢集使用多個 Linux 運算節點和執行 [NAMD](http://www.ks.uiuc.edu/Research/namd/) 工作 **charmrun** 計算和視覺化大型 biomolecular 系統的結構。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。



NAMD (適用於奈米分子動力程式) 是專為高效能模擬大型生物分子系統而設計的平行分子動力套件，包含多達數百萬個原子，例如病毒、細胞結構和大蛋白。 NAMD 會針對典型模擬縮放到數百個核心，以及針對最大型的模擬縮放至超過 500,000 個核心。

Microsoft HPC Pack 提供功能來執行各種大規模 HPC 和平行應用程式，包括 Microsoft Azure 虛擬機器的叢集上的 MPI 應用程式。 從 Microsoft HPC Pack 2012 R2 Update 2 開始，HPC Pack 也支援在部署於 HPC Pack 叢集中的 Linux 計算節點 VM 上執行 Linux HPC 應用程式。 請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster-hpcpack.md) 的簡介。


## 先決條件

* **HPC Pack 叢集使用 Linux 運算節點** -請參閱 [開始使用 Linux 運算節點，在 Azure 中的 HPC Pack 叢集](virtual-machines-linux-cluster-hpcpack.md) 如的先決條件及步驟來部署 linux HPC Pack 叢集計算節點，在 Azure 上的使用 Azure Marketplace 中的 PowerShell 指令碼和 HPC Pack 的映像。

    以下是範例 XML 組態檔，您可以與指令碼搭配使用，以部署 Azure 架構 HPC Pack 叢集，其中包含 Windows Server 2012 R2 前端節點和 4 個大型 (A3) CentOS 6.6 運算節點。 請將您的訂用帳戶和服務名稱取代為適當的值。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **NAMD software and tutorial files** - Download NAMD software for Linux from the [NAMD](http://www.ks.uiuc.edu/Research/namd/) site. This article is based on NAMD version 2.10, and uses the [Linux-x86_64 (64-bit Intel/AMD with Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) archive, which you'll use to run NAMD on multiple Linux compute nodes in a cluster network. Also download the [NAMD tutorial files](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Follow the instructions later in this article to extract the archive and the tutorial samples on the cluster head node.

* **VMD** (optional) - To see the results of your NAMD job, download and install the molecular visualization program [VMD](http://www.ks.uiuc.edu/Research/vmd/) on a computer of your choice. The current version is 1.9.2. See the VMD download site to get started.  


## Set up mutual trust between compute nodes
Running a cross-node job on multiple Linux nodes requires the nodes to trust each other (by **rsh** or **ssh**). When you create the HPC Pack cluster with the Microsoft HPC Pack IaaS deployment script, the script automatically sets up permanent mutual trust for the administrator account you specify. For non-administrator users you create in the cluster's domain, you have to set up temporary mutual trust among the nodes when a job is allocated to them, and destroy the relationship after the job is complete. To do this for each user, provide an RSA key pair to the cluster which HPC Pack uses to establish the trust relationship.

### Generate an RSA key pair
It's easy to generate an RSA key pair, which contains a public key and a private key, by running the Linux **ssh-keygen** command.

1.  Log on to a Linux computer.

2.  Run the following command.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Press **Enter** to use the default settings until the command is completed. Do not enter a passphrase here; when prompted for a password, just press **Enter**.

    ![Generate an RSA key pair][keygen]

3.  Change directory to the ~/.ssh directory. The private key is stored in id_rsa and the public key in id_rsa.pub.

    ![Private and public keys][keys]

### Add the key pair to the HPC Pack cluster
1.  Make a Remote Desktop connnection to your head node with your HPC Pack administrator account (the administrator account you set up when you ran the deployment script).

2. Use standard Windows Server procedures to create a domain user account in the cluster's Active Directory domain. For example, use the Active Directory User and Computers tool on the head node. The examples in this article assume you create a domain user named hpclab\hpcuser.

2.  Create a file named C:\cred.xml and copy the RSA key data into it. You can find an example in the sample files at the end of this article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Open a Command Prompt and enter the following command to set the credentials data for the hpclab\hpcuser account. You use the **extendeddata** parameter to pass the name of C:\cred.xml file you created for the key data.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    This command completes successfully without output. After setting the credentials for the user accounts you need to run jobs, store the cred.xml file in a secure location, or delete it.

5.  If you generated the RSA key pair on one of your Linux nodes, remember to delete the keys after you finish using them. HPC Pack does not set up mutual trust if it finds an existing id_rsa file or id_rsa.pub file.

>[AZURE.IMPORTANT] We don’t recommend running a Linux job as a cluster administrator on a shared cluster, because a job submitted by an administrator runs under the root account on the Linux nodes. A job submitted by a non-administrator user runs under a local Linux user account with the same name as the job user, and HPC Pack sets up mutual trust for this Linux user across all the nodes allocated to the job. You can set up the Linux user manually on the Linux nodes before running the job, or HPC Pack creates the user automatically when the job is submitted. If HPC Pack creates the user, HPC Pack deletes it after the job completes. The keys are removed after job completion on the nodes to reduce security threats.

## Set up a file share for Linux nodes

Now set up a standard SMB share on a folder on the head node, and mount the shared folder on all Linux nodes to allow the Linux nodes to access NAMD files with a common path. See the file sharing options and steps in [Get started with Linux compute nodes in an HPC Pack Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md). (We recommend mounting a shared folder on the head node in this article because CentOS 6.6 Linux nodes don’t currently support the Azure File service, which provides similar features. For more about mounting an Azure File share, see [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).)

1.  Create a folder on the head node, and share it to everyone by setting Read/Write privileges. In this example, \\\\CentOS66HN\Namd is the name of the folder, where CentOS66HN is the host name of the head node.

2. Extract the NAMD files in the folder by using a Windows version of **tar** or another Windows utility that operates on .tar archives. Extract the NAMD tar archive to \\\\CentOS66HN\Namd\namd2, and extract the tutorial files under \\\\CentOS66HN\Namd\namd2\namdsample.

2.  Open a Windows PowerShell window and run the following commands to mount the shared folder.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

The first command creates a folder named /namd2 on all nodes in the LinuxNodes group. The second command mounts the shared folder //CentOS66HN/Namd/namd2 onto the folder with dir_mode and file_mode bits set to 777. The *username* and *password* in the command should be the credentials of a user on the head node.

>[AZURE.NOTE]The “\`” symbol in the second command is an escape symbol for PowerShell. “\`,” means the “,” (comma character) is a part of the command.


## Prepare to run a NAMD job

 Your  NAMD job needs a *nodelist* file for **charmrun** to know the number of nodes to use when starting NAMD processes. You'll write a Bash script that generates the nodelist file and runs **charmrun** with this nodelist file. You can then submit a NAMD job in HPC Cluster Manager that calls this script.

### Environment variables and nodelist file
Information about nodes and cores is in the $CCP_NODES_CORES environment variable, which is automatically set by the HPC Pack head node when the job is activated. The format for the $CCP_NODES_CORES variable is as follows:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

This lists the total number of nodes, node names, and number of cores on each node that are allocated to the job. For example, if the job needs 10 cores to run, the value of $CCP_NODES_CORES will be similar to:

```
3 CENTOS66LN 00 4 CENTOS66LN 01 4 CENTOS66LN 03 2
```

Following is the information in the nodelist file, which the script will generate:

```
主要群組
主機 <Name of node1> + + 中的 cpu <Cores of node1>
主機 <Name of node2> + + 中的 cpu <Cores of node2>
…
```

For example:

```
主要群組
裝載 CENTOS66LN 00 + + 4 個 cpu
裝載 CENTOS66LN 01 + + 4 個 cpu
裝載 CENTOS66LN 03 + + cpu 2
```
### Bash script to create a nodelist file

Using a text editor of your choice, create the following Bash script in the folder containing the NAMD program files and name it hpccharmrun.sh. A complete example is in the sample files at the end of this article. This bash script does the following things.

>[AZURE.TIP] Save your script as a text file with Linux line endings (LF only, not CR LF). This ensures that it runs properly on the Linux nodes.

1.  Define some variables.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Get node information from the environment variables. $NODESCORES stores a list of split words from $CCP_NODES_CORES. $COUNT is the size of $NODESCORES.

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.  If the $CCP_NODES_CORES variable is not set, just start **charmrun** directly. (This should only occur when you run this script directly on your Linux nodes.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Or create a nodelist file for **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  執行 **charmrun** nodelist 檔案時，取得其傳回的狀態，並移除節點清單檔案結尾。

    ${CCP_NUMCPUS} 是 HPC Pack 前端節點所設定的另一個環境變數。 它會儲存配置給這項工作的總核心數目。 我們可以用它來為 charmrun 指定處理序數目。

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  以結束 **charmrun** 傳回狀態。

    ```
    exit ${RTNSTS}
    ```

## 提交 NAMD 工作

現在您已準備就緒在 HPC 叢集管理員中提交 NAMD 工作。

1.  連接至您的叢集前端節點並且啟動 HPC 叢集管理員。

2.  在 **節點管理**, ，確保 Linux 運算節點處於 **線上** 狀態。 如果沒有，請選取它們，然後按一下 [ **上線**。

2.  在 **作業管理**, ，按一下 [ **新工作**。

3.  輸入工作的名稱，例如 *hpccharmrun*。

    ![新的 HPC 工作][] namd_job

4.  在 **工作詳細資料** ] 頁面的 [ **工作資源**, ，選取的資源類型 **節點** 並設定 **最小值** 為 3。 在此範例中，我們將在 3 個 Linux 節點上執行工作，每個節點有 4 個核心。

    ![工作資源][] job_resources

5.  在 **工作詳細資料與 I/O 重新導向** 頁面上，加入新的工作工作並設定下列值。

    * **命令列** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **工作目錄** -/namd2

    * **最小值** -3

    ![工作詳細資料][] task_details

    >[AZURE.NOTE] 您的工作目錄在這裡設定因為 **charmrun** 嘗試瀏覽至相同的工作目錄，每個節點上。 如果未設定工作目錄，HPC Pack 會在其中一個 Linux 節點上建立的隨機命名資料夾中啟動命令。 這將在其他節點上造成下列錯誤:
`/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` 若要避免這個問題，指定可存取的所有節點，和工作目錄的資料夾路徑。

5.  按一下 [ **提交** 即可執行此作業。

    根據預設，HPC Pack 會以您目前登入的使用者帳戶提交工作。 對話方塊可能會提示您輸入使用者名稱和密碼之後您按一下, **提交**。

    ![工作認證][認證]

    在某些情況下 HPC Pack 會記住您以前輸入的使用者資訊，所以不會顯示此對話方塊。 若要讓 HPC Pack 再次顯示該對話方塊，在命令視窗中輸入下列命令，然後提交工作。

    ```
    hpccred delcreds
    ```

6.  工作需要數分鐘的時間才能完成。

7.  在作業記錄檔中看到 \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log 和輸出檔案 \\<headnode>\Namd\namd2\namdsample\1-2-sphere\。

8.  選擇性啟動 VMD 以檢視您的工作結果。 用來視覺化 NAMD 輸出檔案 (在此案例中，水圈中的泛素蛋白質分子) 的步驟已超出本文的範圍。 請參閱 [NAMD 教學課程](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) 如需詳細資訊。

    ![工作結果][] vmd_view

## 範例檔案

### 範例 hpccharmrun.sh 指令碼

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
### 範例 cred.xml 檔案

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png

