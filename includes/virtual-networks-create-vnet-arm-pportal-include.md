## 如何在 Azure Preview 入口網站中建立 VNet

若要透過 Azure Preview 入口網站，建立上述案例中的 VNet，請依照下列步驟執行。

1. 從瀏覽器，瀏覽至 http://portal.azure.com，並有必要，請登入您的 Azure 帳戶。
2. 按一下 [ **新增** > **網路** > **虛擬網路**, ，然後按一下 [ **資源管理員** 從 **選取部署模型** 清單，然後再按 **建立**, ，如下圖所示。

    ![在 Preview 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. 在 **建立的虛擬網路** 刀鋒視窗中，設定 VNet 設定，如下圖所示。

    ![建立虛擬網路的刀鋒視窗](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. 按一下 [ **資源群組** ，然後選取要新增的 VNet，或按一下 [資源群組 **新建** 將 VNet 新增至新的資源群組。 下圖顯示的資源群組設定為新的資源群組，稱為 **TestRG**。 如需資源群組的詳細資訊，請瀏覽 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

    ![資源群組](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 如有必要，變更 **訂閱** 和 **位置** VNet 的設定。 

6. 如果您不想在磚中查看 VNet **儀表板**, ，停用 **釘選到開始面板**。 

7. 按一下 [ **建立** ，並注意名為的磚 **建立虛擬網路** 如下圖所示。

    ![建立虛擬網路磚](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. 等候系統建立 vnet，然後在 **虛擬網路** 刀鋒視窗中，按一下 [ **所有設定** > **子網路** > **新增** ，如下所示。

    ![在 Preview 入口網站中新增子網路](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. 指定的子網路設定 *後端* 子網路，如下所示，然後再按一下 **確定**。 

    ![子網路設定](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. 請查看如下圖所示的子網路清單。

    ![VNet 中的子網路清單](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

