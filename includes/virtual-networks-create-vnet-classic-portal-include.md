## 如何在 Azure 入口網站中建立 VNet

若要根據上述案例建立 VNet，請依照下列步驟執行。

1. 從瀏覽器，瀏覽至 http://manage.windowsazure.com，並有必要，請登入您的 Azure 帳戶。
2. 按一下 [ **新增** > **網路服務** > **虛擬網路** > **自訂建立** 如下圖所示。

    ![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. 在 **虛擬網路詳細資料** 頁面上，輸入 **名稱** 的 vnet 中，選取其 **位置**, ，然後按一下 [前往步驟 2 頁的右下角的箭頭。 下圖顯示本文案例的設定。

    ![虛擬網路詳細資料頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. 在 **DNS 伺服器和 VPN 連線能力** 頁面上，指定的名稱和最多 9 部 DNS 伺服器使用的 IP 位址。 如果您不指定 DNS 伺服器，VNet 會使用 Azure 提供的內部命名解決方案。 在我們的案例中不會設定 DNS 伺服器。
5. 如果您想要提供給 VNet 的點對站 VPN 存取，啟用 **設定點對站 VPN** 核取方塊。 如果您並未設定點對站 VPN，可在建立點對站 VPN 後隨時新增至 VNet。 在我們的案例中不會設定點對站 VPN。
6. 如果您想要提供您的 VNet 與另一個 VNet 或內部網路之間的站台對站 VPN 連線能力，啟用 **設定站台對站 VPN** 核取方塊，並指定想要使用 **ExpressRoute** 或附註，並連接到網路的名稱。 如果您並未設定站對站 VPN，建立之後隨時可以將站對站 VPN 新增至 VNet。 在我們的案例中不會設定站對站 VPN。
7. 按一下頁面右下角的箭頭，繼續進行步驟 3。下圖顯示我們案例的設定。

    ![DNS 伺服器和 VPN 連線能力頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. 在 **虛擬網路位址空間** ] 頁面的 [ **起始 IP**, ，按一下 [ *10.0.0.0* 來切換 VNet 位址空間，然後輸入您想要使用的起始位址空間。 我們的案例中，輸入 *192.168.0.0*。 
9. 在 **CIDR (位址計數)** 選取的子網路遮罩的位元數。 我們的案例中，選取 *16 (65536)*。
10. 在 **子網路**, ，按一下 [ *子網路 1* 並視需要重新命名子網路。 我們的案例中，重新命名為 *前端*。

    >[AZURE.NOTE] 如果您按一下 [名稱] 文字方塊中的子網路外您將無法編輯名稱，如果一次的子網路。 若要修正此問題，您需要按一下子網路右邊的 X 按鈕移除子網路，然後如以下步驟 13 所述新增子網路。

11. 在 **起始 IP** 對於第一個子網路中，指定 [起始 IP 位址子網路。 我們的案例中，輸入 *192.168.1.0*。
12. 在 **CIDR (位址計數)** 選取第一個子網路的子網路遮罩的位元數目。 我們的案例中，選取 *24 (256)*。
13. 按一下 [ **新增子網路** 新增新的子網路，如有需要。 在我們的案例中會新增子網路，然後重複步驟 10 到 12 設定 VNet，如下圖所示。

    ![虛擬網路位址空間頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. 按一下頁面右下角的核取記號按鈕建立 VNet。 幾秒鐘後，VNet 會顯示在可用 VNet 清單中，如下圖所示。

    ![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)
