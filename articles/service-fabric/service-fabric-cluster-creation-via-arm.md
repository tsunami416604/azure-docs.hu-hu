<properties
   pageTitle="使用 ARM 範本設定 Service Fabric 叢集 | Microsoft Azure"
   description="使用 ARM 範本設定 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# 使用 ARM 範本設定 Service Fabric 叢集

本頁面可協助您使用 ARM 範本來設定「Service Fabric 叢集」。 這會假設您的訂用帳戶有足夠的核心來部署將構成此叢集的 IaaS VM。

## 必要條件

- 如果您想要設定安全的叢集，請確定已上傳的 X509 憑證金鑰保存庫。 您必須是來源保存庫 URL、 憑證 URL 和憑證指紋。
-  請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 如需有關如何。

## 取得範例 ARM 範本

1. 範例 ARM 範本時使用 [Azure 的快速入門範本組件庫在 github 上 ](https://github.com/Azure/azure-quickstart-templates)。 以名稱 「 服務-架構...」 開頭的所有服務網狀架構範本 . 您可以搜尋 「 結構 」 的儲存機制，或只是向下捲動到集合的範例範本。
2. 為了協助您快速找出您可能要尋找的目標，範本已經依下列方式命名：
    1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) 指定 5 個節點的單一節點不安全的叢集範本。 
    3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) 指定已啟用的 WAD 5 個節點單一節點叢集安全範本。 
    4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) 表示已啟用的 WAD 的 10 個節點的兩個節點安全叢集範本。 
    

## 建立自訂 ARM 範本

2. 您有兩種選擇 
    1. 您可以從範例範本來取得 [Azure 的快速入門範本組件庫在 github 上 ](https://github.com/Azure/azure-quickstart-templates) 並對它進行變更。
    2. 登入 Azure 入口網站，然後使用 Service Fabric 入口網站頁面來產生可供您自訂的範本。 此做法的程序說明如下。
3. 登入 Azure 入口網站 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)。
2. 中所述，建立叢集的程序進行 [透過入口網站中建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md) , ，但請不要按 ***建立**, 改為摘要，請下載範本。

 ![DownloadTemplate][DownloadTemplate]

## 使用 Azure PS 將 ARM 範本部署到 Azure

請參閱 [部署 ARM 範本使用 PS ](resource-group-template-deploy.md) 如需詳細指示如何部署使用 PowerShell 的範本。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 
- [管理 Service Fabric 應用程式在 Visual Studio](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png



