<properties
   pageTitle="開始使用 SQL Database 動態資料遮罩 (Azure 傳統入口網站)"
   description="如何開始在 Azure 傳統入口網站中使用 SQL 資料庫動態資料遮罩"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# 開始使用 SQL Database 動態資料遮罩 (Azure 傳統入口網站)

> [AZURE.SELECTOR]
- [動態資料遮罩-Azure 入口網站](sql-database-dynamic-data-masking-get-started.md)

## 概觀

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。 Azure SQL Database V12 版可支援動態資料遮罩。

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。 它是以原則為基礎的安全性功能，可針對指定的資料庫欄位隱藏查詢結果集中的機密資料，而不變更資料庫中的資料。

例如，客服中心服務代表可透過數個社會安全號碼或信用卡號碼的數字來識別來電者，但這些資料項目不應完全公開給服務代表。 可以定義遮罩規則，以針對任何查詢的結果集中任何社會安全號碼或信用卡號碼的末四碼以外的所有數字進行遮罩處理。 在另一個範例中，可以定義適當的資料遮罩來保護個人識別資訊 (PII) 資料，以便開發人員在生產環境中進行疑難排解用途的查詢，且不會違反法務規定。

## SQL Database 動態資料遮罩的基本概念

您在 Azure 傳統入口網站中的資料庫 [稽核與安全性] 索引標籤下，設定動態資料遮罩原則。


> [AZURE.NOTE] 若要設定在 Azure 入口網站中的動態資料遮罩，請參閱 [開始使用 SQL Database 動態資料遮罩 (Azure 入口網站)](sql-database-dynamic-data-masking-get-started.md)。


### 動態資料遮罩權限

動態資料遮罩可由　Azure 資料庫管理員、伺服器管理員或安全性主管人員等角色來設定。

### 動態資料遮罩原則

* **已從遮罩排除 SQL 使用者** -一組 SQL 使用者，或在 SQL 中可取得非遮罩的資料的 AAD 身分識別查詢的結果。 請注意，具有系統管理員權限的使用者永遠會從遮罩處理中排除，而且將會看到沒有任何遮罩的原始資料。

* **遮罩規則** -一組規則，定義會遮罩處理的指定的欄位和所將使用的遮罩函數。 指定的欄位可使用資料庫結構描述名稱、資料表名稱和資料行名稱來定義。

* **遮罩函數** -一組方法，可控制不同案例的資料的風險。

| 遮罩函數 | 遮罩邏輯 |
|----------|---------------|
| **預設值**  |**完整遮罩根據的資料類型的指定欄位**<br/><br/>• 使用 XXXX 或更少 x，如果欄位的大小小於 4 個字元的字串資料類型 (nchar、 ntext、 nvarchar)。<br/>• 針對數值資料類型 (bigint、 位元、 decimal、 int、 money、 數值、 smallint、 smallmoney、 tinyint、 float、 真實) 使用零值。<br/>• 針對日期/時間資料類型 (date、 datetime2、 datetime、 datetimeoffset、 smalldatetime、 時間) 使用 01-01-1900。<br/>• 對於 SQL 變數，目前型別的預設值使用。<br/>• 對於 XML 文件 <masked/> 用。<br/>• 使用空值的特殊資料型別 (時間戳記資料表、 hierarchyid、 GUID、 二進位、 影像、 varbinary spatial 類型)。
| **信用卡** |**遮罩方法會公開指定欄位的末四碼** 並新增常數字串做為信用卡格式的前置詞卡<br/><br/>XXXX XXXX XXXX-1234|
| **社會安全號碼** |**遮罩方法會公開指定欄位的末四碼** 並新增常數字串做為美國社會安全碼格式的前置詞。<br/><br/>XXX-XX-1234 |
| **電子郵件** | **遮罩方法會公開 (expose) 的第一個字母並以 XXX.com 取代網域** 使用電子郵件地址格式的常數字串前置詞。<br/><br/>aXX@XXXX.com |
| **隨機數字** | **遮罩方法會產生一個隨機數字** 根據選取的界限與實際資料類型。 如果指定的邊界相等，則遮罩函數將是常數。<br/><br/>![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **自訂文字** | **遮罩方法會公開第一個和最後一個字元** ，並在中間新增自訂填補字串。 如果原始字串小於公開的前置詞和後置詞，將使用填補字串。<br/>前置詞 [填補] 尾碼<br/><br/>![瀏覽窗格](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## 使用 Azure 傳統入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure 傳統入口網站， [https://manage.windowsazure.com](https://manage.windowsazure.com)。

2. 按一下您想要加上遮罩，然後按一下 [的資料庫 **稽核與安全性** ] 索引標籤。

3. 在 **動態資料遮罩**, ，按一下 [ **啟用** 以啟用動態資料遮罩功能。  

4. 輸入應從遮罩處理中排除，並可存取未經遮罩處理之敏感性資料的 SQL 使用者或 AAD 身分識別。 這應該是以分號分隔的使用者清單。 請注意，具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    >[AZURE.TIP] 若要讓應用程式層級可以顯示為具有特殊權限的應用程式使用者的敏感性資料，將 SQL 使用者加入或 AAD 識別應用程式用來查詢資料庫。 強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. 在功能表列中的頁面底端，按一下 [ **加入遮罩** 以開啟遮罩規則組態視窗。

6. 選取 **結構描述**, ，**資料表** 和 **欄** 從下拉式清單中，以定義會遮罩處理的指定的欄位。

7. 選擇 **遮罩函數** 從機密資料遮罩類別清單。

    ![導覽窗格](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. 按一下 [ **確定** 中的資料遮罩規則視窗，以更新動態資料遮罩原則中的遮罩規則集。

9. 按一下 [ **儲存** 來儲存新的或更新的遮罩原則。


## 使用 PowerShell Cmdlet 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。

## 使用 REST API 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database 的作業](https://msdn.microsoft.com/library/dn505719.aspx)。

