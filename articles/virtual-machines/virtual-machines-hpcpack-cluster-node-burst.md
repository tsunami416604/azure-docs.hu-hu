<properties
 pageTitle="將高載節點新增至 HPC Pack 叢集 | Microsoft Azure"
 description="了解如何將在雲端服務中隨選做為計算資源來執行的背景工作角色執行個體，新增至 Azure 中的 HPC Pack 前端節點。"
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
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 將做為運算資源的隨選「高載」節點 (背景工作角色執行個體) 新增至 Azure 中的 HPC Pack 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文將說明如何將 Azure 「 高載 」 節點 (背景工作角色執行個體
在雲端服務中執行) 視做為計算資源
現有 HPC Pack 前端節點在 Azure 中。 這可讓您相應增加 Azure 中的 HPC 叢集的運算能力，且無需維護一組預先設定的運算節點 VM。

![高載節點][高載]

>[AZURE.TIP] 如果您使用 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md) 在 Azure 中，建立叢集
您可以在您的自動化包含 Azure 高載節點
安全性旋鈕。

這篇文章中的步驟將協助您快速讓加入 Azure 節點
雲端式 HPC Pack 前端節點 VM 用於測試或概念證明部署。 此程序基本上是
相同 「 高載至 Azure 」 以將雲端運算的容量
內部部署 HPC Pack 叢集。 如需教學課程，請參閱 [設定混合式運算叢集使用 Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 如需
請參閱詳細的指引和實際執行部署的考量
[使用 Microsoft HPC 擴充至 Azure
組件](https://technet.microsoft.com/library/gg481749.aspx)。

如果您想要使用 A8 或 A9 計算密集型執行個體大小，請參閱
[關於 A8、 A9、 A10 和 A11 大量運算執行個體](virtual-machines-a8-a9-a10-a11-specs.md)。

## 先決條件

* **在 Azure VM 中部署 HPC Pack 前端節點** -請參閱 [部署 HPC
在 Azure 的 VM 組件的前端節點](virtual-machines-hpcpack-cluster-headnode.md) 的
在傳統 (服務管理) 部署模型中建立叢集前端節點的步驟。

* **Azure 訂用帳戶** -若要加入 Azure 節點，您可以選擇相同
用來部署前端節點 VM，或不同的訂閱
訂用帳戶 (或訂閱)。

* **核心配額** -您可能需要增加核心配額，特別是如果您選擇部署多核心大小的數個 Azure 節點。 若要增加配額， [開啟線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。

## 步驟 1：建立雲端服務和儲存體帳戶以新增 Azure 節點。

若要設定下，部署時需要使用 Azure 傳統入口網站或對等的工具
您的 Azure 節點:

* 新的 Azure 雲端服務
* 新的 Azure 儲存體帳戶

>[AZURE.NOTE] 不要重複使用現有的雲端服務訂閱中。 也不要
將不同的自訂雲端服務封裝部署到此雲端服務。
HPC Pack 會自動部署雲端服務封裝時您
啟動 (佈建) Azure 節點。

**注意事項**

* 您打算建立的每個 Azure 節點範本設定個別的雲端服務。 不過，您可以將同一個儲存體帳戶用於多個節點範本。

* 您通常應將部署的雲端服務與儲存體帳戶放置在相同區域中。




## 步驟 2：設定 Azure 管理憑證

若要加入 Azure 節點當做計算資源，您必須有管理
在前端節點和對應的憑證上傳的憑證
 要用於部署的 Azure 訂閱。

對於此案例中，您可以選擇 **Default HPC Azure Management
憑證** 該 HPC Pack 安裝和自動設定
前端節點。 此憑證可用於測試用途，
概念證明的部署。 若要使用此憑證，只需上傳
檔案 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer 從前端節點 VM 以
。

若要設定管理憑證的其他選項，請參閱
[若要設定 Azure 管理憑證的 Azure 高載案例
部署](http://technet.microsoft.com/library/gg481759.aspx)。

## 步驟 3：將 Azure 節點部署至叢集



加入及啟動步驟
在此案例中的 azure 節點通常是指那些用於相同
內部部署前端節點。 如需詳細資訊，請參閱下列
各個區段 [使用 Microsoft HPC Pack 部署 Azure 節點的步驟](https://technet.microsoft.com/library/gg481758.aspx):

* 建立 Azure 節點範本

* 將 Azure 節點新增至 Windows HPC 叢集

* 啟動 (佈建) Azure 節點

節點新增並啟動之後，即可供您用來執行叢集工作。

如果您在部署 Azure 節點時遇到問題，請參閱 [疑難排解
使用 Microsoft HPC Azure 節點的部署
組件](http://technet.microsoft.com/library/jj159097(v=ws.10).aspx)。

## 後續步驟

* 如果您想要的方式
自動成長或壓縮根據 Azure 運算資源
目前的工作負載的作業和工作，在叢集上，請參閱 [漲大及縮小 Azure 計算資源中的 HPC Pack 叢集](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-hpcpack-cluster-node-burst/burst.png

