<properties
    pageTitle="使用 Azure 傳統入口網站建立 Oracle 資料庫 VM |Microsoft Azure"
    description="了解如何使用傳統部署模型和 Azure 入口網站建立內含 Oracle 資料庫的虛擬機器"
    services="virtual-machines"
    authors="bbenz"
    documentationCenter=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="Windows"
    ms.workload="infrastructure-services"
    ms.date="06/22/2015"
    ms.author="bbenz" />

#在 Azure 中建立 Oracle 資料庫虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


以下範例說明如何在 Azure 中，根據 Microsoft 提供且在 Windows Server 2012 上執行的 Oracle 資料庫映像來建立虛擬機器 (VM)。 有兩個步驟。 首先，建立 VM，然後在 VM 內建立 Oracle 資料庫。 顯示的範例是 Oracle 資料庫版本 12c，但步驟幾乎與版本 11g 相同。

##在 Azure 中建立 Oracle 資料庫 VM

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [ **Marketplace**, ，按一下 [ **計算**, ，然後輸入 **Oracle** 在搜尋方塊。

3.  選取其中一個可用的 Oracle 資料庫映像包括 **版本 11g、 版本 12c、 標準版、 企業版或其中一個常用的選項或進階的選項套件組合。**  檢閱您選取 （例如建議的最小值），映像的相關資訊，然後按一下 **下一步**。

4.  指定 **主機名稱** vm。

5.  指定 **使用者名稱** vm。 請注意，此使用者是用於從遠端登入 VM，這不是 Oracle 資料庫使用者名稱。

6.  指定並確認 VM 的密碼，或提供 SSH 公用金鑰。

7.  選擇 **定價層**。  請注意，顯示建議的定價層，以根據預設，若要查看所有的組態選項，請按一下 **檢視所有** 右上方。

8.  考量下列因素，並依照需要設定選用設定：

    a. 保留 **儲存體帳戶** 以-為使用 VM 名稱建立新的儲存體帳戶。

    b. 保留 **可用性設定組** 做為 「 未設定 」。

    c. 請勿新增任何 **端點** 這一次。

9.  選擇或建立資源群組。

10. 選擇 **訂閱**。

11. 選擇 **位置**。

12. 按一下 [ **建立**, ，便會開始建立 VM 的程序。 VM 的狀態後 **執行**, ，繼續進行下一個程序。


##在 Azure 中使用 Oracle 資料庫 VM 建立您的資料庫

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [ **虛擬機器**。

3.  按一下要登入的 VM 名稱。

4.  按一下 [ **連接**。

5.  視需要回應提示以連接 VM。 當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立 VM 時提供的值。

6.  建立名為環境變數 **ORACLE_HOSTNAME** 其值設為 VM 的電腦名稱。 您可以使用下列步驟建立環境變數：

    a.  在 Windows 中，按一下 [ **啟動**, ，型別 **控制台**, ，按一下 [ **控制台** 圖示，按一下 [ **系統及安全性**, ，按一下 **系統**, ，然後按一下 [ **進階系統設定**。

    b.  按一下 [ **進階** 索引標籤，然後再按一下 **環境變數**。

    c.  在 **系統變數** 區段中，按一下 **新增** 建立變數。

    d.  在 **新增系統變數** ] 對話方塊中，輸入 **ORACLE_HOSTNAME** 變數的名稱，然後輸入 VM 的電腦名稱做為值。 若要判斷電腦名稱，開啟命令提示字元並執行 **SET COMPUTERNAME** （該命令的輸出將包含電腦名稱）。

    e.  按一下 [ **確定** 來儲存新的環境變數，然後關閉 **新增系統變數** 對話方塊。

    f.  關閉由控制台開啟的其他對話方塊。

7.  在 Windows 中，按一下 [ **啟動**, ，然後輸入 **資料庫組態輔助**。 按一下 [ **資料庫組態輔助** 圖示。

8.  內 **資料庫組態輔助** 精靈] 中，視需要針對每個對話方塊步驟提供值 ︰

    a.  **步驟 1:** 按一下 **Create Database** 然後按一下 [ **下一步**。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

    b. **步驟 2:** ，請輸入 **全域資料庫名稱**。 輸入並確認值 **系統管理密碼**。 此密碼是 Oracle 資料庫的 **系統** 使用者。 清除 **做為容器資料庫建立**。 按一下 [ **下一步**。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

    c. **步驟 3:** 先決條件檢查應該自動繼續進行，逐步引導到 **步驟 4**。

    d. **步驟 4:** 檢閱 **建立資料庫-摘要** 選項，然後再按一下 **完成**。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
    e. **步驟 5:**  **進度頁面** 將會報告資料庫建立的狀態。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
    f. 您的資料庫建立之後，您必須選擇使用 **密碼管理** 對話方塊。 修改密碼設定，如果您的需求，並關閉對話方塊以結束 **資料庫組態輔助** 精靈。

##確認資料庫已安裝

1.  仍然登入您的 VM，啟動 SQL Plus 命令提示字元。 在 Windows 中，按一下 [ *啟動**，然後輸入 **SQL Plus**。 按一下 [ **SQL Plus** 圖示。

2.  出現提示時，使用登入的使用者名稱 **系統** 和您指定當您建立 Oracle 資料庫的密碼。

3.  在 SQL Plus 命令提示字元中，執行下列命令。

        **select \* from GLOBAL\_NAME;**

    結果應該是您建立之資料庫的全域名稱。

    ![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##讓您能夠從遠端連接資料庫
若要讓您能夠從遠端連接資料庫 (例如，從執行 Java 程式碼的用戶端電腦)，將需要啟動資料庫接聽程式、在虛擬機器防火牆中開啟連接埠 1521，然後建立連接埠 1521 的公用端點。

### 啟動資料庫接聽程式
1.  登入您的 VM。

2.  開啟命令提示字元。

3.  在命令提示字元中，執行下列命令。

        **lsnrctl start**

> [AZURE.NOTE] 您可以執行 **lsnrctl 狀態** 檢查接聽程式的狀態。 當您想要停止接聽程式時，您可以執行 **lsnrctl 停止**。

### 在虛擬機器防火牆中開啟連接埠 1521

1.  在仍登入您的虛擬機器，在 Windows 中，按一下 [ **啟動**, ，型別 **具有進階安全性的 Windows 防火牆**, ，然後按一下 [ **具有進階安全性的 Windows 防火牆** 圖示。 這會開啟 **具有進階安全性的 Windows 防火牆** 管理主控台。

2.  在防火牆管理主控台中，按一下 [ **輸入規則** 的左窗格中 (如果您沒有看到 **輸入規則**, ，依序展開左窗格中的最上層節點)，然後按一下 [ **新規則** 在右窗格中。

3.  如 **規則類型**, ，請選取 **連接埠** 然後按一下 [ **下一步**。

4.  如 **通訊協定和連接埠**, ，請選取 **TCP**, ，請選取 **特定本機連接埠**, ，輸入 **1521年** 做為連接埠，然後按一下 [ **下一步**。

5.  選取 **允許連線** 按一下 **下一步**。

6.  接受套用規則的設定檔的預設值，然後按一下 **下一步**。

7.  指定規則，並選擇性地描述的名稱，然後按一下 **完成**。

### 建立連接埠 1521 的公用端點

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [ **瀏覽**。

3.  按一下 [ **虛擬機器**。

4.  選取虛擬機器。

5.  按一下 [ **設定**。

6.  按一下 [ **端點**。

7.  按一下 [ **新增**。

8.  指定端點的名稱：

    a. 使用 **TCP** 通訊協定。

    b. 使用 **1521年** 公用連接埠。

    c. 使用 **1521年** 的私人連接埠。

9.  維持其餘選項不變。

10. 按一下 [ **確定**。

##啟用 Oracle Database Enterprise Manager 遠端存取
如果您想要啟用遠端存取 Oracle Database Enterprise Manager 的功能，可在防火牆中開啟連接埠 5500，並在 Azure 傳統入口網站中針對 5500 建立虛擬機器端點 (使用稍早開啟連接埠 1521 以及針對 1521 建立端點的步驟)。 接著，若要從遠端電腦執行 Oracle Enterprise Manager，可在瀏覽器開啟格式為 `http://<<unique_domain_name>>:5500/em` 的 URL。

> [AZURE.NOTE] 您可以判斷的值 *\ < \ < unique\_domain\_name\ > \ >* 內 [Azure 傳統入口網站](https://ms.portal.azure.com/) 按一下 **虛擬機器** ，然後選取用來執行 Oracle 資料庫虛擬機器)。

##設定常用選項和進階選項套件組合
如果您選擇 **Oracle Database 包含常用選項** 或 **Oracle Database 包含進階選項套件組合**, 下, 一個步驟是在您的 Oracle 安裝中設定附加元件功能。 由於設定會根據您對於每個個別元件的需求而截然不同，因此，請參閱 Oracle 文件，以取得在 Windows 上設定這些功能的相關指示。

 **Oracle Database 包含常用選項套件組合** 包含 Oracle Database Enterprise Edition 和授權包含執行個體的 [資料分割](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), ，[Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), ，[Oracle 資料庫的微調組件](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), ，[資料庫的 Oracle 診斷套件](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ), ，和 [Oracle 資料庫生命週期管理組件](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html)。

 **Oracle Database 包含進階選項套件組合** 授權包含的所有選項的執行個體包含常用選項套件組合中加上 [進階壓縮](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), ，[進階安全性](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), ，[標籤安全性](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), ，[資料庫保存庫](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), ，[進階分析](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), ，[OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), ，[空間和圖形](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), ，[記憶體中資料庫快取](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), ，[資料遮罩的組件](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB), ，與 Oracle 測試資料管理組件 （做為資料遮罩組件的一部分）。

##其他資源
現在您已經設定虛擬機器並建立了資料庫，請查看下列主題以了解其他資訊。

-   [Oracle 虛擬機器映像 - 其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-   [Oracle Database 12c 文件庫](http://www.oracle.com/pls/db1211/homepage)

-   [從 Java 應用程式連線到 Oracle 資料庫 (英文)](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-   [Azure 的 Oracle 虛擬機器影像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-   [Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)


