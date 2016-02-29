<properties
    pageTitle="開始在 CoreOS 使用 Fleet | Microsoft Azure"
    description="提供在 CoreOS Linux VM (在 Azure 上以傳統部署模型建立的 VM) 上使用 Fleet 和 Docker 的基本範例。"
    services="virtual-machines"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="11/16/2015"
    ms.author="danlep"/>

# 開始在 Azure 上的 CoreOS VM 叢集使用 Fleet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員模型](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/)。


這篇文章可讓您快速使用兩個範例 [fleet](https://github.com/coreos/fleet) 和 [Docker](https://www.docker.com/) [CoreOS] 虛擬機器的叢集上執行應用程式。

若要使用這些範例，請先設定三節點 CoreOS 叢集 [如何 Azure 上使用 CoreOS] 中所述。 完成後，您將了解 CoreOS 部署的最基本項目，然後具備工作叢集和用戶端電腦。 我們將使用和這些範例中完全相同的叢集名稱。 此外，這些範例假設您正在使用本機 Linux 主機來執行您 **fleetctl** 命令。 請參閱 [使用用戶端](https://coreos.com/fleet/docs/latest/using-the-client.html) 如需詳細資訊 **fleetctl** 用戶端。


## <a id='simple'>範例 1: Docker 與 Hello World</a>

此為在單一 Docker 容器上執行的簡單 "Hello World" 應用程式。 這會使用 [busybox Docker 中樞映像]。

在 Linux 用戶端電腦上使用您偏好的文字編輯器來建立下列 **systemd** 單位檔案，並將它命名 `helloworld.service`。 (如需有關語法的詳細資訊，請參閱 [單位檔案])。

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

現在請連接 CoreOS 叢集然後執行下列命令啟動這個單位 **fleetctl** 命令。 此輸出顯示此單位已啟動，並顯示其位置。


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE] 若要執行遠端 **fleetctl** 命令，而不 **-通道** 參數，可選擇設定 FLEETCTL_TUNNEL 環境變數，來建立通道的要求。 例如：`export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`。


您可連接到容器以查看此服務的輸出：

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

若要清除，請停止並卸載此單位。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>範例 2: 高可用性 nginx 伺服器</a>

其中一個優點使用 CoreOS、 Docker 和 **fleet** 是很容易以高可用性的方式執行服務。 在此範例中，您將部署一個服務，此服務由執行 Nginx Web 伺服器的三個完全相同的容器所組成。 這些容器將在此叢集的三個 VM 中執行。 此範例類似於在 [以 fleet 啟動容器] 中，並使用 [nginx Docker 中樞映像]。

>[AZURE.IMPORTANT] 若要執行高可用性網頁伺服器，您將需要設定負載平衡的 HTTP 端點 (公用連接埠 80、 私用連接埠 80) 的虛擬機器上。 您可以在建立 CoreOS 叢集後使用 Azure 傳統入口網站或 **azure vm 端點** 命令。 如需詳細資訊，請參閱 [設定負載平衡集]。

用戶端電腦時，使用您偏好的文字編輯器來建立 **systemd** 範本單位檔案，名為 nginx@.service。 您將使用這個簡單的範本來啟動三個不同的執行個體，名為 nginx@1.service、 nginx@2.service 和 nginx@3.service:

```
[Unit]
Description=High Availability Nginx
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --rm --name nginx1 -p 80:80 nginx
ExecStop=/usr/bin/docker stop nginx1

[X-Fleet]
X-Conflicts=nginx@*.service
```

>[AZURE.NOTE]  `X-Conflicts` 屬性會告知 CoreOS 只有一個可以在指定的 CoreOS 主機上執行這個容器的執行個體。 如需詳細資訊，請參閱 [單位檔案]。

現在請在 CoreOS 叢集上啟動單位執行個體。 您應該會發現它們在三個不同的機器上執行：

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start nginx@{1,2,3}.service

unit nginx@3.service launched on 00c927e4.../100.79.62.16
unit nginx@1.service launched on 62f0f66e.../100.79.86.62
unit nginx@2.service launched on df85f2d1.../100.78.126.15

```
若要與其中一個單位上執行的 Web 伺服器連線，請傳送一個簡單的要求到裝載此 CoreOS 叢集的雲端服務。

`curl http://coreos-cluster.cloudapp.net`

您會看到從 Nginx 伺服器傳回的預設文字，與以下內容類似：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

您可以嘗試關閉叢集內的一或多部虛擬機器，以確認 Web 服務仍繼續執行。

完成後，請停止並卸載單位。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop nginx@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload nginx@{1,2,3}.service

```

## 後續步驟

* 您可以在 Azure 上三個節點的 CoreOS 叢集嘗試更多動作。 探索如何建立更複雜的叢集使用 Docker，以及閱讀 [Tim Park 的 CoreOS 教學課程中]，建立更有趣的應用程式 [Patrick Chanezon 的 CoreOS 教學課程] [Docker] 文件，以及 [CoreOS 概觀]。

* 若要開始使用 Fleet 和 CoreOS Azure 資源管理員中，請嘗試這 [快速入門範本](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/)。

* 如需 Linux Vm 上使用開放原始碼環境的詳細資訊，請參閱 [Linux 和開放原始碼運算在 Azure 上]。

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[CoreOS Overview]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park's CoreOS Tutorial]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon's CoreOS Tutorial]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[How to Use CoreOS on Azure]: virtual-machines-linux-coreos-how-to.md
[Configure a load-balanced set]: ../load-balancer/load-balancer-internet-getstarted.md
[Launching containers with fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Unit Files]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[busybox Docker Hub image]: https://registry.hub.docker.com/_/busybox/
[nginx Docker Hub image]: https://hub.docker.com/_/nginx/
[Linux and Open-Source Computing on Azure]: virtual-machines-linux-opensource.md

