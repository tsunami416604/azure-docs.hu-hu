<properties
   pageTitle="建立和註冊發佈者帳戶 | Microsoft Azure"
   description="關於建立 Microsoft 賣方帳戶的指示，經過核准後即可在 Azure Marketplace 上銷售各種優惠類型。"
   services="Azure Marketplace"
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
   ms.date="12/06/2015"
   ms.author="hascipio"/>

# 建立 Microsoft 賣方帳戶 
本文將逐步引導您完成要成為 Azure Marketplace 核准的 Microsoft 賣方所必須執行的帳戶建立和註冊程序。

## 1.建立 Microsoft 帳戶
> [AZURE.WARNING] 若要開始發行程序，您必須建立 Microsoft 帳戶。 此帳戶將用來註冊和登入都 **Microsoft 開發人員中心** 和 **Azure 發佈入口網站**。 您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。 其不應該特別屬於服務或優惠。

表單的使用者名稱的位址應該是在網域上，而且受到您的 IT 團隊 (例如 publishing@example.com)。 付款方式、稅務資訊和報告都將透過這個帳戶路由傳送。

  > [AZURE.WARNING] Microsoft 帳戶註冊不支援 「 Azure 」 和 「 Microsoft 」 之類的字。 請避免使用這些字，以完成帳戶建立及註冊程序。

### 範例的指示

1. 在公司的網域內建立通訊群組清單 (DL) 或安全性群組 (SG)。
  - 將您的上線小組加入 DL。
  - DL 必須在執行中才能收到確認電子郵件。
  - 使用電子郵件地址的 marketplace@example.com dl。
  - 這必須在內部系統中完成。
2. 開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入現有帳戶。
3. 使用 DL 電子郵件註冊 Microsoft 帳戶。
 - 您可以在 Microsoft 帳戶註冊 [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx)。
 - 使用 marketplace@example.com 做為電子郵件地址。
 - 您的 Microsoft 帳戶識別碼現在是 marketplace@example.com。
4. 註冊時，請使用有效的電話號碼。 如果需要身分識別驗證，系統將以簡訊或自動語音的方式傳送驗證碼。
5. 請確認傳送至 DL 的電子郵件地址。
6. 您現在可以在「Microsoft 開發人員中心」中開始使用新的 Microsoft 帳戶。

> [AZURE.TIP] 使用 DL，可讓多人收到電子郵件通知非常重要的付款資訊的報告。 它也可以確保 Microsoft 帳戶的擁有權可以轉移且不受限於單一個人。

## 2.建立您的 Microsoft 開發人員中心帳戶
Microsoft 開發人員中心用於註冊一次公司資訊。 註冊者必須是公司的有效代表，而且必須提供個人資訊用來驗證其身分識別。 註冊的人員必須使用共用公司的 Microsoft 帳戶 **和 Azure 發佈入口網站中，必須使用相同的帳戶。**當您嘗試建立帳戶之前，應該先檢查公司是否確實未曾擁有 Microsoft 開發人員中心帳戶。 在這個程序期間，我們會收集公司地址資訊、銀行帳戶資訊和稅務資訊。 這些資訊通常可以從金融或商務連絡人處取得。

> [AZURE.IMPORTANT] 您必須完成下列賣方分析元件才能提供建立及部署的各個不同階段的進度。


| 賣方設定檔 | 開始草擬 | 預備 | 免費發佈與解決方案範本 | 商業發佈 |
|----|----|----|----|----|
|公司註冊 | 必備 | 必備 | 必備 | 必備 |
|稅務設定檔識別碼 | 選用 | 選用 | 選用 | 必備 |
|銀行帳戶 | 選用 | 選用 | 選用 | 必備 |


> [AZURE.NOTE] 將您自己授權 (BYOL) 僅支援虛擬機器，會視為 **免費** 供應項目。


### 註冊您的公司帳戶
1. 開啟新 Internet Explorer InPrivate 或 Chrome Incognito 瀏覽工作階段以確保您在未登入至個人帳戶。

2. 移至 [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)。

3. 使用您的公司註冊 Microsoft 帳戶 (例如 marketplace@example.com) 登入。

    ![繪圖][img-signin]

4. 請完成 [協助我們保護您的帳戶] 精靈，它會透過電話號碼或電子郵件地址驗證您的身分識別。

    ![繪圖][img-verify]

5. 在 [註冊帳戶資訊] 區段中，選取您 **帳戶國家/地區** 從下拉式功能表中。

    > [AZURE.WARNING] **「 銷售的 「 國家/地區:** 若要銷售您的服務在 Azure Marketplace 上，您已註冊的實體必須從已核准 」 銷售的 「 國家/地區上述的其中一個。 這項限制是基於付款和稅務理由。 我們正積極在不久的將來擴充這份國家/地區清單，所以請密切注意。 如需詳細資訊，請參閱  [服務商場參與原則](http://go.microsoft.com/fwlink/?LinkID=526833)。

6. 選取您 「 帳戶類型 」， **個別** 或 **公司**。

    <!-- Insert screenshot -->

    > [AZURE.IMPORTANT] 若要進一步了解帳戶類型，以及何者最適合您選擇，請檢視頁面 [帳戶類型、 位置和費用](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

7. 輸入 **發行者顯示名稱**, ，通常您公司的名稱。

    > [AZURE.TIP] 目前，Azure 發佈入口網站不使用發行者顯示名稱。 但是，必須填寫這項資訊以完成註冊程序。

8. 輸入帳戶的連絡資訊。

    > [AZURE.IMPORTANT] 您必須提供精確的連絡資訊，因為它將用於我們的驗證程序，為您的公司核准開發人員中心。

9. 公司帳戶，您還需要提供連絡資訊 **公司核准者**, ，可以確認您有權建立代表貴組織帳戶的人員。 當您完成時，按一下 [ **下一步** 移入 **「 付款區段 」**。

10. 輸入您的付款資訊以支付您的帳戶。 如果您有涵蓋註冊成本的促銷代碼，您可以在此處輸入。 否則，請提供您的信用卡資訊 (或受支援市場的 PayPal)。 當您完成時，按一下 [ **下一步** 移入 **「 檢閱螢幕 」**。

11. 檢閱您的帳戶資訊，並確認所有項目都正確。 然後，閱讀並接受條款與條件 **Microsoft Azure Marketplace 發行者合約**。 勾選方塊以表示您已閱讀並接受這些條款。

12. 按一下 [ **完成** 以確認您的註冊。 我們會傳送一則確認訊息到您的開發人員電子郵件地址。

13. 如果您計劃發行只可用的優惠，按一下 [ **移至 Azure Marketplace 發行網站** 您可以直接跳到本文件中，第 3 節 [發佈入口網站中註冊您的帳戶](#3-register-your-account-in-the-publishing-portal)。

14. 如果您計劃發行商業優惠，按一下 [ **更新您的帳戶資訊** 必須完成稅和銀行開發人員中心帳戶中的資訊。

> [AZURE.IMPORTANT] 您將無法適當地在預備環境中測試您的優惠，並不能推送您的優惠至生產環境，而不會完成的稅金和銀行帳戶資訊。

如果您想要更新您稅金和銀行資訊之後，您可以移至 [區段 3， [在發佈入口網站中註冊您的帳戶](#3-register-your-account-in-the-publishing-portal), ，以及會在使用中 Azure 發佈入口網站連結稍後再回來。

### 新增稅務和銀行資訊
 如果您想要發佈商業供應項目供採購，則還需要新增付款和稅務資訊，然後在開發人員中心中提交以供驗證。 如果您只要發佈免費供應項目 (或 BYOL 供應項目)，則不需要加入這項資訊。 您可以稍後再新增，但請花一些時間驗證稅務資訊。 如果您知道將會提供商業供應項目供採購，建議您儘快新增。

**銀行資訊**

1. 登入 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) 與您的 Microsoft 帳戶。

2. 按一下 [ **支付額帳戶** 在左窗格中，在 **選擇付款方式** 按一下 **銀行帳戶** 或 **PayPal**。

    > [AZURE.IMPORTANT] 如果您有商業市場的客戶購買的商品，這是第帳戶將會收到這些採購項支付額。

3. 輸入的付款資訊，然後按一下 [ **儲存** 您感到滿意。

    > [AZURE.IMPORTANT] 如果您需要更新或變更您的付款帳號，請遵循相同的步驟上方，以新資訊取代目前的資訊。 變更您的付款帳戶會延遲您的付款最多一次付款週期。 會發生此延遲是因為我們必須確認帳戶變更，就像我們在您第一次設定付款帳戶時所做的一樣。 您仍然會在帳戶確認之後收到全額款項。當期付款週期未付的款項會新增至下一期。

4. 按一下 [ **下一步**。

**稅務資訊**

1. 登入 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) 與您的 Microsoft 帳戶 (如果需要)。

2. 按一下 [ **稅設定檔** 左功能表中。

3. 在 **設定稅務表單** 頁面上選取的國家或地區，您必須永久常駐，，然後選取 [國家或地區，您用來保存主要的公民意識。 按一下 [ **下一步**。

4. 輸入您的稅金詳細資料，然後按一下 **下一步**。

> [AZURE.WARNING] 您無法發送至您的商業提供，而不會完成您的 Microsoft 開發人員中心帳戶中的稅額和銀行帳戶資訊的生產環境。

## 3.在發佈入口網站中註冊帳戶
Azure 發佈入口網站可用來發佈和管理您的優惠。 您可以在發佈入口網站中找到一些有用的資訊，以指引您完成優惠建立程序。

> [AZURE.WARNING] 必須在這裡使用相同的公司使用 Microsoft 開發人員中心註冊中的 Microsoft 帳戶。 建立主要發行者帳戶之後，可以加入其他使用者來提供協助。

1.  開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入個人帳戶。

2.  移至 [http://publish.windowsazure.com](http://publish.windowsazure.com)。

3.  使用您的公司註冊 Microsoft 帳戶 (亦即，marketplace@example.com) 登入，您可以視需要加入共同管理員。

  > [AZURE.TIP] 在描述參與原則 [Azure 網站](http://azure.microsoft.com/support/legal/marketplace/participation-policies/)。

  > 如果您有問題的開發人員中心註冊，請記錄的支援票證，如下所示:
  1. 請連絡 [支援](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=en-us&pesid=15635&ccsid=635847950577064286)。
  2. 選擇 **開發人員中心**。
  3. 選擇 **設定檔**。
  4. 選擇連絡方法。






## 後續步驟
現在已建立並註冊您的帳戶，請按一下您想要發佈至 Azure Marketplace 的構件類型 (虛擬機器、開發人員服務、資料服務或解決方案範本)。 請參閱下列其中一份文件，了解如何發佈您自己的供應項目：

|| 虛擬機器映像 | 開發人員服務 | 資料服務 | 解決方案範本 |
|----|-----|-----|-----|-----|
|**步驟 2：建立您的優惠** |[一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)|
|| [VM 映像技術性必要條件][link-single-vm-prereq] | 開發人員服務技術性必要條件 | [資料服務技術性必要條件](marketplace-publishing-data-service-creation-prerequisites.md)  | [方案範本技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [VM 映像發佈指南][link-single-vm] | 開發人員服務發佈指南 | [資料服務發佈指南](marketplace-publishing-data-service-creation.md) | [解決方案範本發佈指南](marketplace-publishing-solution-template-creation.md) |
|| [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] |

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md

