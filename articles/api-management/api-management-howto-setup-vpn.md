<properties
    pageTitle="如何在 Azure API 管理中設定 VPN 連線"
    description="瞭解如何在 API 管理中設定 VPN 連線，並透過它存取 Web 服務。"
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="antonba"/>


# 如何在 Azure API 管理中設定 VPN 連線

API 管理的 VPN 支援可讓您將 API 管理閘道連接到 Azure 虛擬網路。 這可讓 API 服務客戶安全地連線到其內部部署或公用網際網路無法存取的後端 Web 服務。

## <a name="enable-vpn"> </a>啟用 VPN 連線

>VPN 連線僅供**進階**層使用。 若要切換到它，開啟 [API 管理服務中的 [Azure 傳統入口網站 []][] ，然後開啟 **延展** ] 索引標籤。 在 [**一般**] 區段下選取進階層，然後按一下 [儲存]。

若要啟用 VPN 連線，開啟 [API 管理服務中的 [Azure 傳統入口網站 []][] 並切換至 **設定** ] 索引標籤。

在 VPN 區段中，將 [**VPN 連線**] 切換為 [**開啟**]。

![API 管理執行個體的設定索引標籤][api-management-setup-vpn-configure]

您現在會看見佈建 API 管理服務所在的所有區域的清單。

為每個區域選取 VPN 和子網路。 VPN 的清單是依據您要設定的區域中所設定 Azure 訂閱可用的虛擬網路而填入。

![選取 VPN][api-management-setup-vpn-select]

按一下畫面底部的 [**儲存**]。 更新時，您將無法透過 Azure 傳統入口網站對 API 管理服務執行其他作業。 服務閘道將保持可用，而執行時期呼叫應該不會受到影響。

請注意，閘道的 VIP 位址將在每次啟用或停用 VPN 時變更。

## <a name="connect-vpn"> </a>連線到 vpn 的 web 服務

在您的 API 管理服務連接至 VPN 之後，於虛擬網路內存取 Web 服務與存取公用服務沒有差別。 只需在建立新 API 或編輯現有 API 時，將 Web 服務的本機位址或主機名稱 (如果已設定 Azure 虛擬網路的 DNS 伺服器) 輸入到 [**Web 服務 URL**] 欄位。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>相關內容

* [教學課程: 建立跨單位虛擬網路的 ][]
* [如何使用 API 偵測器來追蹤呼叫 Azure API 管理 [] 中][]


[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png 
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png 
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png 
[enable vpn connections]: #enable-vpn 
[connect to a web service behind vpn]: #connect-vpn 
[related content]: #related-content 
[azure classic portal]: https://manage.windowsazure.com/ 
[tutorial: create a cross-premises virtual network for site-to-site connectivity]: ../virtual-networks-create-site-to-site-cross-premises-connectivity 
[how to use the api inspector to trace calls in azure api management]: api-management-howto-api-inspector.md 

