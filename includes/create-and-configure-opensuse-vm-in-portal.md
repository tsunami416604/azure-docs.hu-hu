<properties writer="kathydav" editor="tysonn" manager="timlt" />

1. 登入 [Azure 入口網站](http://manage.windowsazure.com)。 簽出 [免費試用版](http://azure.microsoft.com/pricing/free-trial/) 提供如果您沒有訂閱。

2. 在命令列視窗的底部，按一下 [ **新增**。

3. 在 **計算**, ，按一下 [ **虛擬機器**, ，然後按一下 [ **從組件庫**。

    ![建立新的虛擬機器][Image1]

4. 在 **SUSE** 群組選取 OpenSUSE 虛擬機器映像，然後按一下箭頭以繼續。

5. 在第一個 **虛擬機器組態** 頁面:

    - 型別 a **虛擬機器名稱**, ，如"testlinuxvm"。
    - 確認 **層** 挑選 **大小**。 階層可決定您可以選擇的大小。 大小會影響其使用成本以及組態選項 (例如您可連接的資料磁碟數目)。 如需詳細資訊，請參閱 [虛擬機器的大小](../articles/virtual-machines-size-specs.md)。
    - 型別 a **新的使用者名稱**, ，或接受預設值， **azureuser**。 這個名稱會新增至 Sudoers 清單檔案。
    - 決定哪一種類型 **驗證** 使用。 如需一般密碼指導方針，請參閱 [強式密碼](http://msdn.microsoft.com/library/ms161962.aspx)。

6. 在下一次 **虛擬機器組態** 頁面:

    - 使用預設 **建立新的雲端服務**。
    - 在 **DNS 名稱** 方塊中，輸入有效的 DNS 名稱，以作為位址，如"testlinuxvm"的一部分。
    - 在 **區域/同質群組/虛擬網路** 方塊中，選取代管這個虛擬映像的所在區域。
    - 在 **端點**, ，保留 SSH 端點。 您可以立即新增其他內容，或在建立虛擬機器之後新增、變更或刪除它們。

    >[AZURE.NOTE] 如果您想讓虛擬機器使用虛擬網路，您 **必須** 在建立虛擬機器時指定虛擬網路。 在建立虛擬機器之後，您就無法將虛擬機器加入至虛擬網路。 如需詳細資訊，請參閱 [虛擬網路概觀](virtual-networks-overview.md)。

7.  在最後一個 **虛擬機器組態** 頁面上，保留預設設定，然後按一下 [完成] 核取記號。

在入口網站會列出新的虛擬機器，在 **虛擬機器**。 雖然報告狀態為 **(佈建)**, ，設定虛擬機器。 報告狀態為 **執行**, ，您可以移至下一個步驟。

##連線至虛擬機器

視您將連結之電腦上的作業系統而定，您將使用 SSH 或 PuTTY 連線至虛擬機器：

- 請從執行 Linux 的電腦使用 SSH。 在命令提示字元中，輸入：

    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

    輸入使用者的密碼。

- 請從執行 Windows 的電腦使用 PuTTY。 如果您沒有安裝此軟體，下載從 [PuTTY 下載頁面][PuTTYDownload]。

    儲存 **putty.exe** 到您的電腦上的目錄。 開啟命令提示字元，巡覽至該資料夾，並執行 **putty.exe**。

    輸入主機名稱，例如"testlinuxvm.cloudapp.net"，並輸入"22" **連接埠**。

    ![PuTTY 畫面][Image6]  

##更新虛擬機器 (選用)

1. 連線至虛擬機器之後，您可以選擇性地安裝系統更新和修補程式。 若要執行更新，請輸入：

    `$ sudo zypper update`

2. 選取 **軟體**, ，然後 **線上更新** 來列出可用的更新。 選取 **接受** 即可開始安裝並套用所有新可用的修補檔 (選擇性者除外)。

3. 安裝完成之後，請選取 **完成**。  您的系統現在已是最新版本。

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

