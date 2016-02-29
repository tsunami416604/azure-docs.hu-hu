
<!--
includes/sql-database-include-ip-address-22-v12portal.md

最新的有效性檢查: 2015年-09-04，GeneMi。

做為的 2015年-09-04 約下列主題可能包含包括:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## 伺服器層級防火牆規則

### 透過新 Azure 入口網站管理伺服器層級防火牆規則
-->


1. 透過登入 [Azure 預覽入口網站](http://portal.azure.com/), ，在 http://portal.azure.com/。

2. 在左橫幅中，按一下 [ **全部瀏覽**。  **瀏覽** 刀鋒視窗隨即出現。

3. 捲動並按一下 [ **SQL 伺服器**。  **SQL 伺服器** 刀鋒視窗隨即出現。 

    ![在入口網站中尋找您的 Azure SQL Database 伺服器][b21-FindServerInPortal]

4. 為了方便起見，按一下最小化控制項上先前 **瀏覽** 刀鋒視窗。

5. 在篩選文字方塊中開始輸入您的伺服器名稱。 即會顯示您的資料列。

6. 按一下伺服器的資料列。 即會顯示您伺服器的刀鋒視窗。

7. 在您的伺服器] 分頁中，按一下 [ **設定**。  **設定** 刀鋒視窗隨即出現。

8. 按一下 [ **防火牆**。  **防火牆設定** 刀鋒視窗隨即出現。 

    ![按一下 [設定] > [防火牆]。][b31-SettingsFirewallNavig]

9. 按一下 [ **新增用戶端 IP**。 在第一個文字方塊中替新規則輸入名稱。

10. 輸入低和高 IP 位址值作為要啟用的範圍。
 - 可以是很有較低的值結束 **.0** 」 和 「 具有高 **.255**。 

    ![將一組 IP 位址範圍加入為允許][b41-AddRange]

11. 按一下 [ **儲存**。



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

