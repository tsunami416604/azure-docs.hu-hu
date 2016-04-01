<properties
    pageTitle="Batch 服務配額和限制 | Microsoft Azure"
    description="深入了解使用 Azure Batch 服務的配額、限制及條件約束"
    services="batch"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2015"
    ms.author="danlep"/>



# Azure Batch 服務的配額和限制

本文列出您可以搭配 Azure Batch 服務使用的特定資源的預設值和最大限制。 這些限制中大部分都是 Azure 套用到您的訂用帳戶或 Batch 帳戶的配額。

如果您計劃執行實際執行 Batch 工作負載，您可能需要增加一或多個高於預設值的配額。 若要提升配額，請開啟線上客戶支援要求，不另外加收費用。

>[AZURE.NOTE] 配額為信用限制，而不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

## 訂用帳戶配額
資源|預設限制|上限
---|---|---
Batch 帳戶 (每一區域的每一訂用帳戶)|1|50

## Batch 帳戶配額
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## 其他限制
資源|上限
---|---
每個計算節點的工作|4 x 節點的核心數目

## 檢視 Batch 配額

檢視您的批次帳戶配額中 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站中，按一下 [ **批次帳戶** 和批次帳戶的名稱。

2. 在 [帳戶] 分頁中，按一下 [ **設定** > **屬性**。

    ![Batch 帳戶配額][account_quotas]

3. 在 **屬性** 刀鋒視窗中，檢閱目前套用至批次帳戶的配額。

## 增加配額

使用下列步驟，以要求配額增加 Azure 入口網站 (您也可以要求增加 [Azure 傳統入口網站](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/))。

1. 在預覽入口網站的儀表板，按一下 [ **說明 + 支援**。

2. 按一下 [ **建立支援要求 > 基本概念**。

3. 在 **基本概念** 刀鋒視窗中，執行下列動作 ︰

    a. 在 **問題類型**, ，請選取 **配額**。

    b. 選取您的訂閱。

    c. 在 **服務**, ，請選取 **批次服務**。

    d. 在 **支援計劃**, ，請選取 **Azure 支援方案開發人員**。

    按一下 [ **下一步**。

4. 在 **問題** 刀鋒視窗中，執行下列動作 ︰

    a. 在 **問題類型**, ，請選取 **批次**。

    b. 在 **詳細資料**, 、 列出配額，或是想要變更在特定的帳戶] 和 [新限制的配額。

    按一下 [ **下一步**。

5. 在 **連絡資訊** 刀鋒視窗中，輸入您的連絡人詳細資料，然後按一下 [ **下一步**。

6. 按一下 [ **建立** 送出新的支援要求。

Azure 支援會與您連絡。 完成要求需要花費最多 2 個工作天。

## 相關主題

* [建立和管理 Azure Batch 帳戶](batch-account-create-portal.md)

* [Azure Batch 功能概觀](batch-api-basics.md)

* [Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG


