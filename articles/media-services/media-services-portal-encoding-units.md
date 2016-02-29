<properties
    pageTitle="如何使用 Azure 傳統入口網站調整媒體處理"
    description="了解如何指定要佈建給帳戶的隨選串流保留單元和編碼保留單元，以調整媒體服務。"
    services="media-services"
    documentationCenter=""
    authors="juliako,milangada"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="juliako"/>


# 如何使用 Azure 傳統入口網站調整媒體處理

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [入口網站](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## 概觀

媒體服務帳戶是與可決定媒體處理工作速度的保留單元類型相關聯。 您可以在下列的保留的單元類型之間選擇: **基本**, ，**標準**, ，或 **高階**。 例如，相同的編碼工作執行的速度會當您使用 **標準** 保留單元類型和比較 **基本** 型別。 如需詳細資訊，請參閱 [編碼保留單元類型](http://azure.microsoft.com/blog/author/milanga)。

除了指定保留單元類型之外，您還可以指定使用編碼保留單元來佈建帳戶。 佈建的編碼保留單元數目可決定給定帳戶中可同時處理的媒體工作數目。 例如，如果帳戶有 5 個保留單元，則只要有工作需要處理，就會同時執行 5 個媒體工作。 剩餘的工作會在佇列中等待，且隨著執行中的工作完成，就立即循序地挑選來開始處理。 如果帳戶未佈建任何保留單元，則會循序地挑選工作。 在此情況下，一件工作完成與下一件工作開始之間的等待時間，視系統中的資源可用性而定。

>[AZURE.IMPORTANT] 保留的單元適用於平行處理所有的媒體處理，包括工作使用 Azure Media Indexer 編製索引。  不過，不同於編碼、 編製索引工作不會處理速度與速度的保留單位。

若要變更保留單元類型以及編碼保留單元數目，請執行下列動作：

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **媒體服務**。 接著，按一下媒體服務的名稱。

2. 選取 **編碼** 頁面。

    若要變更 **保留單元類型**, ，請按 BASIC、 STANDARD 或 PREMIUM。

    若要變更所選取保留的單元類型的保留單元數目，請使用 **編碼** 滑桿。


    ![Processors page](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)


    >[AZURE.NOTE] The following data centers do not offer the Premium reserved unit type: Singapore, Hong Kong, Osaka, Beijing, Shanghai.

3. 按 [儲存] 以儲存您的變更。

    新的編碼保留單元會在您按 [儲存] 後立即配置。

    >[AZURE.NOTE]計算成本時會使用 24 小時內指定的最大單元數。

##配額和限制

配額和限制以及如何開啟支援票證的相關資訊，請參閱 [配額和限制](media-services-quotas-and-limitations.md)。



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

