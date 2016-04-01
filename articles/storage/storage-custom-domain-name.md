<properties 
    pageTitle="針對 Azure 儲存體帳戶中的 Blob 資料設定網域名稱 | Microsoft Azure" 
    description="了解如何設定自訂網域名稱，用以存取 Azure 儲存體帳戶中的 Blob 資料。" 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/03/2015" 
    ms.author="tamram"/>


# 針對 Azure 儲存體帳戶中的 Blob 資料設定自訂網域名稱

## 概觀

您可以設定自訂網域名稱，以供存取 Azure 儲存體帳戶中的 Blob 資料。 Blob 服務的預設端點是 https://<*mystorageaccount*>。 <containername>@<storageaccountname>.blob.core.windows.net。 如果您將自訂網域和對應子網域例如 **www.contoso.com** 至儲存體帳戶的 blob 端點，然後您的使用者也可以存取的 blob 儲存體帳戶，使用該網域中的資料。 


> [AZURE.NOTE]  這項工作中的程序適用於 Azure 儲存體帳戶。 若是雲端服務，請參閱<a href = "/develop/net/common-tasks/custom-dns/">設定 Azure 雲端服務的自訂網域名稱</a>；若是網站，請參閱<a href="/develop/net/common-tasks/custom-dns-web-site/">設定 Azure 網站的自訂網域名稱</a>。 

將自訂網域指向儲存體帳戶之 Blob 端點的方法有兩種。 最簡單的方法是建立 CNAME 記錄，以將自訂網域和子網域對應至 Blob 端點。 CNAME 記錄是將來源網域對應至目的地網域的 DNS 功能。 在這種情況下，來源網域是您的自訂網域和子網域 (請注意，子網域一律是必要的項目)。 目的地網域是 Blob 服務端點。

將自訂網域對應至 blob 端點的程序，不過，造成短暫的停機時間的網域中註冊的網域時 [Azure 傳統入口網站](manage.windowsazure.com)。 如果您的自訂網域目前支援的應用程式與服務等級協定 (SLA) 要求那里任何停機時間，則您可以使用 Azure **asverify** 子網域來提供中繼註冊步驟，讓使用者能夠在 DNS 對應時存取網域。

下表展示的範例 Url 來存取名為的儲存體帳戶中的 blob 資料 **mystorageaccount**。 針對儲存體帳戶註冊自訂網域 **www.contoso.com**:

資源類型|URL 格式
---|---
儲存體帳戶|**預設 URL:** http://mystorageaccount.blob.core.windows.net<p>**自訂網域 URL:** http://www.contoso.com</td>
Blob|**預設 URL:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**自訂網域 URL:** http://www.contoso.com/mycontainer/myblob
根容器|**預設 URL:** http://mystorageaccount.blob.core.windows.net/myblob 或 http://mystorageaccount.blob.core.windows.net/ root/myblob<p>**自訂網域 URL:** http://www.contoso.com/myblob 或 http://www.contoso.com/ root/myblob

## 針對儲存體帳戶註冊自訂網域

如果您不需要顧及使用者會短暫地無法存取網域的問題，抑或是自訂網域目前未主控應用程式，您可以使用此程序來註冊自訂網域。 

若自訂網域目前支援不允許發生任何停機狀況的應用程式，請執行 <a href="#register-asverify">使用中繼的 asverify 子網域針對儲存體帳戶註冊自訂網域</a> 列出的程序。

若要設定自訂網域名稱，您必須向網域註冊機構建立新的 CNAME 記錄。 CNAME 記錄能指定網域名稱的別名。在這種情況下，它能將自訂網域的位址對應至儲存體帳戶的 Blob 服務端點。

各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念
都一樣。 請注意，許多基本網域註冊套件並未提供 DNS 組態，因此您可能需要先升級網域註冊套件，然後再建立 CNAME 記錄。 

1.  在 [Azure 傳統入口網站](manage.windowsazure.com), ，瀏覽至 **儲存體** ] 索引標籤。

2.  在 **儲存體** 索引標籤上，按一下您要對應自訂網域的儲存體帳戶名稱。

3.  按一下 [ **設定** ] 索引標籤。

4.  按一下畫面底部 **管理網域** 顯示 **管理自訂網域** ] 對話方塊。 在對話方塊頂端的文字中，你可以看見有關如何建立 CNAME 記錄的資訊。 此程序，應該忽略指的是文字 **asverify** 子網域。

5.  登入 DNS 註冊機構的網站，然後移至
    DNS 管理頁面。 您可能會發現這一節中例如 **網域
    名稱**, ，**DNS**, ，或 **名稱伺服器管理**。

6.  尋找管理 CNAME 的區段。 您可能需要移至
    進階的設定] 頁面上，並尋找的單字 **CNAME**, ，**別名**,，
    或 **子網域**。

7.  建立新的 CNAME 記錄，並提供子網域別名，例如 **www** 或 **相片**。 然後
    提供主機名稱，也就是 Blob 服務端點，格式 **mystorageaccount.blob.core.windows.net** (其中 **mystorageaccount** 是儲存體帳戶的名稱)。 若要使用的主機名稱為您提供的文字中 **管理自訂網域** ] 對話方塊。

8.  您已建立 CNAME 記錄後，回到 **管理自訂網域** ] 對話方塊中，輸入您的自訂網域，包括子網域中的名稱和 **自訂網域名稱** 欄位。 例如，如果您的網域是 **contoso.com** 子網域，而且 **www**, ，輸入 **www.contoso.com**; 如果您的子網域為 **相片**, ，輸入 **photos.contoso.com**。 請注意，子網域是必要項目。

9. 按一下 [ **註冊** 按鈕以註冊您的自訂網域。 

    如果註冊成功，您會看到訊息 **您的自訂網域為作用中**。 現在只要使用者擁有適當的權限，便能檢視自訂網域中的 Blob 資料。 

## 使用中繼的 asverify 子網域針對儲存體帳戶註冊自訂網域

如果自訂網域目前需支援不得發生停機時間之 SLA 的應用程式，您可以使用此程序來註冊自訂網域。 透過建立從 asverify.&lt;subdomain&gt;.&lt;customdomain&gt; 指向 asverify.&lt;storageaccount&gt;.blob.core.windows.net 的 CNAME，可以向 Azure 預先註冊網域。 接著，您可以建立從 &lt;subdomain&gt;.&lt;customdomain&gt; 指向 &lt;storageaccount&gt;.blob.core.windows.net 的第二個 CNAME，將前往自訂網域的流量引導至 Blob 端點。

asverify 子網域是 Azure 認可的特殊子網域。 附加在前面 **asverify** 自己的子網域，您允許 Azure 在不修改網域之 DNS 記錄的情況下認可自訂網域。 一旦修改網域的 DNS 記錄，它將能在沒有停機時間的情況下對應至 Blob 端點。

1.  在 [Azure 傳統入口網站](manage.windowsazure.com), ，瀏覽至 **儲存體** ] 索引標籤。

2.  在 **儲存體** 索引標籤上，按一下您要對應自訂網域的儲存體帳戶名稱。

3.  按一下 [ **設定** ] 索引標籤。

4.  按一下畫面底部 **管理網域** 顯示 **管理自訂網域** ] 對話方塊。 在對話方塊頂端的文字，您可以看見有關如何建立 CNAME 記錄使用 **asverify** 子網域。

5.  登入 DNS 註冊機構的網站，然後移至
    DNS 管理頁面。 您可能會發現這一節中例如 **網域
    名稱**, ，**DNS**, ，或 **名稱伺服器管理**。

6.  尋找管理 CNAME 的區段。 您可能需要移至
    進階的設定] 頁面上，並尋找的單字 **CNAME**, ，**別名**,，
    或 **子網域**。

7.  建立新的 CNAME 記錄並提供包含 asverify 子網域的子網域別名。 例如，您所指定的子網域會採用格式 **asverify.www** 或 **asverify.photos**。 然後
    提供主機名稱，也就是 Blob 服務端點，格式 **asverify.mystorageaccount.blob.core.windows.net** (其中 **mystorageaccount** 是儲存體帳戶的名稱)。 若要使用的主機名稱為您提供的文字中 **管理自訂網域** ] 對話方塊。

8.  您已建立 CNAME 記錄後，回到 **管理自訂網域** ] 對話方塊中，輸入您的自訂網域名稱中 **自訂網域名稱** 欄位。 例如，如果您的網域是 **contoso.com** 子網域，而且 **www**, ，輸入 **www.contoso.com**; 如果您的子網域為 **相片**, ，輸入 **photos.contoso.com**。 請注意，子網域是必要項目。

9.  按一下核取方塊，指出 **進階 ︰ 使用 'asverify' 子網域將核取方塊**。 

10. 按一下 [ **註冊** 按鈕以預先註冊自訂網域。 

    Custom 成功時，您會看到訊息 **您的自訂網域為作用中**。 

11. 此時，自訂網域已通過 Azure 的驗證，不過前往網域的流量尚未路由傳送到儲存體帳戶。 若要完成程序，請返回 DNS 註冊機構的網站，然後建立將子網域對應至 Blob 服務端點的另一個 CNAME 記錄。 例如，指定做為子網域 **www** 或 **相片**, ，並將主機名稱為 **mystorageaccount.blob.core.windows.net** (其中 **mystorageaccount** 是儲存體帳戶的名稱)。 待這個步驟完成後，自訂網域的註冊作業也宣告完成。

12. 最後，您可以使用您所建立的 CNAME 記錄 **asverify**, ，因為它只有在中繼步驟才需要。

現在只要使用者擁有適當的權限，便能檢視自訂網域中的 Blob 資料。

## 確認自訂網域參考 Blob 服務端點

若要驗證自訂網域是否確實對應至 Blob 服務端點，請在儲存體帳戶內的公用容器中建立 Blob。 接著，在網頁瀏覽器中使用以下格式的 URI 存取 Blob：

-   http://<*subdomain.customdomain*> / <*mycontainer*> / <*myblob*>

例如，您可以使用下列 URI 透過
**photos.contoso.com** 自訂子網域中之 blob 對應程式
**myforms** 容器 ︰

-   http://photos.contoso.com/myforms/applicationform.htm

## 其他資源

-   <a href="http://msdn.microsoft.com/library/azure/gg680307.aspx">如何將 CDN 內容對應至自訂網域</a>
 


