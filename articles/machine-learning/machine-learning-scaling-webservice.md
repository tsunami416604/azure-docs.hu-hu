<properties
   pageTitle="調整 Web 服務 | Microsoft Azure"
   description="了解如何藉由增加並行要求和新增新端點的方式調整 Web 服務。"
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure machine learning, web services, operationalization, scaling, endpoint, concurrency"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/27/2015"
   ms.author="neerajkh"/>

# 調整 Web 服務

## 增加並行要求

根據預設，系統將每個發佈的 Web 服務設定為支援 20 個並行要求。 您可以增加此並行模式設為 200 的並行要求透過 [Azure 傳統入口網站](https://manage.windowsazure.com/) 如下圖所示。

移至 [Azure 傳統入口網站](https://manage.windowsazure.com/), 、 按一下左邊的 [機器學習] 圖示，選取用於發佈 web 服務的工作空間、 按一下所需的 web 服務、 選取所需的並行存取，增加的端點，然後按一下 **設定**。 使用滑桿以提高並行處理，然後按一下 **儲存** 下方面板上。

若要增加並行處理，請參閱 [調整 API 端點](machine-learning-scaling-endpoints.md)。

   ![機器學習中，調整端點。][] 1

## 在相同 Web 服務新增新的端點

調整 Web 服務的規模大小是常見的工作，目的是支援 200 個以上的並行要求、透過多個端點提高可用性、或是為不同 Web 服務的取用者提供不同端點。 使用者為同一 Web 服務新增額外的端點，即可增加其規模大小。 使用者可以新增其他端點，透過 [Azure 傳統入口網站](https://manage.windowsazure.com/) 如下圖所示:

移至 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下左邊的 [機器學習] 圖示，選取用於發佈 web 服務的工作空間中，按一下所需的 web 服務，按一下 **新增端點** 底部面板，然後再提供新的端點名稱、 描述和所需的並行存取。

若要加入新端點，請參閱 [建立端點的](machine-learning-create-endpoint.md)。

   ![機器學習，加入新端點]。[] 2

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
