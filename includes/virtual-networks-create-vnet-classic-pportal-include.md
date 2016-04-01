## 如何在 Azure Preview 入口網站中建立傳統 VNet

若要根據上述案例建立傳統 VNet，請依照下列步驟執行。

1. 從瀏覽器，瀏覽至 http://portal.azure.com，並有必要，請登入您的 Azure 帳戶。
2. 按一下 [ **新增** > **網路** > **虛擬網路**, ，請注意， **選取部署模型** 清單已顯示 **傳統**, ，然後按一下 [ **建立**, ，如下圖所示。

    ![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. 在 **虛擬網路** 刀鋒視窗中，輸入 **名稱** 的 VNet，然後再按一下 **位址空間**。 設定 VNet 和其第一個子網路的位址空間設定，然後按一下 [ **確定**。 下圖顯示我們案例的 CIDR 區塊設定。

    ![位址空間刀鋒視窗](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. 按一下 [ **資源群組** ，然後選取要新增的 VNet，或按一下 [資源群組 **建立新的資源群組** 將 VNet 新增至新的資源群組。 下圖顯示的資源群組設定為新的資源群組，稱為 **TestRG**。 如需資源群組的詳細資訊，請瀏覽 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

    ![建立資源群組刀鋒視窗](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. 如有必要，變更 **訂閱** 和 **位置** VNet 的設定。 

6. 如果您不想在磚中查看 VNet **儀表板**, ，停用 **釘選到開始面板**。 

7. 按一下 [ **建立** ，並注意名為的磚 **建立虛擬網路** 如下圖所示。

    ![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. 等候建立 VNet，然後在看到下方的磚之後，按一下以新增更多子網路。

    ![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. 您應該會看到 **組態** vnet，如下所示。 

    ![在入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. 按一下 [ **子網路** > **新增**, ，然後輸入 **名稱** ，並指定 **位址範圍 （CIDR 區塊）** 適用於您的子網路，然後按一下 **確定**。 下圖顯示我們目前案例的設定。

    ![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

