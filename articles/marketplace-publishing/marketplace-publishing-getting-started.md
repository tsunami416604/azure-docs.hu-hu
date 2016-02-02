<properties
   pageTitle="概述如何建立及部署優惠至 Marketplace | Microsoft Azure"
   description="了解在 Azure Marketplace 中成為核准的 Microsoft 賣方，以及建立及部署虛擬機器映像、範本、資料服務或開發人員服務所需的步驟"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="hascipio" />


# 如何將優惠發佈至 Azure Marketplace

本文可協助賣方建立解決方案並部署到 Azure Marketplace，讓其他的 Azure 客戶和合作夥伴可進行購買及利用。

首先，您需要以發行者的身分，定義您的公司提供的解決方案類型。 Azure Marketplace 支援多種解決方案，您必須為每個解決方案進行略為不同的工作，才能成功發佈到 Marketplace。

解決方案類型：

- 虛擬機器映像
- 開發人員服務
- 資料服務
- 解決方案範本

不同類型的解決方案之間有一些相同的步驟。 本文提供簡短的概觀，讓您了解每種類型的解決方案必須完成哪些步驟。
> [AZURE.NOTE] 開始在 Azure Marketplace 上進行任何工作之前，必須取得預先核准。 這不適用於資料服務發佈者。

| | 虛擬機器映像| 開發人員服務| 資料服務| 方案範本|
|----|----|----|----|----|
| **取得預先核准**| [Microsoft Azure 認證 ][link-certification]| [Microsoft Azure 認證 ][link-certification]| n/a| [Microsoft Azure 認證 ][link-certification]|
| **步驟 1: 註冊賣方帳戶**| [Microsoft 賣方帳戶: 建立和註冊 ][link-accts]| [Microsoft 賣方帳戶: 建立和註冊 ][link-accts]| [Microsoft 賣方帳戶: 建立和註冊 ][link-accts]| [Microsoft 賣方帳戶: 建立和註冊 ][link-accts]|
| **步驟 2: 建立您的產品**| [非技術性的一般必要條件](marketplace-publishing-pre-requisites.md)| [非技術性的一般必要條件](marketplace-publishing-pre-requisites.md)| [非技術性的一般必要條件](marketplace-publishing-pre-requisites.md)| [非技術性的一般必要條件](marketplace-publishing-pre-requisites.md)|
| | [VM 技術必要條件 ][link-single-vm-prereq]| 開發人員服務技術性必要條件| [資料服務技術的必要條件](marketplace-publishing-data-service-creation-prerequisites.md)| [方案範本技術必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)|
| | [VM 映像發佈指南 ][link-single-vm]| 開發人員服務發行指導| [資料服務的發行指導](marketplace-publishing-data-service-creation.md)| [方案範本發行指導](marketplace-publishing-solution-template-creation.md)|
| | [Azure Marketplace 行銷內容指南 ][link-pushstaging]| [Azure Marketplace 行銷內容指南 ][link-pushstaging]| [Azure Marketplace 行銷內容指南 ][link-pushstaging]| [Azure Marketplace 行銷內容指南 ][link-pushstaging]|
| **步驟 3: 發送至預備環境優惠**| [在預備環境中測試 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md)| 在預備環境中測試您的開發人員服務供應項目| [在預備環境中測試您的資料服務供應項目](marketplace-publishing-data-service-test-in-staging.md)| [在預備環境中測試您的方案範本](marketplace-publishing-solution-template-test-in-staging.md)|
| **步驟 4: 將您提供的服務部署至 Marketplace**| [將您提供的服務部署至服務商場 ][link-pushprod]| [將您提供的服務部署至服務商場 ][link-pushprod]| [將您提供的服務部署至服務商場 ][link-pushprod]| [將您提供的服務部署至服務商場 ][link-pushprod]|

## 支援

- [為發行者 ][suppt-general]
- [了解賣方 insights 報告 ][suppt-rpt-insights]
- [了解支付額報告 ][suppt-rpt-payouts]
- [疑難排解常見問題發行市場 ][suppt-common]

## 其他資源

- 若要深入了解使用的入口網站，請參閱 [入口網站，您將需要](marketplace-publishing-portals.md)。

**虛擬機器**

- [設定 Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [在內部建立的 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
- [建立在 Azure 預覽入口網站中執行 Windows 的虛擬機器](../virtual-machines-windows-tutorial/)

**資料服務**

- [資料服務的 OData 對應](marketplace-publishing-data-service-creation-odata-mapping.md)
- [資料服務的 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [資料服務的 OData 對應範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)


[suppt-general]: marketplace-publishing-get-publisher-support.md 
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md 
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md 
[suppt-common]: marketplace-publishing-support-common-issues.md 
[link-certification]: marketplace-publishing-azure-certification.md 
[link-accts]: marketplace-publishing-accounts-creation-registration.md 
[link-single-vm]: marketplace-publishing-vm-image-creation.md 
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md 
[link-multi-vm]: marketplace-publishing-solution-template-creation.md 
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md 
[link-datasvc]: marketplace-publishing-data-service-creation.md 
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md 
[link-devsvc]: marketplace-publishing-dev-service-creation.md 
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md 
[link-pushstaging]: marketplace-publishing-push-to-staging.md 
[link-pushprod]: marketplace-publishing-push-to-production.md 

