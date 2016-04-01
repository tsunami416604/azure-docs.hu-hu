
<properties
    pageTitle="如何在 Azure App Service Web Apps 中購買自訂網域名稱"
    description="了解如何在 Azure App Service 中購買搭配 Web 應用程式的自訂網域名稱。"
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="robmcm"/>

# 在 Azure App Service 中購買並設定自訂網域名稱

> [AZURE.SELECTOR]
- [購買網域的 Web 應用程式](custom-dns-web-site-buydomains-web-app.md)
- [內含外部網域的 web 應用程式](web-sites-custom-domain-name.md)
- [包含流量管理員的 Web 應用程式](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)




[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

當您建立 Web 應用程式時，Azure 會將它指派給 azurewebsites.net 的子網域。 例如，如果您的 web 應用程式的名稱為 **contoso**, ，URL 是 **contoso.azurewebsites.net**。 Azure 也會指派虛擬 IP 位址。

若是實際執行的 Web 應用程式，您可能想讓使用者看到自訂網域名稱。 這篇文章說明如何購買並設定自訂網域名稱與 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。 

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 概觀

> [AZURE.NOTE] 請不要嘗試購買網域使用沒有與其相關聯作用中信用卡的訂閱。 這可能會導致您的訂用帳戶被停用。 

如果您沒有 web 應用程式的網域名稱，您可以輕鬆地購買上 [Azure 入口網站](https://portal.azure.com)。 在購買程序期間，您可以選擇將 WWW 和根網域的 DNS 記錄自動對應到您的 Web 應用程式。 您也可以在 Azure 入口網站管理您的網域權限。


使用下列步驟購買網域名稱，並將其指派給您的 Web 應用程式。

1. 在瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 **Web 應用程式** 索引標籤上，按一下 web 應用程式，然後選取名稱 **設定**, ，然後選取 **自訂網域和 SSL**

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. 在 **自訂網域和 SSL** 刀鋒視窗中，按一下 [ **購買網域**。

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. 在 **購買網域** 刀鋒視窗中，使用文字方塊來輸入您想要購買，然後按 enter 鍵的網域名稱。 建議的可用網域將會顯示在文字方塊下方。 選取您想要購買的網域。 您可以選擇一次購買多個網域。 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. 按一下 [ **連絡人資訊** 然後填寫網域連絡資訊表單。

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE] 務必填寫所有必要的欄位正確越好，尤其是電子郵件地址。 如果購買的網域沒有「隱私權保護」，在網域作用前，系統可能會要求驗證您的電子郵件。 在某些情況下，不正確的連絡資訊資料將導致購買網域失敗。 

6. 現在您可以選擇，

    a) 每年「自動續訂」您的網域
    
    b) 選擇加入購買時免費隨附的「隱私權保護」
    
    c) 為 WWW 和根網域「指派預設主機名稱」到目前的 Web 應用程式。 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE] C 選項可用來設定 DNS 繫結，會自動為您的主機名稱繫結。  如此一來，購買程序一旦完成 (除了少數情況下的 DNS 傳播延遲)，您的 Web 應用程式就可以存取自訂網域。 如果您的 Web 應用程式在 Azure 流量管理員後面，您將不會看到指派根網域的選項，因為 A 記錄不能和流量管理員一起使用。 
>
>您可以隨時透過一個 Web 應用程式，指派購買的網域/子網域到另一個 Web 應用程式，反之亦然。 如需詳細資訊，請參閱步驟 8。 

    
7. 按一下 [ **選取** 上 **購買網域** 刀鋒視窗中，則您會看到購買資訊上 **購買確認** 刀鋒視窗。 如果您接受法律條款，然後按一下 **購買**, 將送出您的訂單，您可以監視購買程序 **通知**。 網域訂單可能需要幾分鐘的時間才能完成。 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. 如果您已成功下訂一個網域，您就可以管理網域並指派給您的 Web 應用程式。 按一下 [ **"..."** 在您網域的右側。 然後，您便可以 **取消訂單** 或 **管理網域**。 按一下 [ **管理網域**, ，然後我們可以繫結 **子網域** 到我們的 web 應用程式 **管理網域** 刀鋒視窗。 如果您想要繫結  **子網域** 至不同的 Web 應用程式，然後從執行此步驟在個別的 Web 應用程式的內容。 在這裡，您可以從下拉式功能表選取流量管理員名稱，將網域指派至流量管理員端點 (如果 Web 應用程式在流量管理員後面)。 如此一來，網域/子網域會自動指派給該流量管理員端點後面的所有 Web 應用程式。 

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE] 您可以 「 取消購買 」 獲取完整退款 5 天內。 5 天後您將無法再「取消購買」，取而代之的是您會看到「刪除」網域的選項。 刪除該網域不會退款，並將導致網域從您訂用帳戶釋放變成可用的網域。 

當組態完成之後時，自訂網域名稱會列示在 **主機名稱繫結** web 應用程式的一節。

此時，您應該能夠在瀏覽器中輸入自訂網域名稱，並且能成功移至您的 Web 應用程式。
 


