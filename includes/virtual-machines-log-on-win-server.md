<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. 按一下 **連接** 會建立並下載遠端桌面通訊協定 （.rdp 檔） 檔案。 按一下 [ **開啟** 使用此檔案。

5. 在 [遠端桌面] 視窗中，按一下 [ **連接** 以繼續。

    ![繼續連接](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 在 [Windows 安全性] 視窗中，輸入帳戶的認證在虛擬機器上，然後按一下 **確定**。

    在大多數案例中，這是您在建立虛擬機器時所指定的本機帳戶使用者名稱和密碼。 在此情況下，網域是虛擬機器的名稱，並輸入為 *vmname*& #92;*使用者名稱*。  
    
    如果虛擬機器屬於您公司的網域，請確定使用者名稱包含該網域的名稱格式 *網域*& #92;*使用者名稱*。 此帳戶也必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。
    
    如果虛擬機器是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

7.  按一下 [ **是** 驗證虛擬機器的身分識別，並完成登入。

    ![驗證機器的身分識別](./media/virtual-machines-log-on-win-server/connectverify.png)


