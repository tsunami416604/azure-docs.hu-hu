下列步驟示範如何使用 SQL Server Management Studio (SSMS) 透過網際網路連線到 SQL Server 執行個體。 不過，相同的步驟適用於讓您在內部部署或 Azure 中執行的應用程式可存取 SQL Server 虛擬機器。

您必須先完成後續各小節描述的下列工作，才能從其他 VM 或網際網路連接 SQL Server 的執行個體：

- [為虛擬機器建立 TCP 端點](#create-a-tcp-endpoint-for-the-virtual-machine)
- [在 Windows 防火牆中開啟 TCP 連接埠](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [設定 SQL Server 以接聽 TCP 通訊協定](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [設定 SQL Server 以進行混合模式驗證](#configure-sql-server-for-mixed-mode-authentication)
- [建立 SQL Server 驗證登入](#create-sql-server-authentication-logins)
- [決定虛擬機器的 DNS 名稱](#determine-the-dns-name-of-the-virtual-machine)
- [從另一部電腦連接 Database Engine](#connect-to-the-database-engine-from-another-computer)

連線路徑如以下圖表總結：

![連接 SQL Server 虛擬機器](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### 為虛擬機器建立 TCP 端點

若要從網際網路存取 SQL Server，虛擬機器必須具有端點才能接聽傳入 TCP 通訊。 此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

>[AZURE.NOTE] 如果您要連接相同雲端服務或虛擬網路中，您就不需要建立可公開存取的端點。 在此情況下，您可以繼續進行下一個步驟。 如需詳細資訊，請參閱 [連線案例](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios)。

1. 在 Azure 管理入口網站中，按一下 **虛擬機器**。
    
2. 按一下新建立的虛擬機器。 畫面上即會顯示您虛擬機器的相關資訊。
    
3. 選取頁面頂端附近 **端點** 頁面，，然後在頁面底部，按一下 **新增**。
    
4. 在 **將端點加入至虛擬機器** 頁面上，按一下 **新增獨立端點**, ，然後按一下 [下一步箭頭以繼續。
    
5. 在 **指定端點的詳細資料** 頁面上，提供下列資訊。

    - 在 **名稱** 方塊中，提供端點的名稱。
    - 在 **通訊協定** 方塊中，選取 **TCP**。 您可以輸入 **57500** 中 **公用連接埠** 方塊。 同樣地，您可以輸入 SQL Server 的預設接聽連接埠 **1433年** 中 **私用連接埠** 方塊。 請注意，有許多組織會藉由選取不同連接埠號碼來避免惡意的安全性攻擊。 

6. 按一下打勾記號繼續。 系統隨即會建立端點。

### 在 Windows 防火牆中為 Database Engine 的預設執行個體開啟 TCP 連接埠

1. 透過 Windows 遠端桌面連接虛擬機器。 在 [開始] 畫面中，登入後，輸入 **WF.msc**, ，然後按 enter 鍵。 

    ![啟動防火牆程式](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. 在 **具有進階安全性的 Windows 防火牆**, ，在左窗格中，以滑鼠右鍵按一下 **輸入規則**, ，然後按一下 [ **新規則** 動作] 窗格中。

    ![新增規則](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. 在 **新增輸入規則精靈** 對話方塊的 [ **規則類型**, ，請選取 **連接埠**, ，然後按一下 [ **下一步**。

4. 在 **通訊協定和連接埠** ] 對話方塊中，使用預設 **TCP**。 在 **特定本機連接埠** 方塊，然後輸入 Database Engine 執行個體的連接埠號碼 (**1433年** 預設執行個體也可以選擇在端點步驟中的私人連接埠)。 

    ![TCP 連接埠 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. 按一下 [ **下一步**。

6. 在 **動作** 對話方塊中，選取 **允許連線**, ，然後按一下 [ **下一步**。

    **安全性注意事項 ︰** 選取 **允許安全連線** 可以提供額外的安全性。 如果您想要在環境中設定額外的安全性選項，請選取此選項。

    ![允許連線](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. 在 **設定檔** 對話方塊中，選取 **公用**, ，**私人**, ，和 **網域**。 然後按一下 [ **下一步**。 

    **安全性注意事項 ︰**  選取 **公用** 可允許透過網際網路存取。 請盡可能選取較具有限制性的設定檔。

    ![公用設定檔](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. 在 **名稱** 對話方塊中，輸入名稱和描述這項規則，然後按一下 **完成**。

    ![規則名稱](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

視需要為其他元件開啟額外的連接埠。 如需詳細資訊，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](http://msdn.microsoft.com/library/cc646023.aspx)。


### 設定 SQL Server 以接聽 TCP 通訊協定

1. 當連接到虛擬機器，在開始頁面上，鍵入 **SQL Server 組態管理員** 然後按 enter 鍵。
    
    ![開啟 SSCM](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. 在 「 SQL Server 組態管理員 」 中，在主控台窗格中，展開 **SQL Server 網路組態**。

3. 在主控台窗格中，按一下 [ **MSSQLSERVER 的通訊協定** （預設的執行個體名稱）。在詳細資料窗格中以滑鼠右鍵按一下 [TCP]。依預設，組件庫映像的 TCP 應該是 [已啟用]。 對於自訂映像，按一下 [ **啟用** （如果其狀態停用）。

    ![啟用 TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. 在主控台窗格中，按一下 [ **SQL Server 服務**。 在詳細資料窗格中，以滑鼠右鍵按一下 **SQL Server (_執行個體名稱_)** (預設執行個體是 **SQL Server (MSSQLSERVER)**)，然後按一下 [ **重新啟動**, ，以停止並重新啟動 SQL Server 執行個體。 

    ![重新啟動 Database Engine](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. 關閉 SQL Server 組態管理員。

如需啟用通訊協定的 SQL Server 資料庫引擎的詳細資訊，請參閱 [啟用或停用伺服器網路通訊協定](http://msdn.microsoft.com/library/ms191294.aspx)。

### 設定 SQL Server 以進行混合模式驗證

SQL Server Database Engine 須有網域環境才能使用 Windows 驗證。 若要從另一部電腦連接 Database Engine，請設定 SQL Server 以進行混合模式驗證。 混合模式驗證可允許 SQL Server 驗證和 Windows 驗證。

>[AZURE.NOTE] 設定混合的模式驗證可能不需要您設定 Azure 虛擬網路與設定的網域環境。

1. 當連接到虛擬機器，在開始頁面上，鍵入 **SQL Server 2014 Management Studio** 和按一下選取的圖示。

    ![啟動 SSMS](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

    當您首次開啟 Management Studio 時，它必須建立使用者 Management Studio 環境。 這可能需要花費幾分鐘的時間。

2. Management Studio 顯示 **連接到伺服器** 對話方塊。 在 **伺服器名稱** 方塊中，輸入要連接到資料庫引擎與 [物件總管] 中的虛擬機器的名稱。 (而不是虛擬機器名稱您也可以使用 **(local)** 或將一個句點當做 **伺服器名稱**。 選取 **Windows 驗證**, ，並保留 **_your_VM_name_\your_local_administrator** 中 **使用者名稱** 方塊。 按一下 [ **連接**。

    ![連線到伺服器](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. 在 SQL Server Management Studio 物件總管中，SQL Server （虛擬機器名稱） 的執行個體的名稱上按一下滑鼠右鍵，然後按一下 [ **屬性**。

    ![伺服器屬性](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. 在 **安全性** ] 頁面的 [ **伺服器驗證**, ，請選取 **SQL Server 和 Windows 驗證模式**, ，然後按一下 [ **確定**。

    ![選取驗證模式](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. 在 [SQL Server Management Studio] 對話方塊中，按一下 [ **確定** 以確認重新啟動 SQL Server 的需求。

6. 在 [物件總管] 中，以滑鼠右鍵按一下您的伺服器，以及 [ **重新啟動**。 (如果 SQL Server Agent 處於執行狀態，您也必須將其重新啟動。)

    ![重新啟動](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. 在 [SQL Server Management Studio] 對話方塊中，按一下 [ **是** 同意您想要重新啟動 SQL Server。

### 建立 SQL Server 驗證登入

若要從另一部電腦連接 Database Engine，您至少必須建立一個 SQL Server 驗證登入。

1. 在 SQL Server Management Studio 物件總管中，展開要建立新登入之伺服器執行個體的資料夾。

2. 以滑鼠右鍵按一下 **安全性** 資料夾，指向 **新增**, ，然後選取 **登入...**。

    ![新增登入](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. 在 **登入-新增** 對話方塊的 [ **一般** 頁面上，輸入新使用者的名稱 **登入名稱** 方塊。

4. 選取 **SQL Server 驗證**。

5. 在 **密碼** 方塊中，輸入新使用者的密碼。 輸入該密碼一次 **確認密碼** 方塊。

6. 若要強制執行複雜性和強制性密碼原則選項，請選取 **強制執行密碼原則** （建議選項）。 此為選取 SQL Server 驗證時的預設選項。

7. 若要強制執行逾期密碼原則選項，請選取 **強制執行密碼逾期** （建議選項）。 您必須選取強制執行密碼原則才能啟用此核取方塊。 此為選取 SQL Server 驗證時的預設選項。

8. 若要強制使用者在首次登入後建立新的密碼，請選取 **使用者必須變更密碼，在下次登入時** （建議使用此登入是否為其他人使用。 如果此登入是供您自己使用，請勿選取此選項。)您必須選取強制執行密碼逾期才能啟用此核取方塊。 此為選取 SQL Server 驗證時的預設選項。 

9. 從 **預設資料庫** 清單中，選取登入的預設資料庫。 **主要** 是此選項的預設值。 如果您尚未建立使用者資料庫，保留設為 **主要**。

10. 在 **預設語言** 清單中，保留 **預設** 做為值。
    
    ![登入屬性](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. 如果您正在建立第一個登入，也許會想要將此登入指定為 SQL Server 系統管理員。 如果是的話，在 **伺服器角色** ] 頁面上，核取 **sysadmin**。 

    **安全性注意事項 ︰** sysadmin 固定的伺服器角色的成員擁有 Database Engine 的完整控制權。 因此請謹慎限制此角色的成員資格。

    ![系統管理員 (sysadmin)](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. 按一下 [確定]。

如需 SQL Server 登入的詳細資訊，請參閱 [建立登入](http://msdn.microsoft.com/library/aa337562.aspx)。

### 決定虛擬機器的 DNS 名稱

若要從另一部電腦連接 SQL Server Database Engine，您必須知道虛擬機器的網域名稱系統 (DNS) 名稱。 (這是網際網路用來識別虛擬機器的名稱。 您可以使用 IP 位址，不過當 Azure 因備援或維護而移動資源時，IP 位址可能會改變。 DNS 名稱是穩定的，因為它可以重新導向新的 IP 位址。)  

1. 在 Azure 管理入口網站 （或上一個步驟），請選取 **虛擬機器**。 

2. 在 **虛擬機器執行個體** ] 頁面的 [ **快速概覽** 資料行中，尋找並複製 DNS 名稱的虛擬機器。

    ![DNS 名稱](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
    

### 從另一部電腦連接 Database Engine
 
1. 在連接網際網路的電腦上開啟 SQL Server Management Studio。
2. 在 **連接到伺服器** 或 **連接到 Database Engine** 對話方塊中，於**伺服器名稱** 方塊中，輸入 DNS 名稱 （於上一個工作中決定） 的虛擬機器及公用端點連接埠號碼，格式為 *DNSName，portnumber* 例如 **tutorialtestVM.cloudapp.net,57500**。
若要取得連接埠號碼，請登入 Azure 管理入口網站並尋找虛擬機器。 在儀表板，按一下 [ **端點** ，並使用 **公用連接埠** 指派給 **MSSQL**。
    ![公用連接埠](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. 在 **驗證** 方塊中，選取 **SQL Server 驗證**。
5. 在 **登入** 方塊中，輸入您在先前工作中建立登入名稱。
6. 在 **密碼** 方塊中，輸入您在先前工作中建立的登入的密碼。
7. 按一下 [ **連接**。

    ![使用 SSMS 進行連線](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)


