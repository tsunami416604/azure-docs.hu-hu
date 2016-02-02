


1. 透過登入 [Azure 預覽入口網站](http://portal.azure.com/), ，在 http://portal.azure.com/。

2. 在左邊的橫幅中按一下 [瀏覽全部]****。 即會顯示 [瀏覽]**** 刀鋒視窗。

3. 捲動並按一下 [SQL Server]****。 即會顯示 [SQL Server]**** 刀鋒視窗。

    ![在入口網站中尋找您的 Azure SQL Database 伺服器][b21-findserverinportal]

4. 為了方便起見，按一下前述 [瀏覽]**** 刀鋒視窗上的最小化控制項。

5. 在篩選文字方塊中開始輸入您的伺服器名稱。 即會顯示您的資料列。

6. 按一下伺服器的資料列。 即會顯示您伺服器的刀鋒視窗。

7. 在伺服器刀鋒視窗中，按一下 [設定]****。 即會顯示 [設定]**** 刀鋒視窗。

8. 按一下 [防火牆]****。 即會顯示 [防火牆設定]**** 刀鋒視窗。

    ![按一下 ][b31-settingsfirewallnavig]

9. 按一下 [加入用戶端 IP]****。 在第一個文字方塊中替新規則輸入名稱。

10. 輸入低和高 IP 位址值作為要啟用的範圍。
 - 將低值結尾設為 **.0**、高值結尾設為 **.255** 會比較方便。

    ![將一組 IP 位址範圍加入為允許][b41-addrange]

11. 按一下 [儲存]****。










[b21-findserverinportal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png 
[b31-settingsfirewallnavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png 
[b41-addrange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png 

