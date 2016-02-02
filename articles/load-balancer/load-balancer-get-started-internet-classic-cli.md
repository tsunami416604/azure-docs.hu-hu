<properties 
   pageTitle="開始使用 Azure CLI 在傳統部署模型中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 Azure CLI 在傳統部署模型中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/06/2015"
   ms.author="joaoma" />


# 開始在 Azure CLI 中建立網際網路面向的負載平衡器 (傳統)

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]



[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## 使用 CLI 逐步建立網際網路面向的負載平衡器

本指南根據上述案例說明如何建立網際網路面向的負載平衡器。

1. 

2. 執行 **azure config mode** 命令，以切換為傳統模式，如下所示。

        azure config mode asm

    預期的輸出：

        info:    New mode is asm



## 建立端點與負載平衡器集

此案例假設虛擬機器 "web1" 和 "web2" 已經建立。 
本指南將使用連接埠 80 作為公用連接埠，以及 80 作為本機連接埠，建立負載平衡器集。 探查連接埠也設定在連接埠 80 上，並將負載平衡器集稱為 "lbset"


### 步驟 1



    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset 

所使用的參數：






## 步驟 2

將第二個虛擬機器 "web2" 新增到負載平衡器集。

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## 步驟 3



    azure vm show web1

輸出將是：

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## 為虛擬機器建立遠端桌面端點



    azure vm endpoint create web1 54580 -k 3389 

## 從負載平衡器移除虛擬機器

您必須從虛擬機器刪除與負載平衡器集相關聯的端點。 一旦移除端點，虛擬機器就不再屬於負載平衡器集。

 

    azure vm endpoint delete web1 tcp-80-80

>[AZURE.NOTE] 


## 後續步驟












