<properties
   pageTitle="開始使用 SQL Database 威脅偵測"
   description="如何開始在 Azure 入口網站中使用 SQL Database 威脅偵測"
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
   ms.date="10/29/2015"
   ms.author="ronmat; ronitr"/>


# 開始使用 SQL Database 威脅偵測

## 概觀

威脅偵測會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。  威脅偵測處於預覽階段，Azure SQL Database V12 版本支援此功能。

威脅偵測提供新的一層安全性，在發生異常活動時會提供安全性警示，讓客戶偵測並回應潛在威脅。  使用者可以瀏覽使用可疑事件 [Azure SQL Database 稽核](sql-database-auditing-get-started.md) 來判斷是否造成從嘗試存取、 破壞或利用資料庫中的資料。
您不必是安全性專家，也不需要管理進階的安全性監視系統，威脅偵測讓您輕鬆解決資料庫的潛在威脅。

威脅偵測會偵測異常資料庫活動，指出潛在的 SQL 插入式攻擊。 SQL 插入式攻擊是網際網路上常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，以破壞或修改資料庫中的資料。


## 設定資料庫的威脅偵測

1. 啟動 Azure 入口網站， [https://portal.azure.com](https://portal.azure.com)。

2. 瀏覽至您要監視的 SQL Database 的組態刀鋒視窗。 在 [設定] 分頁中，選取 **稽核與威脅偵測**。

    ![導覽窗格][1]

3. 在 **稽核與威脅偵測** 組態刀鋒視窗中開啟 **ON** 稽核，這將顯示的威脅偵測設定。

    ![導覽窗格][2]

4. 開啟 **ON** 威脅偵測。

5. 設定在偵測到異常資料庫活動時將收到安全性警示的電子郵件清單。

6. 按一下 [ **儲存** 中 **稽核與威脅的偵測** 組態] 分頁，以儲存新的或更新的稽核和威脅偵測原則。

    ![導覽窗格][3]


## 偵測到可疑事件時探索異常資料庫活動

1. 偵測到異常資料庫活動時，您將收到電子郵件通知。 <br/>
電子郵件將提供包括性質的異常活動、 資料庫名稱、 伺服器名稱和事件時間的可疑的安全性事件的相關資訊。 此外，它還會提供可能的原因，並建議您調查並減輕資料庫的潛在威脅的動作。<br/>

    ![導覽窗格][4]

2. 在電子郵件中，按一下 **Azure SQL 稽核記錄檔** 連結，它便會啟動 Azure 入口網站，並顯示於可疑事件相關的稽核記錄。

    ![導覽窗格][5]

3. 按一下稽核記錄以檢視可疑資料庫活動的詳細資訊，例如 SQL 陳述式、失敗原因和用戶端的 IP。

    ![導覽窗格][6]

4. 在 [稽核記錄] 刀鋒視窗中，按一下 [  **在 Excel 中開啟** 開啟預先設定的 excel 匯入和執行深入分析稽核記錄的可疑事件的時間前後的範本。<br/>
**注意 ︰** 在 Excel 2010 或更新版本，電源查詢和 **快速合併** 是必要設定

    ![導覽窗格][7]

5. 若要設定 **快速合併** 設定-在 **POWER QUERY** 功能區索引標籤上，選取 **選項** 以顯示 [選項] 對話方塊。 選取 [隱私權] 區段，選擇第二個選項 - [忽略隱私權等級並可能改善效能]：

    ![導覽窗格][8]

6. 若要載入 SQL 稽核記錄檔，請確定 [設定] 索引標籤中的參數已正確設定，然後選取 [資料] 功能區，並按一下 [全部重新整理] 按鈕。

    ![導覽窗格][9]

7. 結果會出現在 **SQL 稽核記錄檔** 表可讓您執行已偵測到的異常活動的更深入的分析和減輕您的應用程式的安全性事件的影響。


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


