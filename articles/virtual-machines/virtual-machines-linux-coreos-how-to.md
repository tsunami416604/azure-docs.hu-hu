<properties
    pageTitle="如何使用 CoreOS |Microsoft Azure"
    description="說明 CoreOS、如何在 Azure 上於傳統部署模型中建立 CoreOS 虛擬機器叢集在和其基本使用方法。"
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="10/21/2015"
    ms.author="rasquill"/>

# 如何在 Azure 上使用 CoreOS

本主題說明 [CoreOS]，並示範如何在 Azure 上建立三部 CoreOS 虛擬機器的叢集，作為了解此作業系統的快速入門。 它會使用 CoreOS 部署的 [Azure 上的 CoreOS]，從範例非常基本的項目 [Tim Park 的 CoreOS 教學課程，] 和 [Patrick Chanezon 的 CoreOS 教學課程] 示範了解 CoreOS 部署的基本結構和順利執行三部虛擬機器之叢集的絕對最低需求。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員部署模型](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/)。


## CoreOS、叢集和 Linux 容器

CoreOS 是精簡版的 Linux 旨在支援快速建立可能的大型 Vm 叢集，會使用 Linux 容器作為唯一封裝機制，包括 [Docker] 容器。 CoreOS 將協助支援：

+ 非常高層級的自動化
+ 更簡單、一致的應用程式部署
+ 應用程式層級和系統層級的延展性

概括而言，支援這目標的 CoreOS 功能包括：

1. 單一封裝系統：CoreOS 只會執行可在 Linux 容器中執行，以取得部署速度、一致性和方便性的 Linux 容器映像
2. 系統會自動執行作業系統更新，因此作業系統會以單一實體的方式進行更新，並可輕易地回復成已知狀態
3. 內建 [etcd](https://github.com/coreos/etcd) 和 [fleet](https://github.com/coreos/fleet) 動態 VM 和叢集通訊與管理精靈 (服務)

這是 CoreOS 及其功能的非常一般說明。 如需更完整 CoreOS 的詳細資訊，請參閱 [CoreOS 概觀]。

## 安全性考量
目前來說，CoreOS 假設可以 SSH 進入叢集的使用者便會有管理叢集的權限。 這樣的結果是，在沒有修改的情況下，CoreOS 叢集在測試和開發環境中表現十分出色，但您應在任何實際執行環境中套用另外的安全措施。

## 如何在 Azure 上使用 CoreOS

本節說明如何使用三部 CoreOS 虛擬機器才能使用 [Azure 命令列介面 (Azure CLI)] 建立的 Azure 雲端服務。 基本步驟如下：

1. 建立 SSH 憑證和金鑰，來保護與 CoreOS 虛擬機器的通訊安全
2. 取得叢集的 etcd 識別碼以便互相通訊
3. 建立 [YAML] 格式的 cloud-config 檔案
4. 使用 Azure CLI 建立新的 Azure 雲端服務和三部 CoreOS VM
5. 在 Azure VM 中測試您的 CoreOS 叢集
6. 在 localhost 中測試您的 CoreOS 叢集

### 建立通訊的公開和私密金鑰

使用中的指示 [如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-use-ssh-key.md) 建立 SSH 的公開和私密金鑰的金鑰。 (您可在下面的指示中找到基本步驟。)您打算使用這些金鑰來與叢集中的 VM 連線，以驗證他們是否可正常運作並可互相通訊。

> [AZURE.NOTE] 本主題假設您沒有這些金鑰，並要求您建立 `myPrivateKey.pem` 和 `myCert.pem` 檔案。 如果已將公開和私密金鑰組儲存至 `~/.ssh/id_rsa`，只要鍵入 `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` 就能取得需要上傳至 Azure 的 .pem 檔案。

1. 在工作目錄中，鍵入 `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` 以建立私密金鑰和與其相關聯的 X.509 憑證。

2. 若要判斷私密金鑰的擁有者是否可讀取或寫入檔案，請鍵入 `chmod 600 myPrivateKey.key`。

您的工作目錄中現在應同時包含 `myPrivateKey.key` 和 `myCert.pem` 檔案。


### 取得叢集的 etcd 識別碼

CoreOS 的 `etcd` 精靈需要探索識別碼，以自動查詢叢集中的所有節點。 若要擷取您的探索識別碼並將它儲存到 `etcdid` 檔案，請鍵入

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### 建立 cloud-config 檔案

在相同的工作目錄中，使用您喜愛的文字編輯器建立包含下列文字的檔案，並將儲存為 `cloud-config.yaml`。 (您可以將它儲存為任何檔案名稱，但是當您在下一個步驟中建立 Vm，您必須參考此檔案的名稱，在您 **-自訂資料** 選項 **azure vm 建立** 命令。)

> [AZURE.NOTE] 請記得輸入 `cat etcdid` 擷取 etcd 探索識別碼，從 `etcdid` 上面建立的檔案，並將 `<token>` 下列 `cloud-config.yaml` 檔案產生的數字，從您 `etcdid` 檔案。 如果您在最後無法驗證叢集，這有可能是您遺漏了其中一個步驟！

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

(如需更完整 cloud-config 檔案的詳細資訊，請參閱 [使用 Cloud-config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) CoreOS 文件中。)

### 使用 Azure CLI 建立新的 CoreOS VM
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. 安裝 [Azure 命令列介面 (Azure CLI)]，如果您沒有已完成，並登入使用的是工作或學校識別碼，或下載.publishsettings 檔案，並匯入您的帳戶的。
2. 尋找您的 CoreOS 映像。 鍵入 `azure vm image list | grep CoreOS` 可隨時找到可用的映像，應會顯示類似下面的結果清單：

    資料: 2b171e93f07c4903bcad35bda10acf22__CoreOS-穩定-522.6.0 公用 Linux

3. 輸入建立基本叢集的雲端服務
`azure service create <cloud-service-name>` 其中 <*雲端服務名稱*> 是您的 CoreOS 雲端服務的名稱。 這個範例會使用名稱 **`coreos-cluster`**; 您必須重複使用您選擇建立您的 CoreOS VM 雲端服務內的執行個體的名稱。

    附註: 如果您發現您的工作到目前為止在 [預覽入口網站](https://portal.azure.com), ，您會看到您的雲端服務名稱在資源群組和網域，如下圖所示:

    ![] [CloudServiceInNewPortal]

4. 連接到您的雲端服務，並建立新的 CoreOS VM 內使用 **azure vm 建立** 命令。 您會傳遞 X.509 憑證中的位置 **--ssh-cert** 選項。 輸入下列命令，建立您的第一個 VM 映像來取代記住 **coreos 叢集** 您所建立的雲端服務名稱:

    ```
建立 azure vm-自訂資料 = cloud-config.yaml-ssh = 22--ssh-cert=./myCert.pem-否-ssh-密碼---名稱 = 節點 1--連接 = coreos 叢集-位置 ="West US"522.6.0-穩定 2b171e93f07c4903bcad35bda10acf22__CoreOS-核心
```

5. Create the second node by repeating the command in step 4, replacing the **--vm-name** value with **node-2** and the **--ssh** port value with 2022.

6. Create the third node by repeating the command in step 4, replacing the **--vm-name** value with **node-3** and the **--ssh** port value with 3022.

You can see from the shot below how the CoreOS cluster appears in the portal.

![][EmptyCoreOSCluster]

### Test your CoreOS cluster from an Azure VM

To test your cluster, make sure you are in your working directory and then connect to **node-1** using **ssh**, passing the private key by typing:

    ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Once connected, type `sudo fleetctl list-machines` to see whether the cluster has already identified all VMs in the cluster. You should receive a response similar to the following:


    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -


### Test your CoreOS cluster from localhost

Finally, let's test your CoreOS cluster from your local Linux client. You might be able to install **fleetctl** by using **npm**, or you might want to install **fleet** and build **fleetctl** yourself on your local client. **fleet** requires **golang**, so you may need to install that first by typing:

`sudo apt-get install golang`

Then clone the **fleet** repository from github by typing:

`git clone https://github.com/coreos/fleet.git`

Build **fleet** by changing to the `fleet` directory and type

`./build`

And finally place **fleet** for easy use (depending upon your configuration you may or may not need to **sudo**):

`cp bin/fleetctl /usr/local/bin`

Make sure **fleet** has access to your `myPrivateKey.key` in the working directory by typing:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] If you are already using the `~/.ssh/id_rsa` key, then add that with `ssh-add ~/.ssh/id_rsa`.

Now you are ready to test remotely using the same **fleetctl** command you used from **node-1**, but passing some remote arguments:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

The results should be exactly the same:


    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## Next steps

You should now have a running three-node CoreOS cluster on Azure. From here, you can explore how to create more complex clusters and use Docker and create more interesting applications. To try a couple of quick examples, see [Get Started with Fleet on CoreOS on Azure].

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Azure Command-line Interface (Azure CLI)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[CoreOS Overview]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park's CoreOS Tutorial]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon's CoreOS Tutorial]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Get Started with Fleet on CoreOS on Azure]: virtual-machines-linux-coreos-fleet-get-started.md

