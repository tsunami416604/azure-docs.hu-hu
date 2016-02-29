<properties
    pageTitle="如何將 Azure API 管理服務執行個體部署到多個 Azure 區域"
    description="瞭解如何將 Azure API 管理服務執行個體部署到多個 Azure 區域。" 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="sdanie"/>

# 如何將 Azure API 管理服務執行個體部署到多個 Azure 區域

API 管理支援多區域部署，可讓 API 發行者跨所需的任意數量 Azure 區域發佈單一 API 管理服務。 這有助於降低地理上分散的 API 取用者感受到的要求延遲，並且可以改善某個區域離線時服務的可用性。 

最初建立 API 管理服務時，它只包含一個 [單元][] 並且位在單一的 Azure 區域，而指定為主要區域中。 可透過 Azure 傳統入口網站輕鬆加入其他區域。 API 管理閘道伺服器會部署到每個區域，並且將呼叫流量遞送到最近的閘道。 如果區域離線，流量會自動重新導向到下一個最近的閘道。 

> [AZURE.IMPORTANT] 多重區域部署僅供以 **[Premium][]** 層。

## <a name="add-region"> </a>API 管理服務執行個體部署到新的區域

若要開始使用，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。

![發佈者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

瀏覽至 **延展** API 管理服務執行個體在 Azure 傳統入口網站] 索引標籤。 

![調整索引標籤][api-management-scale-service]

若要部署新區域，請按一下主要區域下的下拉式清單，然後從清單選擇一個區域。

![加入區域][api-management-add-region]

選取區域之後，從下拉式清單選擇新區域的單位數量。

![指定單位][api-management-select-units]

一旦設定所需的區域和單位，按一下 **儲存**。

## <a name="remove-region"> </a>從區域刪除 API 管理服務執行個體

若要從區域移除 API 管理服務執行個體，請瀏覽至 **延展** API 管理服務執行個體在 Azure 傳統入口網站] 索引標籤。 

![調整索引標籤][api-management-scale-service]

按一下 [ **X** 右邊的所需的區域移除。  

![移除區域][api-management-remove-region]

一旦移除所需的區域，按一下 [ **儲存**。


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/


