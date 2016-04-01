## 如何在 Azure 入口網站中使用網路組態檔建立 VNet

Azure 使用 xml 檔案定義訂用帳戶可用的所有 VNet。 您可以下載這個檔案並加以編輯以進行修改，或刪除現有的 VNet，建立新的虛擬網路。 本文件將說明如何下載這個檔案 (下稱網路組態，或 Netcfg 檔案)，以及如何編輯該檔案，以建立新的 VNet。 檢查 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx) 若要深入了解網路組態檔。

若要透過 Azure 入口網站，使用 Netcfg 檔案建立 VNet，請依照下列步驟執行。

1. 從瀏覽器，瀏覽至 http://manage.windowsazure.com，並有必要，請登入您的 Azure 帳戶。
2. 向下捲動服務清單，然後按一下 **網路** ，如下所示。

    ![Azure 虛擬網路](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. 在頁面底部，按一下 [ **匯出** ] 按鈕，如下所示。

    ![匯出按鈕](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. 在 **匯出網路組態** 頁面上，選取您想要匯出虛擬網路組態，來自訂用的帳戶，然後按一下 [在頁面的左下角的核取記號按鈕。
5. 遵循您的瀏覽器指示儲存 **NetworkConfig.xml** 檔案。 請務必記下儲存檔案的位置。
6. 開啟您在上面使用任何 XML 或文字編輯器應用程式的步驟 5 中所儲存的檔案，並尋找 **<VirtualNetworkSites>** 項目。 如果您有任何已建立的網路，每個網路將會變成自己 **<VirtualNetworkSite>** 項目。
7. 若要建立虛擬網路在此案例中所述，新增下列 XML 低於 **<VirtualNetworkSites>** 項目 ︰

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  儲存網路組態檔。
9.  在 Azure 入口網站中，在頁面的左下角按一下 **新增**, ，然後按一下 [ **網路服務**, ，然後按一下 [ **虛擬網路**, ，然後按一下 [ **匯入組態** 如下圖所示。

    ![匯入組態](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  在 **匯入網路組態檔** 頁面上，按一下 **瀏覽檔案 …**, ，然後瀏覽至您儲存檔案的上述的步驟 8 中的資料夾中，選取檔案，然後按一下 **開啟**。 網頁外觀將類似於下圖。 在頁面右下角，按一下箭頭按鈕，以移至下一個步驟。

    ![Import network configuration file page](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   在 **建置您的網路** 頁面上，針對新的 VNet，注意此項目，如下圖所示。

    ![Building your network page](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   按一下頁面右下角的核取記號按鈕建立 VNet。 幾秒鐘後，VNet 會顯示在可用 VNet 清單中，如下圖所示。

    ![New virtual network](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

